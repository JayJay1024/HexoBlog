---
title: Setting Up A Local Environment Of EOS.IO
date: 2018-05-03 09:34:53
tags: [Blockchain]
categories: 攻城狮那些事儿
---

eos.io的本地环境的搭建主要是参考eos.io在GitHub的[Wiki](https://github.com/EOSIO/eos/wiki/Local-Environment)，同时记录一下搭建过程中遇到的坑。<!--more-->

## Getting the Code
首先需要获得eos.io的source code，通过以下的命令clone到本地：
`git clone https://github.com/EOSIO/eos --recursive`
命令行的*--recursive*这个flag确保克隆eos仓库和它的子模块，如果clone时没有使用这个flag，则通过下面的命令也可以取得子模块：
`git submodule update --init --recursive`

## Building EOSIO
#### Automated build script
最简单的方法是使用eos的自动编译脚本，自动编译脚本支持以下的系统：
```
1. Amazon 2017.09 and higher.
2. Centos 7.
3. Fedora 25 and higher (Fedora 27 recommended).
4. Mint 18.
5. Ubuntu 16.04 (Ubuntu 16.10 recommended).
6. MacOS Darwin 10.12 and higher (MacOS 10.13.x recommended).
```
#### System Requirements (all platforms)
同时，所有的操作平台，需要满足：
```
1. 8GB的可用内存
2. 20GB的可用磁盘
```
如果内存不满足，脚本将会给出提示并且终止当前编译。
#### Run the build script
`cd eos`
`./eosio_build.sh`
脚本会check系统的环境，如果某些依赖不存在，脚本会提示进行安装。
如下是博主在macOS Darwin 10.13.4上运行脚本后出现的依赖不存在情况：
```
Checking dependencies.
Checking automake ... 		 automake NOT found.
Checking Libtool ... 		 Libtool NOT found.
Checking OpenSSL ... 		 OpenSSL NOT found.
Checking llvm ... 		 llvm NOT found.
Checking wget ... 		 wget NOT found.
Checking CMake ... 		 CMake NOT found.
Checking GMP ... 		 GMP NOT found.
Checking gettext ... 		 gettext NOT found.
Checking MongoDB ... 		 MongoDB NOT found.
Checking Doxygen ... 		 Doxygen NOT found.
Checking Graphviz ... 		 Graphviz NOT found.
Checking LCOV ... 		 LCOV NOT found.
Checking Python3 ... 		 python3 NOT found.

The following dependencies are required to install EOSIO.

1. automake
2. Libtool
3. OpenSSL
4. llvm
5. wget
6. CMake
7. GMP
8. gettext
9. MongoDB
10. Doxygen
11. Graphviz
12. LCOV
13. Python 3


Do you wish to install these packages?
1) Yes
2) No
#?
```
build seccess将会出现以下画面：
```
	 _______  _______  _______ _________ _______
	(  ____ \(  ___  )(  ____ \\__   __/(  ___  )
	| (    \/| (   ) || (    \/   ) (   | (   ) |
	| (__    | |   | || (_____    | |   | |   | |
	|  __)   | |   | |(_____  )   | |   | |   | |
	| (      | |   | |      ) |   | |   | |   | |
	| (____/\| (___) |/\____) |___) (___| (___) |
	(_______/(_______)\_______)\_______/(_______)

	EOS.IO has been successfully built. 1:6:15

	To verify your installation run the following commands:

	/usr/local/bin/mongod -f /usr/local/etc/mongod.conf &
	cd /Users/lauyearh/Blockchain/EOS/eos/build; make test

	For more information:
	EOS.IO website: https://eos.io
	EOS.IO Telegram channel @ https://t.me/EOSProject
	EOS.IO resources: https://eos.io/resources/
	EOS.IO wiki: https://github.com/EOSIO/eos/wiki
```
#### Build validation
可以通过一组测试用例，验证你的编译。
如果是在Linux平台：
`~/opt/mongodb/bin/mongod -f ~/opt/mongodb/mongod.conf &`
如果是在macOS:
`/usr/local/bin/mongod -f /usr/local/etc/mongod.conf &`
然后执行以下命令，不管在Linux还是macOS：
`cd build`
`make test`
最后出现这样的提示说明测试通过：
```
100% tests passed, 0 tests failed out of 33

Total Test time (real) = 806.70 sec
```
#### Install the executables
通过安装可执行档案，可以在系统中方便操作eos，相当于把cmd写到PATH变量中，但是其实是把内容安装到/usr/local：
`cd build`
`sudo make install`
## Creating and Launching a Single Node Testnet
运行下面的命令，将启动一个简单的节点：
`cd build/programs/nodeos`
`./nodeos -e -p eosio --plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::account_history_api_plugin`
这个时候，默认的config路径，在macOS上是*\$HOME/Library/Application Support/eosio/nodeos/config*，里面包含*config.ini*和*genesis.json*两个文件。当然，你也可以在启动nodeos时通过*\-\-config\-dir*指定*config*路径，并且需要建立ini和json两个文件。
## 总结
博主build时用的是macOS，同时系统配置满足要求，使用自动编译脚本的时候没遇到什么坑，顺利build通过。Source code是*master* branch，commit id是*19227c9b0a095683c1df3668c110cef2d91cbb15*。
## 参考
- [https://github.com/EOSIO/eos/wiki/Local-Environment](https://github.com/EOSIO/eos/wiki/Local-Environment)
