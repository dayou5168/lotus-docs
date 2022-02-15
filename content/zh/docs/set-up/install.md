---
title: "安装"
description: "本指南内容涵盖如何安装 Lotus 应用程序和启动 Lotus 节点, 以及在你计算器上安装 lotus、lotus-miner 和 lotus-worker，然后运作Lotus 节点的设置。我们不会在本节中启动 Lotus miner。"
lead: "本指南内容涵盖如何安装 Lotus 应用程序和启动 Lotus 节点, 以及在你计算器上安装 lotus、lotus-miner 和 lotus-worker，然后运作Lotus 节点的设置。我们不会在本节中启动 Lotus miner。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 220
toc: true
---

## 云上运行

您可以在云计算提供商上运行 Lotus以替代本地运行，。最简单和最便宜的方法是使用 [DigitalOcean市场的一键式应用程序](https://marketplace.digitalocean.com/apps/filecoin-lotus) 。

在 [Running in the cloud]({{< relref "running-in-the-cloud" >}}) 中介绍了其他选项，包括 Amazon Web Services。



## 最低配置要求

要执行 Lotus node，您的计算器必须具有:

- 安装 macOS 或 Linux . 目前尚不支持Windows.
- 8 核心 CPU 和 32 GiB RAM。支持英特尔 _SHA扩展_ 的机型（自 Zen 微架构后的 AMD 或自 Ice Lake 后的英特尔）速度将显著加快。　
- 足够的空间来存储当前的 Lotus 区块链（最好用 SSD 存储）。该区块链以每天大约 38 GiB 的速度增加。该区块链可以从 [受信任的状态快照同步并压缩或修剪]({{< relref "chain-management" >}}) 到最小33Gib左右。 2021 年 6 月的完整记录约为 10TiB。

{{< alert icon="warning" >}}
这些是运用Lotus node的最低要求。[Filecoin存储供货商的硬件要求]({{< relref "../storage-providers/hardware-requirements" >}})不同。
{{< /alert >}}

## Linux

以下的说明适用于 Linux 系统的安装。如果您想在 Mac 上安装 Lotus，请前往 [macOS](#macos) 章节。

在 Linux 上安装 Lotus 有几种方法:

+ [Snap 软件包管理](#snap-package-manager)
+ [AppImages](#appimage)
+ [由源码构建](#building-from-source).

{{< alert icon="warning">}}**存储供货商应该从源码构建**。
从源码构建 Lotus 允许您精确的配置 Lotus 的运行方式以及它如何与其依赖软件通信。希望提高系统效率的存储供应商应该[从源码安装Lotus](#building-from-source).
{{< /alert >}}

### Snap 软件包管理

要使用 Snap 安装 Lotus，请执行:

```shell
snap install lotus-filecoin
```

您可以使用 `--edge` 标志安装每日构建版。这些建构是每晚从 master 分支 [Lotus GitHub repository](https://github.com/filecoin-project/lotus) 创建的。

```shell
snap install lotus-filecoin --edge
```

关于此 Snap 的更多资料可点击 [over at Snapcraft.io](https://snapcraft.io/lotus-filecoin).

### AppImage

[AppImages](https://appimage.org/) 是便携式的应用程序，让开发人员将软件和依赖项目打包到单个可执行文件中。 AppImages 可运行于基于linux的系统中。


1. 到最新版的 [GitHub仓库版本发布页面](https://github.com/filecoin-project/lotus/releases/tag/v1.13.0).
1. 在 **Assets**　下，下载 AppImage.
1. 打开终端窗口并移动到下载 AppImage 的位置。这个位置可能是你的**Downloads**文件夹 :

    ```shell
    cd ~/Downloads
    ```

1. 让 AppImage 可执行:

    ```shell
    chmod +x lotus_v1.13.0_linux-amd64.appimage
    ```

1. 现在可以双击或从终端窗口运行 AppImage 文件:

    ```shell
    ./lotus-v1.13.0_linx-amd64.appimage
    ```

### 由源码构建

您可以按照以下步骤从源码构建 Lotus 可执行文件。

#### 依赖软件

您需要安装以下软件才能安装和运行 Lotus。

#### 系统特定

构建 Lotus 需要一些系统依赖项，这通常由您的系统发行版提供。

Arch:

```shell
sudo pacman -Syu opencl-icd-loader gcc git bzr jq pkg-config opencl-icd-loader opencl-headers opencl-nvidia hwloc
```

Ubuntu/Debian:

```shell
sudo apt install mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential hwloc libhwloc-dev wget -y && sudo apt upgrade -y
```

Fedora:

```shell
sudo dnf -y install gcc make git bzr jq pkgconfig mesa-libOpenCL mesa-libOpenCL-devel opencl-headers ocl-icd ocl-icd-devel clang llvm wget hwloc hwloc-devel
```

OpenSUSE:

```shell
sudo zypper in gcc git jq make libOpenCL1 opencl-headers ocl-icd-devel clang llvm hwloc && sudo ln -s /usr/lib64/libOpenCL.so.1 /usr/lib64/libOpenCL.so
```

Amazon Linux 2:

```shell
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm; sudo yum install -y git gcc bzr jq pkgconfig clang llvm mesa-libGL-devel opencl-headers ocl-icd ocl-icd-devel hwloc-devel
```

#### Rustup

Lotus需要 [rustup](https://rustup.rs). 安装它最简单的方法是:

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

#### Go

要构建 Lotus，您需要安装 [Go 1.16.4 or higher](https://golang.org/dl/)或更新版本:

```shell
wget -c https://golang.org/dl/go1.16.4.linux-amd64.tar.gz -O - | sudo tar -xz -C /usr/local
```

{{< alert icon="tip">}}
您需要将 `/usr/local/go/bin` 添加到路径中。对于大多数 Linux 发行版，您可以执行以下:

```shell
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc && source ~/.bashrc
```

如果你卡住了可参考 [official Golang installation instructions](https://golang.org/doc/install).
{{< /alert >}}

#### 构建和安装 Lotus

一旦安装了所有依赖项，您就可以构建和安装 Lotus。

1. 复制代码仓库:

   ```shell
   git clone https://github.com/filecoin-project/lotus.git
   cd lotus/
   ```

1. 检查您要使用的网络的版本

   要加入mainnet，请查看最新版 [latest release](https://github.com/filecoin-project/lotus/releases).

   如果您正在更新先前已安装的 Lotus或进行了网络重置，请在继续进行前阅读 [切换网络向导]({{< relref "switch-networks" >}}).

   对于mainnet以外的网络，在[Filecoin网络仪表板](https://network.filecoin.io)上查找你想加入的网络的当前branch或者tag/commit. 然后用以下步骤构建你指定的网络.

   ```shell
   git checkout <tag_or_branch>
   # For example:
   git checkout <vX.X.X> # tag for a release
   ```

   目前，主分支master上的最新代码对应mainnet.

1. 如在中国, 请参考 "[Lotus: 在中国运行时的提示]({{< relref "nodes-in-china" >}})".
1. 根据您的 CPU 型号，您需要导出另外的环境变量:

    a. 如果您有 **AMD Zen 或 Intel Ice Lake CPU（或更新版本）**，请通过增加以下两个环境变量来启用 SHA扩展:

    ```shell
    export RUSTFLAGS="-C target-cpu=native -g"
    export FFI_BUILD_FROM_SOURCE=1
    ```

    更多此过程的信息请参阅 [Native Filecoin FFI section](#native-filecoin-ffi).

    a. 一些没有 ADX 指令支持的较旧 Intel 和 AMD 处理器可能会因非法指令错误而崩溃。要解决此问题，请添加`CGO_CFLAGS`环境变量:

    ```shell
    export CGO_CFLAGS_ALLOW="-D__BLST_PORTABLE__"
    export CGO_CFLAGS="-D__BLST_PORTABLE__"
    ```

    a. “multicore-sdr”的默认选项是在证明库中。除非明确禁用，否则此功能也用于 FFI。要禁止依赖项“multicore-sdr”的构建，请将`FFI_USE_MULTICORE_SDR` 为 `0`:

    ```shell
    export FFI_USE_MULTICORE_SDR=0
    ```

1. 构建及装置lotus

   Lotus 是编译为用在单一网络上，执行以下其中之一的指令之可在特定的 Lotus 网络上构建lotus 节点。

   ```shell
   # join mainnet
   make clean all

   # Or to join a testnet or devnet:
   make clean calibnet # Calibration with min 32GiB sectors

   sudo make install
   ```

   这会把 `lotus`, `lotus-miner` 及 `lotus-worker` 放在 `/usr/local/bin` 文件夹下。

   `lotus` 将默认使用 `$HOME/.lotus` 文件夹进行存储（配置、链数据、钱包）。有关如何量身定制 Lotus 文件夹请参阅 [高级选项]({{< relref "configuration" >}}) .

   安装完成后，用下面的指令确保Lotus成功的安装于正确的网络。

   ```shell
   lotus --version
   ```
   ```
   lotus version 1.13.0+calibnet+git.7a55e8e89
   ```

1. 您现在应该已经安装好 Lotus。现在可以 [启动 Lotus 守护程序](#start-the-lotus-daemon-and-sync-the-chain).

#### Filecoin FFI 源码

一些较新的 CPU 架构，像AMD 的 Zen 和英特尔的 Ice Lake都支持 SHA 扩展。启用这些 扩展可以显著加快 Lotus 节点的速度。要充分利用处理器的性能，请确保在　**从源代码构建之前** 设置以下变量:

```shell
export RUSTFLAGS="-C target-cpu=native -g"
export FFI_BUILD_FROM_SOURCE=1
```

这种构建方法不会产生可移值的二进制文件。确保在构建二进制文件的同一台机器上执行此文件。

#### Systemd服务管理文件

Lotus 提供通用的 Systemd 服务文件。它们可以通过以下方式安装:

```shell
make install-daemon-service
make install-miner-service
```

{{< alert icon="warning">}}
所提供的服务文件应根据用户需求进行 **检查和编辑** ，因为它们是非常通用的，可能缺少个别用户所需要的特定环境变量和设置。

一个例子是日志默认存到 `/var/log/lotus` 中，而在 `journalctl` 看不到。.
{{< /alert >}}

## MacOS

这些说明适用于 macOS。您可以在 macOS 10.11 El Capitan 或更高版本上安装 Lotus。如果您要在 Linux 发行版上安装 Lotus，请转到 [Linux section](#linux).

在 macOS 上安装 Lotus 有几种方法:

- [用 Homebrew 安装](#install-with-homebrew).
- [从源码构建](#build-from-source).

{{< alert icon="warning">}}**Miners should build from source.**
存储供货商应该从源码构建。从源码构建 Lotus 可给您严格设定 Lotus 的运行方式以及它如何与其依赖项进行沟通。希望提高系统效率的存储供应商应该[从源码构建安装](#build-from-source) .
{{< /alert >}}

### 用Homebrew安装

您可以在 macOS 上使用 Homebrew 快速安装 Lotus。

1. 添加filecoin-project/lotus 输入:

   ```shell
   brew tap filecoin-project/lotus
   ```

1. 安装 Lotus:

    ```shell
    brew install lotus
    ```

1. 您现在应该已经安装了 Lotus。现在可以 [启动 Lotus 守护程序](#start-the-lotus-daemon-and-sync-the-chain).

### 从源码构建

您可以按照以下步骤从源码构建Lotus 可执行文件。

#### 软件依赖

您必须安装 XCode 和 Homebrew 才能从源码构建Lotus。

##### XCode 命令行工具

Lotus 要求在构建Lotus 二进制文件之前先安装 X-Code CLI 工具。

1. 检查您是否已经由 CLI 安装了 XCode 命令行工具，执行:

    ```shell
    xcode-select -p
    ```
    ```
    /Library/Developer/CommandLineTools
    ```

    如果用这个指令会返回一个路径，那么你已经安装了 Xcode！您可以 [继续使用 Homebrew 安装相关项目](#homebrew).

   {{< alert icon="warning">}}
   如果上述命令没有返回路径，请安装 Xcode:

   ```shell
   xcode-select --install
   ```
   {{< /alert >}}

接下来是使用 Homebrew 安装 Lotus 的相关依赖。

##### Homebrew

我们建议 macOS 用户使用 [Homebrew](https://brew.sh) 来安装每个必要的组件。

1. 使用命令 brew install 安装以下组件:

   ```shell
   brew install go bzr jq pkg-config rustup hwloc
   ```

接下来是复制 Lotus 存储库并构建可执行文件。

#### 构建和安装 Lotus

此安装说明会因 Mac 中的 CPU 而异:

- [搭载M1的ＣPUs](#m1-based-cpus)
- [搭载Intel及ＡＭＤ的ＣPUs](#intel-and-amd-based-cpus)

##### 搭载 M1 的 ＣPUs

{{< alert icon="warning">}}
这些说明用于在搭载 M1 的 Mac 上安装 Lotus。如果您有搭载 Intel 或 AMD 的 CPU，请使用搭载 [Intel 和 AMD 的 CPU 说明 ↓](#intel-and-amd-based-cpus)
{{< /alert >}}

1. 复制仓库:

   ```shell
   git clone https://github.com/filecoin-project/lotus.git
   cd lotus/
   ```

1. 执行 `git checkout <RELEASE TAG>` 以切换最新的 Lotus 版本:

    ```shell
    git checkout v1.13.1
    ```

    您可以使用 [Lotus GitHub 版本发行页面上](https://github.com/filecoin-project/lotus/releases)列出的任何标签切换至特定版。

    {{< alert icon="tip">}}
    如果要切换主网以外的网络，请查看 [切换网络指南 →]({{< relref "switch-networks" >}})
    {{< /alert >}}

1. 创建必要的环境变量以允许 Lotus 在 ARM 架构上运行:

    ```shell
    export LIBRARY_PATH=/opt/homebrew/lib
    export FFI_BUILD_FROM_SOURCE=1
    ```

1. 构建 `lotus` 守护程序:

    ```shell
    make all
    ```

1. 执行最后的 `make` 指令将这个 `lotus` 可执行文件移到 `/usr/local/bin`。这允许您从任何目录执行lotus。

    ```shell
    sudo make install
    ```

1.  您现在应该已经安装了 Lotus。现在可以 [启动 Lotus 守护程序](#start-the-lotus-daemon-and-sync-the-chain).

##### 搭载 Intel 及 ＡＭＤ 的 ＣPUs

{{< alert icon="warning">}}
这些说明用于在搭载 Intel 或 AMD 的 Mac 上安装 Lotus。如果您有搭载 M1 的 CPU请参阅 [搭载M1的ＣPUs的说明 ↑](#m1-based-cpus)
{{< /alert >}}

1. 复制仓库:

   ```shell
   git clone https://github.com/filecoin-project/lotus.git
   cd lotus/
   ```

1. 执行 `git checkout <RELEASE TAG>` 以切换最新的 Lotus 版本:

    ```shell
    git checkout v1.13.1
    ```

    您可以使用 [Lotus GitHub 版本发行页面上](https://github.com/filecoin-project/lotus/releases)列出的任何标签切换至特定版。

    {{< alert icon="tip">}}
    如果要切换主网以外的网络，请查看 [切换网络指南 →]({{< relref "switch-networks" >}})
    {{< /alert >}}

1. 如果你在中国，看看 [在中国运行时的提示]({{< relref "nodes-in-china" >}})".
1. 一些不支持 ADX 指令的旧款 Intel 和 AMD 处理器可能会出现非法指令错误。要解决此问题，请加 `CGO_CFLAGS` 环境变量:

   ```shell
   export CGO_CFLAGS_ALLOW="-D__BLST_PORTABLE__"
   export CGO_CFLAGS="-D__BLST_PORTABLE__"
   ```

   这是由 Lotus 错误致使 Lotus 无法在缺少 adx 指令的处理器上运作, 应尽快修复。

1. 构建和安装 Lotus:

   ```shell
   make clean && make all
   sudo make install
   ```

1. 您现在应该已经安装了 Lotus。您现在可以 [启动 Lotus 守护程序](#start-the-lotus-daemon-and-sync-the-chain).

## 启动 Lotus 守护程是并与区块链同步

`lotus` 做为守护程序和客户端运行并与自身的守护程序进行管理及互动。守护程序通常在后台持续运行。

使用mainnet时，我们建议从[从可信状态快照同步]({{< relref "chain-management#lightweight-snapshot" >}}). 在任何情况下，您都可以使用以下指令启动守护程序d:

```shell
lotus daemon
```

在第一次执行时，Lotus 将:

- 设置其数据文件夹于 `~/.lotus`。
- 下载必要的证明参数。这几 GB 数据是一次性下载的。
- 导入快照（如有指定）并开始同步 Lotus 区块链。

守护程序将立即产生大量日志数据。从这刻，您将需在新终端上工作。您现在所执行的任何lotus 指令将与正在运行的守护程序互动。

{{< alert icon="tip">}}
不要担心日志中显示的警告和有时错误讯息的数量。它们是守护程序因正常参与了全球分布式共识网络一部分。
{{< /alert >}}

如果您使用了快照，则后续守护程序可以正常的启动，无任何选项:

```shell
lotus daemon
## When running with systemd do:
# systemctl start lotus-daemon
```

有关同步和快照的更多信息，[请参阅链管理部分]({{< relref "chain-management" >}}).

我们建议等待同步过程完成，这在使用可信状态快照导入时应该相对较快:

```shell
lotus sync wait
```

## 与守护程序互动

`lotus` 指令允许您与正在 _运行_ 中的 Lotus 守护程序互动。

`lotus-miner` 和 `lotus-worker` 指令的运作方式相同.

```shell
lotus
  - chain: Interact with filecoin blockchain
  - client: Make deals, store data, retrieve data
  - wallet: Manage wallet
  - net: Manage P2P Network
  - sync: Inspect or interact with the chain syncer
  ...

# Show general help
lotus --help
# Show help for the "client" to make deals, store data, retrieve data
lotus client --help
```

例如，在 Lotus 守护程序运行几分钟后，可用 lotus sync 检查您的 lotus 节点的同步状态。

```shell
lotus net sync
```
```
sync status:
...
	Target:	[bafy2bzaceaki6bjhe2lxmtyexcff6vh5y5uw4tmbjr3gatwvh5dhaqqb2ykaa] (320791)
	Stage: complete
	Height: 320791
...
```

或者使用 lotus net 来检查它连接的在 Filecoin 网络中其他节点的数量。

```shell
lotus net peers
```
```
12D3KooWSDqWSDNZtpJae15FBGpJLeLmyabUfZmWDWEjqEGtUF8N, [/ip4/58.144.221.27/tcp/33425]
12D3KooWRTQoDUhWVZH9z5u9XmaFDvFw14YkcW7dSBFJ8CuzDHnu, [/ip4/67.212.85.202/tcp/10906]
```

或者查验您的 Lotus 节点和网络目前的版本。

```shell
lotus version
```
```
Daemon:  1.13.0+calibnet+git.7a55e8e89+api1.4.0
Local: lotus version 1.13.0+calibnet+git.7a55e8e89
# running lotus v1.13.0 on Calibration testnet
```

## 停止 Lotus 守护程序

要优雅地停止正在运行的 lotus 守护程序（重新启动守护程序以更新 Lotus 时需要），请使用以下指令:

```shell
lotus daemon stop
## When running with systemd do:
# systemctl stop lotus-daemon
```
