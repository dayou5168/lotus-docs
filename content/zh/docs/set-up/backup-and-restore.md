---
title: 备份和恢复节点
description: "您可以备份您的 Lotus 节点。如果有问题可以从此备份恢复，或者您只是希望将 Lotus 节点从一台计算机移动到另一台计算机。"
lead: "您可以备份您的 Lotus 节点。如果有问题可以从此备份恢复，或者您只是希望将 Lotus 节点从一台计算机移动到另一台计算机。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 292
toc: true
---

这个备份过程与 [`lotus-miner` 备份]({{< relref "../storage-providers/backup-and-restore" >}})不同。此备份过程不保留任何存储信息。

## 备份

1. 如果您的 `lotus` 守护程序正在运行，请停止它。
1. 执行 `lotus daemon backup`:

    ```shell
    lotus backup --offline ~/lotus-backup.cbor
    ```

    ```shell output
    2021-09-24T20:21:03.986Z        INFO    backupds        backupds/datastore.go:75Starting datastore backup
    2021-09-24T20:21:03.987Z        INFO    backupds        backupds/datastore.go:130
    Datastore backup done
    ```

1. 您的 Lotus 守护程序数据现已备份到 `lotus-backup.cbor`.

## 恢复

1. 如果您的 `lotus` 守护程序已经在运作，请停止它。
1. 执行`lotus daemon` 守护程序备份并使用 `--restore` 选项 :

    ```shell
    lotus daemon 2>&1 --restore=offline-backup.cbor
    ```

    ```shell output
    2021-09-24T20:24:51.729Z        INFO    main    lotus/daemon.go:218     lotus repo: /root/.lotus
    2021-09-24T20:24:51.730Z        INFO    paramfetch      go-paramfetch@v0.0.2/paramfetch.go:191  Parameter file /var/tmp/filecoin-proof-parameters/v28-proof-of-spacetime-fallback-merkletree-poseidon_hasher-8-0-0-7d739b8cf60f1b0709eeebee7730e297683552e4b69cab6984ec0285663c5781.vk is ok
    ...
    2021-09-24T20:24:52.296Z        INFO    badger  v2@v2.2007.2/value.go:1178      Replay took: 2.975µs
    2021-09-24T20:24:52.297Z        INFO    backupds        backupds/log.go:125     opening log     {"file": "/root/.lotus/kvlog/metadata/1632514590.log.cbor"}
    ```

1. `lotus daemon` 守护程序应会继续从您备份的区块高度开始运作。
