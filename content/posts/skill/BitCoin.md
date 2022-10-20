---
title: "Bitcoin Intro"
date: "2019-09-03"
description: "Reading summary on bitcoin"
categories:
    - "Bitcoin"
    - "Blockchain"
---

# why we need bitcoin

Today I read Satoshi *Bitcoin:A peer-to-peer Electronic Cash System*. It is a very interesting paper and whole Bitcoin system is based on this paper. Although I have some basic understanding of bitcoin before, reading this paper really help me to have deeper knowledge of it.

During our daily money transaction , we need bank to provide trust between two people. The bitcoin system replace the middle man and use cryptograghic proof to solve trust problem. The advantage is that we could transfer money at will, without a third party. 

# how it works

The key element is hashing. If A wanted to pay a bitcoin to B, A needs to use his private key to sign on a hash, which is the combination of B's public key and last transaction. A could use his public key to verify this transaction. In other words, only giver of bitcoin could verify and sign in the new transaction block. Therefore receiver could verify the previous owner. However, how receiver knows whether sender has used this coin in other place. In other words, how could we know whether this coin is not used by this man previously. The answer is we could build a single history for all nodes in the network. Therefore we could check whether it is spent by last owner before or not. 

Thus, we could put many transaction items in one block and hash it with timestamp and publish it with timestamp server. The next hash is followed by this hash, which is called as blockchain. It is like a consensus algorithm, like raft. When we have the same history, we will agree with the future. It also makes people hard to change any history.


# proof-of-work and network

It is also important to implement a proof-of-work system. Why we need this? In the blockchain network, how to decide which node attach the new block on the chain? It is the one who finish the work in the first place. Proof-of-work can be viewed as a mathmatics problem, which is computationally difficult to solve. We could add a nonce(like a integer) on a block. Then we could hash the block. The nonce value is unknown, but we need to get the block hash value to a specific amount of zero defined by rule. We could only try different nonce value one by one to meet requirements. The first node could solve the problem will have the chance to add new block on the chain. 

When a node solve the proof-of-work, it will broadcast the block to all nodes. Other nodes will accept the block only when all transcations are valid and they are not spent twice. When they accept the value, they will use new hash to do proof-of-work.

Node will only work on the longest branch.

# Incentive

I think this is very important in this network, and this is what I am not sure how to implement blockchain in other field. In bitcoin network, we could get new bitcoins when we find the new block by spending lots of electricity and CPU resources. Nodes could also have transcation fee when they add new block on the chain. Therefore, it encourages node to do proof-of-work and be honest. 

However, in other implementation of blockchain. I could not see the incentive for other people in the network. If one company wanted to use blockchain to trace its product from production to sales, how could they reward nodes in the network. Blockchain is a P2P network, and if it is owned by one company, it is not a decentralized technology. Additionally, proof-of-work is also important in the blockchain because it makes sure no one could change the history. Therefore, it will be costy for a company to use such large network. If it didn't use proof-of-work, it is meaningfulless for blockchain network.


# disk space optimization 
Another concern for me is how to save space when we need to know all the history. One way is to use tree structure to save all transaction and only root will be hashed. If the past transaction could be buried, we will discard the branch to save disk space.

# how to implement it 

http://adilmoujahid.com/posts/2018/03/intro-blockchain-bitcoin-python/?utm_source=wanqu.co&utm_campaign=Wanqu+Daily&utm_medium=email