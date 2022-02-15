---
title: "在云端运行"
description: "在云提供商上运作 Lotus 是一种快速启用的方式。使用这些镜像，您可以在不到半小时的时间内启动并运作一个完全同步的节点而无需在本地机器上编译 Lotus。"
lead: "在云提供商上运作 Lotus 是一种快速启用的方式。使用这些镜像，您可以在不到半小时的时间内启动并运作一个完全同步的节点而无需在本地机器上编译 Lotus。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 293
toc: true
---

## 云服务商

### DigitalOcean

最简单及最便宜的途径是使用 [在DigitalOcean市场上的一键应用程序](https://marketplace.digitalocean.com/apps/filecoin-lotus).

部署后，使用您在创建预安装 `root`  的“Droplet”（虚拟机）时提供的密码以 root 身份登录你新的 DigitalOcean Droplet:

```shell
ssh root@<your_droplet_public_ipv4>
```

#### 使用 DigitalOcean API 部署 Lotus

您还可以通过编程方式启动 Lotus 节点。例如，要在 SFO2 区域创建一个 4 GB 的 filecoin-lotus Droplet，您可以使用以下 curl 指命:

```shell
curl -X POST -H 'Content-Type: application/json' \
-H 'Authorization: Bearer '$TOKEN'' \
-d '{"name":"a_name","region":"sfo2","size":"s-2vcpu-4gb",
"image":"protocollabs-filecoinlotus-20-04"}'  "https://api.digitalocean.com/v2/droplets"
```

### 亚马逊网络服务

我们定期为每个 [Filecoin 网络](https://network.filecoin.io/)发布 AMI。 要使用这其中一映像，只需搜索我们的 AMI 之一。 You can use this [example link which populates a search for Lotus in the `us-west-2` region](https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#Images:visibility=public-images;search=lotus-mainnet;ownerAlias=657871693752;sort=name).

启动 AWS 实例后，您应该使用“ubuntu”账户登录。 Filecoin 实例运作基于主机的防火墙 (UFW)，它会阻止除端口 22 和 5678 (libp2p) 之外的所有传入端口。如果您想将实例置于许可安全组之后这很有用。

```shell
ssh ubuntu@<your_instance_public_ipv4>
```

## 使用镜像

实例一启动，就会下载一个包含最近 2000个state root的大文件。 在此期间，lotus API不会启动，但 lotus _在_ 运行。 大约20分钟后，stateroot下载完成，lotus正常启动。这个过程是自动发生的。您无需为 Lotus 启动做任何操作。

如果你有兴趣看看发生了什么，你可以在systemd日志中查看lotus的日志。

```shell
journalctl -u lotus-daemon
```

在下载 stateroot 文件时，systemctl将显示此任务的状态为"`Activating`".

您会注意到程序尚未启动。不用担心，在导入 stateroot 后它会转换为活动状态。

```shell
[mainnet ~] ⨎ systemctl status lotus-daemon

● lotus-daemon.service - Lotus Daemon
   Loaded: loaded (/lib/systemd/system/lotus-daemon.service; disabled; vendor preset: enabled)
   Active: activating (start-pre) since Sat 2021-04-03 01:36:03 UTC; 7s ago
Cntrl PID: 1088 (bash)
    Tasks: 11 (limit: 9513)
   Memory: 600.8M
   CGroup: /system.slice/lotus-daemon.service
           ├─1088 bash /usr/local/bin/lotus-init.sh
           └─1101 lotus daemon --import-snapshot https://fil-chain-snapshots-fallback.s3.amazonaws>
```

大约 20 分钟后，stateroot 快照将完成下载，lotus 将正常启动

{{< alert icon="warning" >}}
stateroot 下载仅在您的实例最初启动时发生一次。未来可能发生的任何重启都不会再次下载 stateroot，因此启动速度会快得多。
{{< /alert >}}

现在stateroot已经下载成功就可以和lotus网络进行互动了。

```shell
[mainnet ~] ⨎ lotus net peers
<see current peers here>
```

