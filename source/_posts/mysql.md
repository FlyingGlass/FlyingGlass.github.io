---
title: mysql配置
date: 2018-01-13 20:48:17
categories: 
- [MySQL]
---

### centos 6.5

``` bash
yum -y update && yum -y upgrade
yum -y install mysql-server
service mysqld restart
```

### centos 7.0

``` bash
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum -y install mysql-community-server
service mysqld restart
```

### Ubuntu 16.04

```bash
# Remove
sudo apt-get remove mysql-*
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
# Install
sudo apt-get update
sudo apt-get install mysql-server
mysql_secure_installation
systemctl status mysql.service
mysqladmin -p -u root version
```

### /etc/my.cnf配置

``` mysql
[root@namenode01 ~]# grep -v "#" /etc/my.cnf | grep -v "^$"
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
init_connect='SET NAMES utf8mb4'
skip-character-set-client-handshake=true
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[mysql]
default-character-set=utf8mb4
[mysqld]
lower_case_table_names=1
[client]
port            = 3306
default-character-set=utf8mb4
```
### 设置用户
``` mysql
grant all privileges on *.* to 'root'@'%' identified by 'root';
flush privileges;

```



