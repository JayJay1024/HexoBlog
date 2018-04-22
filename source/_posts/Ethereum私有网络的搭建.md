---
title: Ethereum私有网络的搭建
date: 2018-04-22 21:46:44
tags: [Blockchain]
categories: 攻城狮那些事儿
---

## 以太坊网络
以太坊节点网络分为公网、测试网络、私有网络。私有网络是自己搭建的测试网络，类似于一个局域网。以下将通过在macOS上搭建一个私有网络作为示范。

<!--more-->

## 搭建步骤
#### 安装节点客户端
首先默认macOS上已经安装了geth客户端
#### 创建创世区块
新建一个json文件并命名为genesis.json，然后输入如下内容：
```
{
  "config": {
    "chainId": 15,
    "homesteadBlock": 0,
    "eip155Block": 0,
    "eip158Block": 0
  },
  "difficulty": "200000000",
  "gasLimit": "2100000",
  "alloc":
  {
    "7df9a875a174b3bc565e6424a0050ebc1b2d1d82": { "balance": "300000" },
    "f41c74c9ae680c1aa78f42e5647a62f353b7bdde": { "balance": "400000" }
  }
}
```
#### 初始化节点
`geth --datadir "./" init genesis.json`
#### 启动节点
`geth --datadir "./" --networkid 15 --rpc console --identity "ep1"`
这样子一个节点就创建并启动了，如果需要创建多个节点，则分别新建目录，并把上面的json文件拷贝过去（同一私有网络创世区块要相同），在每个目录里面执行初始化命令。启动多个节点的时候则需要区分端口号，否则会冲突，但是网络号需要一样，比如在另外两个终端中：
`geth --datadir "./" --networkid 15 --rpc console --port 30304 --rpcport 8546 --identity "ep2"`
`geth --datadir "./" --networkid 15 --rpc console --port 30305 --rpcport 8547 --identity "ep3"`
如此下来，就启动了3个节点，分别是ep1、ep2、ep3。
#### 节点互连
节点启动的时候，可能只是一个独立的节点，其他节点没有被发现，这时候可以手动添加。首先需要知道节点信息，比如在上面的ep2节点终端中输入 `admin.nodeInfo.enode`获得该节点的enode信息。
接着切到ep1终端，通过 `admin.addPeer(刚才那串enode信息)`添加节点，添加完后，执行 `admin.peers`就可以发现ep1已经连接到ep2节点了