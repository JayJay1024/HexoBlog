---
title: nodeos与config.ini
date: 2018-05-08 16:15:11
tags: [Blockchain]
categories: 攻城狮那些事儿
---

可能我们会通过这样的命令启动eos节点：
```
$ nodeos -e -p eosio --plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::account_history_api_plugin
```
<!--more-->
如果不想每次启动都输入这么多参数，则可以通过配置对应的config.ini文件：
- "-e"对应的修改config.ini文件中
```
# Enable block production, even if the chain is stale. (eosio::producer_plugin)
enable-stale-production = true
```
- "-p eosio"对应
```
# ID of producer controlled by this node (e.g. inita; may specify multiple times) (eosio::producer_plugin)
producer-name = eosio
```
- "--plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::account_history_api_plugin"对应
```
# Plugin(s) to enable, may be specified multiple times
plugin = eosio::producer_plugin
plugin = eosio::wallet_api_plugin
plugin = eosio::chain_api_plugin
plugin = eosio::account_history_api_plugin
plugin = eosio::http_plugin
```

配置好config文件，以后启动节点只需要nodeos命令就好了，不需要参数：
```
$ nodeos
```
