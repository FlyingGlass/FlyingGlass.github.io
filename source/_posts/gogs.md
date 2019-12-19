---
title: gogs配置
date: 2018-01-13 21:26:30
categories: 
- [Devops]
---

# 什么是 Gogs?

Gogs 是一款极易搭建的自助 Git 服务。

## Gogs目的

Gogs 的目标是打造一个最简单、最快速和最轻松的方式搭建自助 Git 服务。使用 Go 语言开发使得 Gogs 能够通过独立的二进制分发，并且支持 Go 语言支持的 **所有平台**，包括 Linux、Mac OS X、Windows 以及 ARM 平台。


## gogs配置
[gogs安装配置文档](https://gogs.io/docs/installation "gogs安装配置文档")


### gogs配置mysql数据库:

- ##### gogs要求Mysql版本为5.7以上,配置如下:

    - ##### 升级MySQL到5.7

        [树莓派中安装MySQL 5.7 ](http://einverne.github.io/post/2017/07/respberry-pi-install-mysql-5-7.html "树莓派中安装MySQL 5.7 ")
        [Install Mysql 5.7 on respbian jesse -pi 3](https://www.raspberrypi.org/forums/viewtopic.php?t=162960 "Install Mysql 5.7 on respbian jesse -pi 3")

    - ##### 配置innodb和字符集为utf8mb4

        ```mysql
        [client]
        default-character-set=utf8mb4
        [mysqld]
        character-set-server = utf8mb4
        collation-server = utf8mb4_unicode_ci
        init_connect='SET NAMES utf8mb4'
        skip-character-set-client-handshake = true
        [mysql]
        default-character-set = utf8mb4

        ```

