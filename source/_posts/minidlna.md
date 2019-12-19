---
title: minidlna配置
date: 2018-01-13 21:48:50
categories: 
- [Devops]
---

#### ubuntu配置minidlna

- 删除minidlna依赖：

``` bash
sudo apt-get purge minidlna
sudo apt-get remove minidlna 
sudo apt-get autoremove
```

- 安装： `sudo apt-get install minidlna`

- 修改配置

``` bash
sudo vim /etc/default/minidlna
# User and group the daemon should run as
USER="root"
#GROUP="minidlna"

sudo vim /etc/minidlna.conf
# Specify the user name or uid to run as.
user=root

sudo service minidlna restart
```


#### 参考链接
- [Raspberry Pi （树莓派）折腾记之二](http://skypegnu1.blog.51cto.com/8991766/1654186 "Raspberry Pi （树莓派）折腾记之二")
- [启动dlna权限问题](http://askubuntu.com/questions/266033/permissions-for-video-folder-for-minidlna "如果启动dlna碰到权限问题:")
