---
title: HomeBrew
date: 2019-02-02 22:18:27
tags: [macOS,HomeBrew]
---

## HomeBrew：
macOS下的`homebrew`相当于Linux下的`apt-get`，即包管理器

## 包卸载：
这里以卸载 `boost` 为例：
```
$: brew unlink boost
$: brew unpin boost
$: brew uninstall boost
```

<!--more-->

## 包升级：
这里以升级 `mongodb` 为例
```
$: brew upgrade mongodb
```
