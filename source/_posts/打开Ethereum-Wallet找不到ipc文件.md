---
title: 打开Ethereum Wallet找不到ipc文件
date: 2018-04-24 20:54:43
tags: [Blockchain]
categories: 攻城狮那些事儿
---

## 背景介绍
在启动Ethereum Wallet之前，不管是通过鼠标双击还是通过命令行启动，如果没有启动节点客户端geth的话，则在启动Ethereum Wallet的时候会默认启动一个geth，该默认geth的datadir在macOS上为\$HOME/Library/Ethereum，geth启动的时候会生成一个ipc文件，路径在对应的datadir下，因为Ethereum Wallet通过rpc方式和geth通信。<!--more-->
那么问题来了，如果启动Ethereum Wallet前，手动启动了一个geth，这时候启动Ethereum Wallet的话将不会再启一个geth，并且会去\$HOME/Library/Ethereum下找一个ipc文件，那么如果你手动启动的geth使用"--datadir"指定了一个路径，并且该路径不是\$HOME/Library/Ethereum，Ethereum Wallet将找不到节点，没法做同步，日子中或控制台将提示\$HOME/Library/Ethereum下找不到ipc文件。

## 解决方法
网上有不同的解决方法，下面的是博主在macOS上试过可用的。
这里假设手动启动geth时，通过"--datadir"指定的路径是\$HOME/Library/Ethereum/ethprivate1，则首先进入Ethereum Wallet的安装目录：
`cd /Applications/Ethereum Wallet.app/Contents/MacOS`
然后通过命令启动Ethereum Wallet，并且通过"--rpc"指定ipc文件的路径：
`./Ethereum\ Wallet --rpc $HOME/Library/Ethereum/ethprivate1/geth.ipc`

**And the end, good luck to you!**

