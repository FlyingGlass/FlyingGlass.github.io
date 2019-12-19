---
title: truffle开发入门
date: 2018-03-19 20:13:51
categories: 
- [区块链]
---

*Truffle是以太坊最受欢迎的开发框架，在搭建第一个区块链程序的过程，由于truffle的版本更新为3.0以上，按照大部分部署教程无法运行，特以此记录。本文部署环境为macosx，采用homebrew套件进行部署，homebrew的安装可参考之前文章[homebrew安装](http://flyflyfish.com/2018/01/13/homebrew/)*

##### Node环境配置

```bash
# Install node
brew install node
# Test 
node -v
```

##### Truffle安装

```bash
# Install truffle v3.2.1
npm install -g truffle@3.2.1
# Install testrpc
npm install -g ethereumjs-testrpc
# Testrpc
testrpc
```

##### Webpack Truffle Box

```bash
# webpack
mkdir webpack && cd webpack 
# Truffle init
truffle ubox webpack
# Compile and migrate
truffle compile && truffle migrate
# Serve
npm run dev
```

##### 参考链接

- [how-to-install-nodejs-and-npm-on-mac-using-homebrew](https://www.dyclassroom.com/howto-mac/how-to-install-nodejs-and-npm-on-mac-using-homebrew)
- [file-node-npm-install-md](https://gist.github.com/rcugut/c7abd2a425bb65da3c61d8341cd4b02d#file-node-npm-install-md)
- [truffle-init-webpack](https://github.com/trufflesuite/truffle-init-webpack)

