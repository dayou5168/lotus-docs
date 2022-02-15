---
title: 轻节点
description: "Lotus 轻节点是一个精简版能在低端硬件上运行的 Lotus 全节点。 Lotus 轻节点不包含任何区块链数据，只能执行信息签名和交易传输。但它们启动起来非常快，并且可以与其他 Lotus 轻节点并行处理事务。"
lead: "Lotus 轻节点是一个精简版能在低端硬件上运行的 Lotus 全节点。 Lotus 轻节点不包含任何区块链数据，只能执行信息签名和交易传输。但它们启动起来非常快，并且可以与其他 Lotus 轻节点并行处理事务。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 230
toc: true
---

在开始之前，我们先介绍将在本指南中使用的术语:

| 术语 | 叙述 |
| --- | --- |
| Lotus | 主要用于实现Filecoin，用 Golang 编写。 |
| Full-node | 一个 Lotus 节点，含 Filecoin 网络的所有区块链数据。 |
| Lite-node | 一个 Lotus 节点，不含 Filecoin 网络的任何区块链数据。 Lite 节点需跟全节点一起使用才能运作。|

## 先决条件

要启动 Lotus lite 节点，您需要:

1. 一个[Lotus全节点]({{< relref "install" >}})。为了取得最佳效果，请确保此节点是完全同步。
2. 用一台至少 2GB RAM 和双核 CPU 的计算机作为 Lotus 轻节点。这可以是您的本地计算机, 它必须安装 Rust 及 Go 1.16.4 或更高版本。
3. 您必须有所需的[所有软件依赖项]({{< relref "install#software-dependencies" >}}) 以构建Lotus。

## 全节点的准备

对有权限使用的全节点，你需要对其配置进行一些小修改。

{{< alert icon="tip">}}
如您使用的是协议实验室 `api.chain.love` 的Lotus 全节点，则无需完成此段。 协议实验室 Lotus 全节点已设置成接受所有传入请求，因此无需创建任何 API 密钥。

如果您使用的是 [Glif](https://www.glif.io/) 或 [Infura](https://infura.io/) 等节点服务，则可能需要通过网站创建 API 密钥。
{{< /alert >}}

1. 在您的全节点打开 `~/.lotus/config` 并:

    a. 取消第 3行的注释.
    a. 更改 `127.0.0.1` 为 `0.0.0.0`.

    ```toml
    ListenAddress = "/ip4/0.0.0.0/tcp/1234/http"
    ```

    保存并退出.

1. 创建一个 API 令牌给你的轻节点使用:

    ```shell
    # lotus auth create-token --perm <read,write,sign,admin>
    lotus auth create-token --perm write
    ```

    您选择的权限取决于您的用例。查看 [API 令牌部分 →]({{< relref "../developers/api-access#obtaining-tokens" >}})以近一步了解

1. 将此 API 令牌发送到您的 轻节点或任何轻节点管理员。
1. 如果您正在执行 `lotus daemon` ，请将其停止并重新启动。这会使 Lotus 开启我们刚刚设置的 API 端口。

接下来, 轻节点上创建 Lotus 可执行文件并在 _lite模式_ 下运作！

## 创建可执行文件

您需要创建 Lotus 可执行文件以运作轻节点。此流程与创建全节点时相同。

### 搭载AMD 及Intel 的计算机

1. 在要运做 轻节点的计算机上复制 [Lotus GitHub仓库](https://github.com/filecoin-project/lotus)

    ```shell
    git clone https://github.com/filecoin-project/lotus
    cd lotus
    ```

1. 创建可执行文件，但什么都不要执行:

    ```shell
    make clean all
    sudo make install
    ```

    如果有错误, 可能是因为您没有安装所有的 Lotus 依赖项目。可快速浏览一下 [Lotus 入门指南]({{< relref "install#software-dependencies" >}}) 并仔细检查是否已安装所有依赖项目，以及 Golang 和 Rust。

1. 转向[启动轻节点 ](#start-the-lite-node).

### 搭载 Ｍ1 的 Macs

由于搭载 M1 的 Mac 计算机的新颖架构，在创建 `lotus` 可执行文件之前必须设置一些特定的环境变量。

1. 从 GitHub 复制  [Lotus 仓库](https://github.com/filecoin-project/lotus) :

    ```shell
    git clone https://github.com/filecoin-project/lotus
    cd lotus
    ```

1. 拉入子模块:

    ```shell
    git submodule update --init --recursive
    ```

1. 创建必要的环境变量让Lotus 在 ARM 上运行:

    ```shell
    export GOARCH=arm64
    export CGO_ENABLED=1
    export LIBRARY_PATH=/opt/homebrew/lib
    export FFI_BUILD_FROM_SOURCE=1
    ```

1. 进入 `extern/filecoin-ffi` 目录并切换到 `m1-portable` 分支:

    ```shell
    cd extern/filecoin-ffi
    git fetch -a
    git checkout master
    ```

1. 创建filecoin-ffi 可执行文件:

    ```shell
    make clean
    make
    ```

1. 移到 Lotus 根目录并创建 lotus 守护程序:

    ```shell
    cd ../../
    make lotus
    ```

1. 转到启动轻节点 [启动轻节点](#start-the-lite-node).

## 启动轻节点

准备好 Lotus 可执行文件，并且拥有Lotus 全节点访问权限。只剩下将轻节点连接到全节点！

1. 在轻节点创建一个环境变量，调用 `FULLNODE_API_INFO` 并赋予以下值并同时调用 `lotus daemon --lite`。确认将 `API_TOKEN` 替换为从全节点获得的token，并将 `YOUR_FULL_NODE_IP_ADDRESS` 替换为您的全节点的 IP 地址:

    ```shell with-output
    FULLNODE_API_INFO=API_TOKEN:/ip4/YOUR_FULL_NODE_IP_ADDRESS/tcp/1234 lotus daemon --lite
    ```
    ```
    2021-03-02T23:59:50.609Z        INFO    main    lotus/daemon.go:201     lotus repo: /root/.lotus
    ...
    ```

    API_TOKEN 变量后面必须有一个冒号 (`:`).

    如果你没有 API_TOKEN，你可以在没有 API_TOKEN 的情况下执行上面的指令，并获得只读访问全节点的权限:

    ```shell
    FULLNODE_API_INFO=/ip4/YOUR_FULL_NODE_IP_ADDRESS/tcp/1234 lotus daemon --lite
    ```

1. 现在你可与轻节点互动:

    ```shell with-output
    lotus wallet balance f10...
    ```
    ```
    100 FIL
    ```

轻节点的功能有限仅设计为执行消息签名和交易操作。 轻节点不能直接密封数据或查询区块链。所有区块链需求都得通过附加的全节点。如果由于某种原因全节点离线，任何与其连结的轻节点也将离线。

### 访问和权限

在不使用 [来自全节点的 API令牌]({{< relref "../developers/api-access/" >}}) 的情况下设置轻节点会导致轻节点对全节点只具读取权限。虽然大多数时间只读权限应该没问题，但在某些情况下，也需要对全节点有写的权限。

## 用例

Lotus 轻结点可以执行交易性的功能, 例如创建交易、提议交易、消息签名等。它们本身没有任何区块链数据，完全依赖全节点的数据。 轻节点本身完全没用。

一个用例是在一分钟内需要签名多条信息的服务，例如交易所。在这种情况下，此服务可以有多个轻节点专门用于消息签名和处理交易的计算，而由单个全节点维护区块链数据。

另一种情况是一个组织有很多数据要存储在 Filecoin 上，但没有资源来运作全节点。他们可以创建一个轻节点来与他们信任的全节点创建交易，然后一旦找到存储供应商，轻节点就可以将数据传输给存储供应商。

## 优点和缺点

因轻节点不需要跟任何区块链数据同步，因此它们能够快速启动。由于它们的硬件要求极低，因此可以用非常小的空间来启动多个轻节点。一个项目可以根据需要添加或删除轻节点来横向扩展或缩减其处理能力。

然而轻节点有一些重大的缺点。虽然轻节点可以在不用全节点的情况下执行某些功能，但所有需要区块链数据的事务和进程都必须通过 Lotus 全节点。
