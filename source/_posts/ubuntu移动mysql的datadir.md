---
title: ubuntu移动mysql的datadir
date: 2018-11-26 18:06:20
categories: 
- [MySQL]
---

### Introduction

数据库会随着时间的推移而增长，有时会超出文件系统上的空间。当它们位于与操作系统其余部分相同的分区上时，您还可能遇到`I/O`争用。`RAID`、网络块存储和其他设备可以提供冗余和其他需要的特性。无论您是在添加更多空间、评估优化性能的方法，还是希望利用其他存储特性，本文都将指导您重新定位`MySQL`的数据目录。

### Prerequisites

前置条件:

- `ubuntu16.04`服务器，具有非根用户和`sudo`特权。
- `MySQL`服务器。如果你还没有安装`MySQL`, 可以参考之前博文的[mysql的安装](https://flyflyfish.com/2018/01/13/mysql/)。

### STEP

- 登录`mysql`查看当前的`datadir`：

```bash
mysql -u root -p
mysql> select @@datadir;
sudo systemctl stop mysql
sudo systemctl status mysql
```

- 同步现有的`datadir`到新的目录：

```bash
# rsync
sudo rsync -av /var/lib/mysql /mnt/data01
sudo mv /var/lib/mysql /var/lib/mysql.bak
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
$ cat /etc/mysql/mysql.conf.d/mysqld.cnf
...
datadir         = /mnt/data01/mysql
...
# Configuring AppArmor Access Control Rules
sudo vim /etc/apparmor.d/tunables/alias
$ cat /etc/apparmor.d/tunables/alias
. . .
alias /var/lib/mysql/ -> /mnt/data01/mysql/
. . .
# Restart apparmor
sudo systemctl restart apparmor
```

- 创建目录以通过`/usr/share/mysql/mysql-systemd-start`脚本的检测：


```bash
$ cat /usr/share/mysql/mysql-systemd-start
...
datadir=$(get_mysql_option mysqld datadir "/var/lib/mysql")
if [ ! -d "${datadir}" ] && [ ! -L "${datadir}" ]; then
	echo "MySQL data dir not found at ${datadir}. Please create one."
    exit 1
fi
  
if [ ! -d "${datadir}/mysql" ] && [ ! -L "${datadir}/mysql" ]; then
    echo "MySQL system database not found in ${datadir}. Please run mysqld --initialize."
    exit 1
fi
...
# 创建目录，根据自己的datadir决定
sudo mkdir /var/lib/mysql/mysql -p
```

- 验证是否更改成功

```bash
sudo systemctl start mysql
sudo systemctl status mysql
mysql -u root -p
mysql> select @@datadir;
+----------------------------+
| @@datadir                  |
+----------------------------+
| /mnt/data01/mysql/ |
+----------------------------+
1 row in set (0.01 sec)
# Restart Mysql
sudo systemctl restart mysql
sudo systemctl status mysql
```

#### 参考链接：

- [How To Install MySQL on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-16-04)
- [how-to-move-a-mysql-data-directory-to-a-new-location-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-move-a-mysql-data-directory-to-a-new-location-on-ubuntu-16-04)