---
title: homebrew配置
date: 2018-01-13 21:41:35
categories: 
- [OS]
---

#### 安装

[参考链接](https://brew.sh/ "参考链接")

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

#### homebrew替换为清华的源

- 替换formula 索引的镜像（即 brew update 时所更新内容）

``` bash
cd "$(brew --repo)"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

brew update

```

- 替换Homebrew 二进制预编译包的镜像

``` bash
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc

```

[homebrew清华大学开源使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/ "homebrew清华大学开源使用帮助")


