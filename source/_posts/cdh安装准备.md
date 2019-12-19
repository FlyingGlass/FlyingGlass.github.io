---
title: cdh5.12.0安装准备
date: 2018-02-04 12:05:09
categories: 
- [大数据]
---

## 简介

CDH是cloudera公司开发的一个快速部署、高效管理Hadoop和其各种组件的一个商业化产品。

主要分为两部分，分别为Cloudera Manager和CDH软件包。其中Cloudera Manager负责集群的部署与管理。
CDH软件包囊括了hdaoop各类的组件的安装包，例如hive、hdfs、spark等等。

### 系统要求
摘自官网文档:
The four hosts in the cluster must satisfy the following requirements:
- The hosts must have at least 10 GB RAM.
- You must have root or password-less sudo access to the hosts.
- If using root, the hosts must accept the same root password.
- The hosts must have Internet access to allow the wizard to install software from ``archive.cloudera.com``
- Run a supported OS:
    - See [ CDH 5 and Cloudera Manager 5 Requirements and Supported Versions. ](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html)
    - SLES - SUSE Linux Enterprise Server 11, 64-bit. Service Pack 2 or higher is required. The Updates repository must be active and [ SUSE Linux Enterprise Software Development Kit 11 SP1 ]( http://download.novell.com/Download?buildid=zzeQqpY9nK4~ ) is required.
    - Debian - Wheezy (7.0 and 7.1), 64-bit.
    - Ubuntu - Trusty (14.04) and (Precise) 12.04, 64-bit.
      If your environment does not satisfy these requirements, the procedure described in this guide might not work. For information about other Cloudera Manager installation options and requirements, 
      see Installing Cloudera Manager and CDH.


### HADOOP版本
目前Hadoop比较流行的主要有2个版本，Apache和Cloudera版本。

- Apache Hadoop：维护人员比较多，更新频率比较快，但是稳定性比较差。
- Cloudera Hadoop（CDH）：CDH：Cloudera公司的发行版本，基于ApacheHadoop的二次开发，优化了组件兼容和交互接口、简化安装配置、增加Cloudera兼容特性。


### CDH安装准备（本文操作系统为centos7及以上系统）

#### jdk 配置 vim /etc/profile

```bash
# JAVA
JAVA_HOME=/usr/local/jdk1.8.0_151
JRE_HOME=$JAVA_HOME/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib

# PATH
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin

# export
export PATH JAVA_HOME JRE_HOME CLASS_PATH

# 环境变量生效
 . /etc/profile
```

#### hosts修改(每一个节点都需要修改)
```bash
    [root@n171 cloudera-RPMS] cat /etc/hosts

    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    
    192.168.30.171 n171
    192.168.30.172 n172
    192.168.30.173 n173
    192.168.30.174 n174
```

#### hostname配置（所有节点都需要修改需要重启）

```bash
[root@n171] cat /etc/hostname
n171
```



#### 防火墙配置(cdh端口过多先关闭防火墙)

```bash
# 关闭防火墙
sudo systemctl stop firewalld.service

# 关闭开机启动
sudo systemctl disable firewalld.service 
```

#### selinux

```bash
# 查看SELinux状态
getenforce # 如果不是Disabled,修改 SELinux=disabled ，需要重启机器

# 修改配置
vim /etc/selinux/config

# 查看配置内容 grep -v "#" /etc/selinux/config | grep -v '^$'
SELINUX=disabled
SELINUXTYPE=targeted
```

#### ntp配置(分布式系统时间同步工具)


[参考linux鸟哥私房菜ntp](http://cn.linux.vbird.org/linux_server/0440ntp.php#server "linux鸟哥私房菜ntp")

- master(本文以n171为主节点)

```bash
# 查看主节点n171的配置
[root@n171 ~]grep -v "#" /etc/ntp.conf | grep -v "^$"
# /etc/ntp.conf 配置
driftfile /var/lib/ntp/drift
restrict default nomodify notrap nopeer noquery
restrict 127.0.0.1
restrict ::1
restrict 192.168.30.0 mask 255.255.255.0 nomodify notrap
server ntp1.aliyun.com prefer
server time1.aliyun.com
includefile /etc/ntp/crypto/pw
keys /etc/ntp/keys
disable monitor
```


- slave(所有从节点ntp客户端配置) 
``` bash
# 查看从节点配置
[root@n172 ~]grep -v "#" /etc/ntp.conf | grep -v "^$"
# /etc/ntp.conf配置
driftfile /var/lib/ntp/drift
restrict default nomodify notrap nopeer noquery
restrict 127.0.0.1
restrict ::1
restrict namenode01
server namenode01
includefile /etc/ntp/crypto/pw
keys /etc/ntp/keys
disable monitor

```
##### 参考链接：

- [CDH5.12.0安装官网链接](https://www.cloudera.com/documentation/enterprise/5-12-x/topics/installation.html)

