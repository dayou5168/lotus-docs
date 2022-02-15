---
title: "切换网络"
description: "本指引将教您如何根据您的测试或开发需求用 Lotus在各种 Filecoin 网络之间切换。"
lead: "本指引将教您如何根据您的测试或开发需求用 Lotus在各种 Filecoin 网络之间切换。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 280
toc: true
---
我们在 [安装指引]({{< relref "install" >}})， 中提到Lotus 是编译在单个网络上运作，配置文件夹中的信息对应于该网络。

+ 本地 devnet - [你可以运行一个本地devnet](https://docs.filecoin.io/build/local-devnet/#manual-set-up)
+ 测试网
  + [Calibnet](https://network.filecoin.io/#calibration)
  + [NerpaNet](https://github.com/filecoin-project/community/discussions/74#discussioncomment-1348469) NerpaNet已于 2021-08-16 弃用，不再可用。
+ [Mainnet](https://network.filecoin.io/#mainnet)

您可以选择以下之一的方法切换到不同的网络。

## 清理、重新编译、重新安装

第一种方法最简单。这方法时您要删除之前运行的网络相关的所有数据并启动一个专为在新网络上运行而构建的 Lotus 二进制文件：

1. 关闭正在运行的Lotus 守护程序。
1. 删除 `~/.lotus` 文件夹，或您 `$LOTUS_PATH`设定的任何值。默认值为 `~/.lotus`。
1. 复制Lotus仓库并进入`lotus` 目录：

    ```shell
    git clone https://github.com/filecoin-project/lotus
    cd lotus
    ```

1. 使用 `make clean ...` 构建 `lotus` 可执行文件以指定想要去加入的网络:

    | 网络 | 编译命令 | 描述 |
    | --- | --- | --- |
    | Mainnet | `make clean all` | Filecoin 营运网络。 FIL 在这个网络上具有实际价值 |
    | Calibnet | `make clean calibnet` | 最小扇区大小为 32 GiB 的测试网络。 FIL 在这个网络上没有实际价值。 |

2. 再次启动 Lotus 守护程序，让它与新网络同步:

    ```shell
    lotus daemon
    ```

{{< alert icon="warning">}}**当运行于不同的 `$LOTUS_PATH` 环境变量值时**
此过程会把原来网络中所有内容删除，包括钱包。如果您在 `mainnet` 上并要切换到 `calibnet` 但您希望在切换回来时保持所有 `mainnet` 内的数据完好无损，那请在执行 `lotus daemon` 守护程序前更改`$LOTUS_PATH` :

要更改 `$LOTUS_PATH` 执行: `export LOTUS_PATH=~/.new-lotus-path`.
{{< /alert >}}

## 备份 Lotus 数据

如果您希望备份 Lotus 数据，请将 `~/.lotus` (或 `$LOTUS_PATH`) 文件夹复制到某处。如果 Lotus 节点与整个网络同步，这将很费时。

另一选择是 [导出您的钱包]({{< relref "manage-fil#exporting-and-importing-addresses" >}}) 及 [链数据]({{< relref "chain-management" >}})， 以便稍后重新导入到新安装的 Lotus 节点上。
