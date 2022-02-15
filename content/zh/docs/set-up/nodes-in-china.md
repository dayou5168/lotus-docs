---
title: "在中国的节点"
description: "本指引为中国用户提供了一些技巧以解决他们在构建和运行 Lotus 时遇到的一些带宽或速度缓慢的问题。"
lead: "本指引为中国用户提供了一些技巧以解决他们在构建和运行 Lotus 时遇到的一些带宽或速度缓慢的问题。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 294
toc: true
---

## 加快下载首次启动的证明参数

运行 Lotus 需要下载区块链的 _证明参数_ 这些参数的大文件默认托管在中国境外，而在中国下载非常慢。为了解决这个问题，用户应该在运行 `lotus`, `lotus-miner` 和 `lotus-worker`时设置以下环境变量:

```sh
export IPFS_GATEWAY=https://proof-parameters.s3.cn-south-1.jdcloud-oss.com/ipfs/
```

## 在构建期间加快 Go 模块下载

构建 Lotus 需要下载一些 Go 模块。这些通常托管在 Github 上，从中国进来的带宽非常低。要解决此问题，请在运行 Lotus  **之前* 使用本地代理设置以下变量:

```sh
export GOPROXY=https://goproxy.cn
```
