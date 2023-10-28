---
title: How to build a simple cryptocurrency blockchain using Node.js
date: 2023-07-17 20:00:00
categories:
  - Cryptocurrency
tags:
  - cryptocurrency
  - Defi
  - Node.js
  - node
  - 
description: Thecoin is an implementation of a cryptocurrency that we will build in this article.
---

## Building a simple cryptocurrency blockchain with Node.js

The blockchain is an open, digitized, repeating ledger of transactions. The history of each new transaction is recorded and stored in an encrypted way that is difficult to change or modify. Copies of this recorded information are sent across the blockchain network. Thus, making it highly secure.

Cryptocurrency is a digitally secure currency used in most of the current trade. The use of cryptography plays an important role in ensuring the security of cryptocurrencies.

This ensures that only genuine transactions are recorded and logged. Most cryptocurrencies use the decentralized principle of blockchain technology.

In this tutorial, we will take a closer look at blockchain and decentralization in some detail. We will also build a simple cryptocurrency system called thecoin .

Thecoin is an implementation of a cryptocurrency that we will build in this article.

### Precondition

In order to keep this tutorial running smoothly, you need to have a good understanding of the following.

- [JavaScript]
- [Node.js]
First, you must have.

- Node.js installed on your machine.
- A code editor.

### What is blockchain?

Bitcoin and Ether are digital cryptocurrencies powered and adopted by a powerful technology called blockchain. It uses cryptography to securely connect and maintain a growing list of records called blocks.

Blockchain, as the name suggests, is the growing blocks of transaction data that form a chain of transactions taking place. Valid transaction data is recorded into the blockchain network according to peer-to-peer rules set by the participants.

### Decentralization

Typically, data in a database is centralized. By centralizing, we operate based on only one server. The chances of risk are maximum due to the failure of the system. Also, decentralization allows data to be stored anywhere, thus making it faster, safer and a better way to store data.

Blockchain stores its information in several places. Whenever a new block is added to the blockchain, a copy is sent to all computers. This makes tampering with the blockchain very difficult because all the computers in the network must agree to the changes that are going to be made in order for the changes to be made.

By the end of this article, we will have a better understanding of blockchain and cryptocurrencies and how they work.

Let's get into the code. I will name my application thecoin .

Create an application called thecoin.js and open it in your code editor.

In the development folder, let's install the crypto library we're going to use using the command.

```shell
  npm install --save crypto-js
```

We will use this library to import the modules in our project.

I will start by creating a class BlockCypto , as shown below.

```js
const SHA256 = require('crypto-js/sha256');
class BlockCypto{
    constructor(index, current_time, info, nextHash=" "){
    this.index = index;
    this.current_time = current_time;
    this.info = info;
    this.nextHash = nextHash;
    this.hash = this.computeHash();     
    }
    computeHash(){
        return SHA256(this.info + this.nextHash + this.current_time + JSON.stringify(this.info)).toString();
    }   
}
```

I will explain each part of the code here.

I've created a class for my BlockCrytpo . Block and added a constructor, just like any other JavaScript class.

In the constructor, we initialize its properties and assign parameters to it as follows.

- crypto-js/sha256: This is the module we import to calculate the hash value for each block. We use the toString() method to convert it to a string as the module will return the object.

- index:This is a unique number that tracks the index of each block in the blockchain.

- current_time:As the name suggests, it keeps track of when each transaction was completed.

- info:All completed transaction data is recorded and stored by this method.

- nexthash:It points to the hash_key of the next block in the network chain. it is mainly used to keep and maintain the integrity of the blockchain.

- computeHash:Based on the properties passed to this method, it is used to compute the hash key of the next block in the chain.

### blockchain theorem (in calculus)

It is a type of database that stores a collection of data in groups with a certain storage capacity. These blocks are connected to the blocks that have been created and this forms a chain of data trees.

The chain is irreversible as the system is decentralized. Here, each block is assigned a timestamp when it is added to the chain.

Now, let's create a class Blockchain , which will maintain this operation.

```js
class Blockchain{
    constructor(){
        this.block1chain = [this.startGenesisBlock()];     
    }
    initGenesisBlock(){
        return new BlockCrypto(0, "06/04/2021", "Initial Block in the Chain", "0");
    }
    latestBlock(){
        return this.block1chain[this.block1chain.length - 1];
    }
    addNewBlock(newBlock){
        newBlock.nextHash = this.latestBlock().hash;
        newBlock.hash = newBlock.computeHash();        
        this.block1chain.push(newBlock);
    }
}
```

Let's understand the code snippet above.

As usual, we have our constructor which instantiates the block chain.

But this time, we pass it to the initGenesisBlock() method, which initializes the blocks in the chain. In our example, this property refers to an array of blocks.

- initGenesisBlock():This is the first block created in the peer-to-peer network and is not linked to any other block. According to our knowledge of indexing, its index is 0 .

> Note that we created it using the BlockCrypto class we created earlier and passed all the arguments as parameters.

- latestBlock:As named, we use it to find the last block added to the chain. As mentioned earlier, it helps to ensure the integrity of the chain by ensuring the hash value of the current block and mapping it to the hash value of the previous block.

- addNewBlock:Using this method, a new block is added to the chain. The previous hash block is matched to the current hash block to ensure minimal or no tampering in the chain.

Now, our blockchain is ready to work. We are missing something, which is the core principle of the blockchain, i.e. the integrity of the blockchain.

Let's see how we can validate it and test our application.

### Verify the integrity of the blockchain

The main feature of the blockchain is that once a block is added to the network, it cannot be changed without invalidating the integrity of the entire blockchain.

To enforce this, we use digital security or cryptographic hashing to secure and verify the blockchain by generating a new hash every time a change is made in the block.

We will loop through the entire blockchain and check if any of the hashes have been tampered with, taking into account the exception of the first block, which is hardcoded.

In addition, this method verifies that the encryption keys of every two blocks in series point to each other. It will return false if the integrity of the blockchain is compromised; otherwise, it will return true if no exceptions are encountered.

We will create this method in the Blockchain class.

```js
checkValidity(){
    // Checking validity
    for(let i = 1; i < this.block1chain.length; i++) {
        const currentBlock = this.block1chain[i];
        const nextBlock= this.blockchain[i-1];
    // Checking current blcok hash
    
    if(currentBlock.hash !== currentBlock.computeHash()) {
        return false;
    }
    // Comparing current block hash with the next block

    if(currentBlock.nextHash !== nextBlock.hash) {
        return false;
    }
    return true;
}

```

Now we can test our application and see the results.

But before we dive into running the code, let's create a new instance of the Blockchain class and name it thecoin , and add some blocks to the blockchain using random values.

```js
let thecoin = new Blockchain();

thecoin.addNewBlock(new BlockCrypto(1, "06/04/2021", {sender: "Rabin Yitzack", recipient: "Loyd Eve", quantity: 20}));

thecoin.addNewBlock(new BlockCrypto(2, "07/04/2021", {sender: "Anita Vyona", recipient: "Felix Mush", quantity: 349}));

console.log(JSON.stringify(thecoin, null, 4));
```

### Run our blockchain

Our terminal node thecoin.js , enter this command will result.

> Note: Before running this command, make sure to navigate to the correct path on your terminal.
> Tip: Use the command pwd to check the path.

## conclusions

You've already built your own cryptocurrency using Node.js. This step brings you one step closer to getting you started building professional applications using Node.js, or alternatively, you can just add more features to our simple blockchain and share it with the market.

Nonetheless, I hope this tutorial has provided you with the basic skill proficiency to move forward in exciting Node.js development!