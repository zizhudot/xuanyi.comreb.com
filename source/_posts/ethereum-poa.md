---
title: Ether PoA consensus method out of the block
date: 2023-07-17 20:00:00
categories:
  - Cryptocurrency
tags:
  - cryptocurrency
  - Defi
  - ethereum
  - PoA 
description: The Ethernet POW method of block out is a workload method of block out
---

The Ethernet POW method of block out is a workload method of block out. Blocks will also be out when there is no transaction, and for smart contract users, empty fast will waste a lot of disk space.
POA is Proof of Asset, which is realized by adding the following parameters in the Genesis block configuration file.

You can modify the period "period": 0, so as to realize that when there is no transaction, no block will be released, thus saving disk space.

```json
 "clique": {
      "period": 0,  // The unit is second. The default value is 15 seconds. If the change value is 0, no block will be produced when there is no transaction
      "epoch": 30000 // The unit is the number of blocks. The default value is 30,000 blocks.
    }
```

```js
"extraData": "0x000000000000000000000000000000000000000000000000000000000000000004819FcA652AD35F9cD688aAAfa53aD61DDA21990000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",      //Here set block account 64 bits + account +65*2 bits;
```

Complete Ethereum po a consensus mode blocks complete configuration, configuration file genesis.json

```js
{

    "config": {
    "chainId": 456719,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "clique": {
      "period": 0,  // The unit is second. The default value is 15 seconds. If the change value is 0, no block will be produced when there is no transaction
      "epoch": 30000 // The unit is the number of blocks. The default value is 30,000 blocks.
    }
  },
  "nonce": "0x0000000000000042",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "difficulty": "0x00100000",
  "coinbase": "0x3333333333333333333333333333333333333333",
  "timestamp": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "extraData": "0x000000000000000000000000000000000000000000000000000000000000000004819FcA652AD35F9cD688aAAfa53aD61DDA21990000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x8000000",
  "alloc": {
    "0x04819FcA652AD35F9cD688aAAfa53aD61DDA2199": {
      "balance": "99999999999999999999"
    },
    "0x59fE0911B06B1Fe0136744D383502da57c0c1229": {
      "balance": "99999999999999999999"
    }
  }
}

```

verify

```php
> eth.sendTransaction({ from:eth.coinbase, to:"0x59fE0911B06B1Fe0136744D383502da57c0c1229",value: 1});
"0x9fdde975c63cde9a2b741022a488b50ce1897c024476281b6461f3ea488f4750"

> eth.getTransactionReceipt("0x9fdde975c63cde9a2b741022a488b50ce1897c024476281b6461f3ea488f4750");
{
  blockHash: "0xc35d93dd73dac56075539fb87cee8f5b6c55ff3465cada34468dcd5891cd1a08",
  **blockNumber: 1,   ## This is the block number **
  contractAddress: null,
  cumulativeGasUsed: 21000,
  effectiveGasPrice: 1000000000,
  from: "0x04819fca652ad35f9cd688aaafa53ad61dda2199",
  gasUsed: 21000,
  logs: [],
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  status: "0x1",
  to: "0x59fe0911b06b1fe0136744d383502da57c0c1229",
  transactionHash: "0x9fdde975c63cde9a2b741022a488b50ce1897c024476281b6461f3ea488f4750",
  transactionIndex: 0,
  type: "0x0"
}
```
