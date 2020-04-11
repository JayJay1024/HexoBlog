---
title: react+typescript+jest踩坑
date: 2020-04-11 21:08:16
tags:
categories: 攻城狮那些事儿
---

## 使用Typescript和一些配置

#### package.json

`babelConfig` 用于告诉 ts-jest 是否使用 babel 。false不使用，true会查找 babel 配置文件，如 babel.config.json ，也可以配置某个具体的值。

`identity-obj-proxy`用于jest mock css module

tsx 使用 babel-jest transform 遇到无法解析，后来改为使用 ts-jest 能解决问题

<!--more-->

```json
{
  "scripts": {
    "dev": "webpack-dev-server --open",
    "test": "jest --no-cache"
  },
  "jest": {
    "globals": {
      "ts-jest": {
        "babelConfig": true
      }
    },
    "transform": {
      "^.+\\.jsx?$": "babel-jest",
      "^.+\\.tsx?$": "ts-jest"
    },
    "transformIgnorePatterns": [
      "<rootDir>/node_modules/?!(react-checkbox-group)"
    ],
    "moduleNameMapper": {
      "\\.(css|less)$": "identity-obj-proxy"
    }
  },
  "dependencies": {
    "jest": "25.1.0",
    "react": "16.9.0",
    "react-checkbox-group": "^5.0.2",
    "react-dom": "16.9.0"
  },
  "devDependencies": {
    "@babel/core": "^7.9.0",
    "@babel/preset-env": "^7.9.5",
    "@types/jest": "25.1.1",
    "@types/node": "12.7.2",
    "@types/pretty": "^2.0.0",
    "@types/react": "16.9.2",
    "@types/react-dom": "16.9.0",
    "@types/react-test-renderer": "^16.9.2",
    "babel-jest": "^25.3.0",
    "css-loader": "3.2.0",
    "html-webpack-plugin": "3.2.0",
    "identity-obj-proxy": "^3.0.0",
    "react-test-renderer": "^16.13.1",
    "source-map-loader": "0.2.4",
    "style-loader": "1.0.0",
    "ts-jest": "^25.3.1",
    "ts-loader": "6.0.4",
    "typescript": "3.6.2",
    "webpack": "4.39.3",
    "webpack-cli": "3.3.7",
    "webpack-dev-server": "3.8.0"
  }
}

```

## Jest Enable ES6

Jest是Node环境，不能识别`import/export`等ES6语法，可以通过安装Babel解决

- 在项目根目录下建立`babel.config.json`文件，**特别地，不要用`.babelrc`**
- 填入：

```json
{
    "presets": [
        ["@babel/preset-env", {"targets": {"node": "current"}}]
    ]
}
```

- 不要忘记：

```shell
npm install @babel/preset-env --save-dev
```
