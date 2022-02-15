---
title: "故障诊断"
description: "此页通过列出Lotus 节点用户可能遇到的一些最常见错误并提供了一些故障诊断建议。"
lead: "此页通过列出Lotus 节点用户可能遇到的一些最常见错误并提供了一些故障诊断建议。"
draft: false
menu:
    docs:
        parent: "node-set-up"
        identifier: "node-troubleshooting"
weight: 295
toc: true
---

{{< alert icon="callout">}}
**您是否成功克服了其他与 Lotus Node 相关的问题？** 请通过使用底部的链接对其进行编辑以对此页面做出贡献！
{{< /alert >}}

## 编译错误

请仔细检查构建日志。如果您的 git 分支显示为dirty state，请执行以下操作:

```sh
git checkout <desired_branch>
git reset origin/<desired_branch> --hard
make clean
```

(或直接删除并再次复制仓库，如 [安装说明]({{< relref "install" >}} 中所示。)

## 从中国构建/启动缓慢

参考 [在中国运行时的提示]({{< relref "nodes-in-china" >}}) 指引.

## Error: initializing node error: cbor input had wrong number of fields

当您启动为一个网络编译的 Lotus，但遇到 Lotus 数据文件夹中的数据用于不同的网络或较旧的不兼容版本时，就会发生这种情况。

解决方法：严格按照 [切换网络的说明]({{< relref "switch-networks" >}}).

## Error: Failed to connect bootstrap peer

```sh
WARN  peermgr peermgr/peermgr.go:131  failed to connect to bootstrap peer: failed to dial : all dials failed
  * [/ip4/147.75.80.17/tcp/1347] failed to negotiate security protocol: connected to wrong peer
```

再次尝试执行构建步骤并确保您拥有来自 GitHub 的最新代码。

## Error: repo is already locked

您已经有另一个 Lotus 守护程序在运行。先停止它 (`lotus daemon stop`).

## Config: Open files limit

Lotus 将尝试自动设置文件描述符 (FD) 限制。如果不行，您仍然可以将系统配置为允许比默认值高。具体说明取决于您的系统以及您运作 Lotus 的方式（是否使用 systemd 等）。请 [使用搜索引擎查找说明](https://duckduckgo.com/?q=increase+open+files+limit&t=ffab&ia=web) 因为有很多非常好的指引。

## Error: Routing: not found

```
WARN  main  lotus/main.go:72  routing: not found
```

此错误意味着您尝试与之交互的存储供应商离线。

## RPC Error: request bigger than maximum

```
ERROR	rpc	go-jsonrpc/server.go:90	RPC Error: request bigger than maximum 104857600 allowed
```

基于安全原因，设有最大请求量，以防 RPC 服务器暴露于外部请求。默认值为 100 MiB，但可以使用启动服务器的相应 CLI 命令的 `api-max-req-size` 参数进行调整。如果指令没有 CLI 参数来调整，请提交一个[issue](https://github.com/filecoin-project/lotus/issues/new?assignees=&labels=area/api&template=bug_report.md&title=Missing%20api-max-req-size%20option%20in%20Lotus%20command) 请求处理。.

## Signal killed

如果您看到 `signal killed` 错误，则可能是在构建过程中出现错误。

```shell
/usr/local/go/pkg/tool/linux_amd64/link: signal: killed
make: *** [Makefile:68: lotus] Error 1
```

仔细检查您的计算机是否满足 Lotus 的 [最低硬件要求]({{< relref "install#minimal-requirements" >}}) 。

## Go command not found

你可能会遇到一个错误，说没有找到 `go` 指令:

```shell with-output
sudo make install
```
```
bash: go: command not found
expr: syntax error: unexpected argument '14'
install -C ./lotus /usr/local/bin/lotus
install -C ./lotus-miner /usr/local/bin/lotus-miner
install -C ./lotus-worker /usr/local/bin/lotus-worker
...
```

您可以在 `sudo make install` 步骤中忽略此错误；它不影响安装。

## chain linked to block marked previously as bad

如果 Lotus 跟随一个坏分叉, 这问题可能会出现。解决办法是:

- 取消标记所有坏块
- 将区块头重置为已知良好的区块高度

```sh
lotus sync unmark-bad --all
lotus chain sethead --epoch <epochNumber>
```
