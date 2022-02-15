---
title: "关于Lotus"
description: "Lotus 是由创建 IPFS、libp2p 和 Filecoin 的 Protocol Labs 编写的Filecoin实现程序."
lead: "Lotus 是由创建 IPFS、libp2p 和 Filecoin 的 Protocol Labs 编写的Filecoin实现程序."
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 210
toc: true
---

它是一套用 [Go](https://golang.org) 编写的命令行应用程序:

- Lotus 节点 (`lotus`): Filecoin 节点：验证网络交易，管理 FIL 钱包，可以执行存储和检索交易。
- Lotus存储供应商 (`lotus-miner`): Filecoin 存储供应商。请参阅 [Storage provider]({{< relref "overview" >}})文档中相应的 Lotus Miner章节.
- Lotus Worker (`lotus-worker`): 协助存储供应者执行奖励相关任务的工作节点。请参阅其各自的 [指引]({{< relref "seal-workers" >}}) 。

安装说明三个都适用，但本节只专注在 **Lotus Node** 的入门：安装、启动、同步链和管理 Lotus 钱包。有关如何使用 Lotus 进行存储交易的文档在[store section]({{< relref "store-data" >}}) ,补充文檔也可供存储供应者和开发人员使用！

{{< alert icon="warning" >}}
Lotus 是一个 **仅适用于 Linux 和 MacOS** 的命令行应用程序，需要 **从由源代码构建**。用户应该熟悉命令行应用程序的运作。
{{< /alert >}}

## Lotus 入门

这些是快速开始使用 Lotus 的主要指引:

- [安装并启动 Lotus 节点]({{< relref "install" >}})
- [创建一个钱包并向您的地址发送或接收 FIL]({{< relref "manage-fil" >}})
- [在不同网络之间切换]({{< relref "switch-networks" >}})
- [了解 Lotus 配置]({{< relref "configuration" >}})

请查看侧边菜单栏以取得更多指引！
