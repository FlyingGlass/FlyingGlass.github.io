---
title: ssr开机启动
date: 2019-06-19 15:56:47
categories: 
- [安装配置]
---

*本文主要介绍privoxy和ssr的安装和开机启动，运行系统为centos7.6，python版本为2.7.x；至于为何使用ssr，而不使用ss，主要得益于ssr具有的加密和混淆特性，其中的爱恨纠葛就不赘述，有兴趣的小伙伴可以自行搜索。*

#### SSR Client

```bash
git clone git@github.com:shadowsocksr-backup/shadowsocksr.git
# 超链接
cd /usr/local/bin && ln -s ${pwd}/shadowsocksr/shadowsocks/local.py ssrlocal
# 配置shadowsocks.json
cat /etc/shadowsocks.json 
{
        "server":"your server ip",
        "server_ipv6": "::",
        "server_port":8388,
        "local_address": "127.0.0.1",
        "local_port":1080,
        "password":"your password",
        "timeout":300,
        "udp_timeout": 60,
        "method":"aes-256-cfb",
        "protocol": "auth_aes128_md5",
        "protocol_param": "",
        "obfs":"tls1.2_ticket_auth",
        "obfs_param": "",
        "fast_open": false,
        "workers": 1
}
# 配置启动脚本
vim /etc/systemd/system/shadowsocksr.service
[Unit]
Description=SSR
[Service]
TimeoutStartSec=0
ExecStart=/usr/local/bin/ssrlocal -c /etc/shadowsocks.json
[Install]
WantedBy=multi-user.target

# 设置开机启动
systemctl enable shadowsocksr
# 启动
systemctl start shadowsocksr
# 查看
systemctl status shadowsocksr
# 停止
systemctl stop shadowsocksr
# 取消开机启动
systemctl disable shadowsocksr
```

#### Privoxy

```bash
yum install -y privoxy
# 修改配置
cat /etc/privoxy/config | grep -v "#"

listen-address  0.0.0.0:8118
...
forward-socks5 / 127.0.0.1:1080 .

# 设置开机启动
systemctl enable privoxy
# 启动
systemctl start privoxy
# 查看
systemctl status privoxy
# 停止
systemctl stop privoxy
# 取消开机启动
systemctl disable privoxy
```

#### 参考链接

- [Centos + Shadowsocks客户端 + Privoxy实现外网访问](http://exp-blog.com/2018/07/04/pid-1591/)
- [windows ssr](https://github.com/shadowsocksrr/shadowsocksr-csharp)

- [SSR-ShadowsocksR-配置及使用](https://walesexcitedmei.github.io/2018/11/26/SSR-ShadowsocksR-配置及使用/)

