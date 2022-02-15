---
title: "链数据管理"
description: "Lotus 区块链有计算 Filecoin 网络当前状态所需的信息。本指引解释如何管理链数据的几个方面，包括如何通过从快照去载入区块链来减少与节点同步的时间。"
lead: "Lotus 区块链有计算 Filecoin 网络当前状态所需的信息。本指引解释如何管理链数据的几个方面，包括如何通过从快照去载入区块链来减少与节点同步的时间。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 290
toc: true
---

## 同步

Lotus 自动同步到最新的 _区块链头_ ，方法是获取从 _当前链头_ 到最后一个同步纪元的区块头。然后该节点会检索并验证从最后一个同步纪元到当前链头的所有区块。一旦 Lotus 同步了，它就会收到每个纪元的新区块并验证它们。每个纪元可能会有不同数量的区块。

Filecoin 的区块链成长相对快, 要完全同步需要很长的时间。Lotus使用可信状态快照提供更快的同步。有两种快照:

| 名称                                 | 结束高度    | 消息初始高度        | 状态开始高度          |
| ------------------------------------ | ------------- | --------------------------- | --------------------------- |
| [Lightweight](#lightweight-snapshot) | 当前区块 | 当前区块 - 2000 区块 | 当前区块 - 2000 区块 |
| [Full chain](#full-chain-snapshot)   | 当前区块 | 创世区块               | 当前区块 - 2000 区块 |

### 轻量级快照

我们建议多数用户从轻量级快照执行节点初始同步。这些快照不含区块链的完整状态所以并不适合需要对节点历史状态执行查询的状况，例如区块浏览器。但是，它们比全区块链快照小得多，对于大多数用例应该足够了。

{{< alert icon="warning" >}}
这些轻量级状态快照 **不包含任何消息回执**。 要获取消息回执，您需要从创世区块同步您的 Lotus 节点，而不使用任何这些轻量级快照。
{{< /alert >}}

1. 下载最新的轻量级快照及其校验和:

    a. 对于**mainnet**，此命令总是包含主网的最新快照。:

    ```shell
    curl -sI https://fil-chain-snapshots-fallback.s3.amazonaws.com/mainnet/minimal_finality_stateroots_latest.car | perl -ne '/x-amz-website-redirect-location:\s(.+)\.car/ && print "$1.sha256sum\n$1.car"' | xargs wget
    ```

    a. 对于 **testnet**, 使用 [最新的 calibration 网络快照](https://www.mediafire.com/file/q7tc2bmcc9d09vv/lotus_cali_snapshot_2021_07_14_high_73770.car.tar.xz/file). Testnet快照是由Filecoin社区自愿维护的，可能不是最新的。在使用之前请仔细检查。

    ```shell
    curl -sI https://www.mediafire.com/file/q7tc2bmcc9d09vv/lotus_cali_snapshot_2021_07_14_high_73770.car.tar.xz/file
    ```

1. 查看已下载快照的 `sha256sum` :

    ```shell with-output
    # Replace the filenames for both `.sha256sum` and `.car` files based on the snapshot you downloaded.
    echo "$(cut -c 1-64 minimal_finality_stateroots_517061_2021-02-20_11-00-00.sha256sum) minimal_finality_stateroots_517061_2021-02-20_11-00-00.car" | sha256sum --check
    ```

    此命令将输出如下内容:

    ```shell
    minimal_finality_stateroots_517061_2021-02-20_11-00-00.car: OK
    ```

1. 使用 `--import-snapshot`选项启动 Lotus 守护程序:

    ```shell
    # Replace the filename for the `.car` file based on the snapshot you downloaded.
    lotus daemon --import-snapshot minimal_finality_stateroots_517061_2021-02-20_11-00-00.car
    ```

{{< alert icon="tip" >}}
我们强烈建议您在导入之前下载并验证快照的校验和。但您也可以跳过  `sha256sum` 检查并直接使用快照 URL:

```shell
lotus daemon --import-snapshot https://fil-chain-snapshots-fallback.s3.amazonaws.com/mainnet/minimal_finality_stateroots_latest.car
```
{{< /alert >}}

### 全区块链快照

全区块链快照包含从创世到当前tipset的每个区块。您可以用`--import-chain` 选项在不确定的导入这些完整快照时重新计算整个状态:

```shell
lotus daemon --import-chain https://fil-chain-snapshots-fallback.s3.amazonaws.com/mainnet/complete_chain_with_finality_stateroots_latest.car
```

由于 Filecoin 区块链的规模及复杂性，此操作将需要数天时间。

### 检验同步状态

有两种方法可以检验 Lotus 守护程序的区块链同步进度。

#### 同步状态

使用 `sync status` 导出本地区块链的当前状态:

```shell
lotus sync status
```

此命令将输出以下内容:

```shell
sync status:
worker 0:
        Base:   [bafy2bzacecnamqgqmifpluoeldx7zzglxcljo6oja4vrmtj7432rphldpdmm2]
        Target: [bafy2bzaceb4b3ionbbxz4uqoehzkjlt4ayta7bneh2bh5xatnwypeuqypebmw bafy2bzaceb2uct4pawanule5bt2ivepcgqls6e6f52lccofvdyfynyfnsa3aa bafy2bzacealylayv2mpgx7wkf54diu6vqmw5yubdgkauii7q2fb7hvwk4343i] (414300)
        Height diff:    414300
        Stage: header sync
        Height: 414300
        Elapsed: 765.267091ms
```

#### 等待同步

使用 `sync wait` 导出当前于正在进行过程中的区块链状态:

```shell
lotus sync wait
```

此命令将输出:

```shell
Worker: 0; Base: 0; Target: 414300 (diff: 414300)
State: header sync; Current Epoch: 410769; Todo: 3531
Validated 0 messages (0 per second)
...
```

使用 `chain getblock` 来检查最后一个同步块的存储时间:

```shell
date -d @$(./lotus chain getblock $(./lotus chain head) | jq .Timestamp)
```

此指令将输出:

```shell
Mon 24 Aug 2020 06:00:00 PM EDT
```

## 创建快照

可以使用 `chain export` 创建完整的区块链 CAR 快照:

```shell
lotus chain export <filename>
```

要备份一定数量的最新状态根，请使用 `--recent-stateroots` 选项，以及您要备份的状态根数量:

```shell
lotus chain export --recent-stateroots=2000 <filename>
```

要创建 _裁减过_ 的快照并仅包含由导出的状态根直接引用的区块，请添加 `skip-old-msgs` 选项:

```shell
lotus chain export --recent-stateroots=2000 --skip-old-msgs <filename>
```

## 恢复定制的快照

您可以使用 `--import-snapshot` 选项启动守护程序来恢复快照:

```shell
# Without verification
lotus daemon --import-snapshot <filename>

# With verification
lotus daemon --import-chain <filename>
```

如果您不想在快照完成后启动守护程序，请加 `--halt-after-import` 标志:

```shell
lotus daemon --import-snapshot --halt-after-import <filename>
```

## 压缩链数据

可以通过最小快照重新同步来 _裁减_ Lotus 当前的区块链数据以减少节点的磁盘占用空间。

1. 导​​出区块链数据:

    ```shell
    lotus chain export --recent-stateroots=901 --skip-old-msgs my-snapshot.car
    ```

1. 停止 Lotus 守护程序:

    ```shell
    lotus daemon stop
    ```

1. 备份链数据并创建链数据目录:

    ```shell
    mv ~/.lotus/datastore/chain ~/.lotus/datastore/chain_backup
    mkdir ~/.lotus/datastore/chain
    ```

1. 导​​入区块链数据:

    ```shell
    lotus daemon --import-snapshot my-snapshot.car --halt-after-import
    ```

1. 开启 Lotus 守护程序:

    ```shell
    lotus daemon
    ```

1. 打开另一个 ssh 连接或终端以检查同步状态 :

    ```shell
    lotus sync status
    lotus sync wait
    ```

1. 就这样了!
