---
title: React Testing
date: 2021-01-13 13:46:32
tags: [React]
categories: 攻城狮那些事儿
---

## 描述

#### Tools

- [Jest](https://facebook.github.io/jest/)
- [React Testing Library](https://testing-library.com/react)

#### 总步骤

1. 通过`render`等渲染函数，渲染一个组件
2. `Queries`等查询、获取组件
3. `Expect`断言渲染结果、状态值

<!--more-->

#### 一些行为

- 对模块、函数、时间进行Mock
- `beforeEach`等函数对环境进行设置、清除
- `act`等函数等待渲染完成，以便对确定完成的结果进行断言
- `fireEvent`发送时间，如点击事件

## 参考

1. [https://reactjs.org/docs/testing-recipes.html](https://reactjs.org/docs/testing-recipes.html)
2. [https://testing-library.com/](https://testing-library.com/)
