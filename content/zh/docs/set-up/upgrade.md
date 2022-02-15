---
title: "升级版本"
description: "本节将说明如何安全地将 Lotus 节点升级到新版本。"
lead: "本节将说明如何安全地将 Lotus 节点升级到新版本。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 250
toc: true
---

## 安装更新

通常，如果您要更新 Lotus，只需在提取所选分支和仓库的最新状态后重建和重新安装软件即可。你可以这样做:

```shell
git pull
git checkout <branch or tag>
```

切换至新版本后，按照 [安装指引]({{< relref "install" >}})中的说明重新编译并重新安装。

您可以通过以下方式验证最新版本：

```shell
lotus --version # for the lotus binary
lotus version   # for the currently running daemon
```

{{< alert icon="callout">}}
**安装好更新版后，您需要停止并再次启动守护程序**。
{{< /alert >}}

## 对照检查你的配置文件

所有的升级都需要你仔细检查你的配置文件是否是最新的。你可以通过从Lotus导出默认的配置文件，并将其与你的配置文件进行比较来达到这一目的。

要从Lotus导出默认配置文件，请运行:

```shell
lotus config default
```

这将输出类似于以下的内容:

```toml
[API]
  # Binding address for the Lotus API
  #
  # type: string
  # env var: LOTUS_API_LISTENADDRESS
  #ListenAddress = "/ip4/127.0.0.1/tcp/1234/http"

...
```

如果你想让Lotus将默认配置导出到一个文件，请运行:

```shell
lotus config default >> ~/default-lotus-client-configuration.toml
```

一旦你有了默认的配置文件，将它与你的配置文件进行比较，确保你没有遗漏任何部分。关于增加了哪些新的部分，请看 [Lotus的版本发布说明](https://github.com/filecoin-project/lotus/releases)。

## 切换网络

如果您想切换网络，请阅读 [切换网络指引]({{< relref "switch-networks" >}}).

