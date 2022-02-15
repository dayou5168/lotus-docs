---
title: "多重签名"
description: "多重签名（multisig）钱包是指需要多个密钥授权以进行`FIL`交易的钱包。"
lead: "多重签名（multisig）钱包是指需要多个密钥授权以进行`FIL`交易的钱包。"
draft: false
menu:
    docs:
        parent: "node-set-up"
weight: 270
toc: true
---

## 创建多重签名钱包

使用 `lotus msig create`  创建多重签名钱包:

```shell with-output
lotus msig create signerAddress1 signerAddress2 signerAddress3...
```
```
Created new multisig:  f01002 f24mscgjtgymb3dqtm4ycwydh4nhygktgxm3nbgva
```

以上的例子中， `f01002` 是 id 地址，`f24mscgjtgymb3dqtm4ycwydh4nhygktgxm3nbgva` 是actor地址。两个地址都代表新创建的多重签名钱包。

默认情况下，批准交易需要所有签名者的签名是预设的。但您可以使用`--required`选项更改所需批准的数量:

```shell
lotus msig create --required=2 signerAddress1 signerAddress2 signerAddress3
```

以上的例子创建了一个有三个签名者的多重签名钱包，但只需两个签名者批准即可执行交易。

## 提议并批准交易

多重签名钱包的任何签名者都可以 _提议_ 交易。_提议者_ 自动批准提议的交易。仅当收到所需的批准数量时才会执行交易。可以[检查多重签名钱包](#inspect-a-multisig-wallet) 以得知所需的批准数量。如多重签名钱包只需要一个签名者，那么交易将在其提议后立即执行。

使用 `lotus msig propose` 提出交易：

````shell with-output
lotus msig propose --from=proposerAddress walletAddress destinationAddress value
````
````
send proposal in message:  bafy2bzaceajm2mghc5rludlbcr3bnpqgcz5m6gmldq6ycrc4trkejz36tnrqe
Transaction ID: 0
````

上例中 `bafy2bzaceajm2mghc5rludlbcr3bnpqgcz5m6gmldq6ycrc4trkejz36tnrqe` 是其 `messageID`, 而 `0` 是其 `transactionID`.

其他签名者可以使用 `lotus msig approve` 来批准消息:

```shell
lotus msig approve walletAddress transactionID proposerAddress destinationAddress value
```

`transactionID`, `proposerAddress`, `destinationAddress` 和 `value` 的值必须与提议中使用的值匹配。

## 取消一个待处理的多签提议

使用 `lotus msig cancel` 去取消一个待处理的多签交易。

```shell
lotus msig cancel walletAddress transactionID destinationAddress value
```

`walletAddress`, `transactionID`, `destinationAddress` 和 `value` 的值必须与提议中使用的值匹配。

成功取消过程中的输出。

```shell
sent cancel in message:  bafy2bzacebjy2limeu6mw4b6x5yqgdupxaqabprojwu72xlfhwkhgb5jcyr7c
```
## 检查多重签名钱包

使用 `lotus msig inspect` 获取有关多重签名钱包的信息:

```shell with-output
lotus msig inspect walletaddress
```
```
Balance: 0 FIL
Spendable: 0 FIL
Threshold: 2 / 3 # number of signature required / number of signers the wallet has
Signers:
ID      Address
t01001  t1ai2gcr2xlpyxcbjlegojbpr3ovdyfdyzigjoyza
t0100   t3r4d3avth4agwxy6ko35reeuydcqaa6cq4mt6owg3zjq23pxqc6xvn7scb43dyhaf2cjnjhtioek6innbpgda
t01003  t3rpukrggza4jjt6vpihiqoekth6tiopzhvxbp36qhrzfu4xpk6n3mxo5geh6bdavkkkhqk7owt2an2wrundtq
Transactions:  1
ID      State    Approvals  To                                         Value   Method   Params
0       pending  1          t1fjswymsauvfh5zxw34t2pgz7iev2fn56unyw6ci  20 FIL  Send(0)
```

## 检查多重签名提议

使用 `lotus-shed msg` 去检查消息参数.

```shell
lotus-shed msg messageID
```

对一个多重签名消息进行成功检查的输出。

```plaintext
---
Message Details:
Value: 0 FIL
Max Fees: 0.00000031548776754 FIL
Max Total Cost: 0.00000031548776754 FIL
Method: Propose
Params: {
  "To": "t1m2oqtdwx2kporl3dgq4schl46jnu63fqwchf4wq",
  "Value": "5000000000000000000",
  "Method": 0,
  "Params": null
}
---
Params message:
Msig Propose:
HEX: 845501669d098ed7d29ee8af633439211d7cf25b4f6cb049004563918244f400000040
B64: hFUBZp0JjtfSnuivYzQ5IR188ltPbLBJAEVjkYJE9AAAAEA=
JSON: {
  "To": "t1m2oqtdwx2kporl3dgq4schl46jnu63fqwchf4wq",
  "Value": "5000000000000000000",
  "Method": 0,
  "Params": null
}

---
```
