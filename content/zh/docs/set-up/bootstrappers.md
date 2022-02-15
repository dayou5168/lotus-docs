---
title: 网络引导节点
description: "Lotus 守护程序可以配置为网络引导节点。引导节点可作为其他 lotus 守护程序寻找对等节点的初始联系点。"
lead: "Lotus 守护程序可以配置为网络引导节点。引导节点可作为其他 lotus 守护程序寻找对等节点的初始联系点。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 291
toc: true
---

{{< alert icon="tip" >}}
除非您正在运作私人测试网络，否则通常不需要使用网络引导节点。
{{< /alert >}}

### 配置引导节点

由于引导节点处理的流量与典型的 lotus 守护程序略有不同，我们建议使用以下配置。此配置将设置节点作为引导节点。

```toml
  [API]
    ListenAddress = "/ip4/0.0.0.0/tcp/1234/http"
  [Libp2p]
    ListenAddresses = ["/ip4/0.0.0.0/tcp/1347"]
    ConnMgrLow = 400
    ConnMgrHigh = 500
    ConnMgrGrace = "5m0s"
    Bootstrapper = true
  [Pubsub]
    Bootstrapper = true

```

### 启动引导节点

使用引导节点配置文件去启动 lotus 守护程序 `--profile=bootstrapper` 覆盖配置默认值以启用引导节点模式子系统libp2p 和 pubsub。
如果您的守护程序配置文件有包含 如上例所示 `Bootstrapper = true` ，则不需用此选项。

```bash
lotus daemon --profile=bootstrapper
```

### 操作引导节点
引导节点将自动使用现有引导节点。如果您正在操作自己的基础设施，您可能需要手动将引导节点互连。

```bash
lotus net peers
lotus net connect <peer_multiaddr>
```

### 配置 lotus 守护程序以连接到引导节点

Lotus 引导节点是在编译时内置的。您可以在 [此处](https://github.com/filecoin-project/lotus/tree/master/build/bootstrap) 找到每个网络的公共引导节点列表. 可以加以下到您的守护程序配置文件以覆盖此列表。


```
[Libp2p]
    BootstrapPeers = [
      "<multiaddr>"
    ]
```
