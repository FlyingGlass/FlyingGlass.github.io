---
title: mysql5.7离线安装
date: 2019-06-17 10:53:49
categories: 
- [Devops]
---

*本文主要介绍mysql5.7的离线安装，关于mysql的配置可以参考之前的博客[Mysql配置](<https://www.flyflyfish.com/2018/01/13/mysql/>)，离线安装中需要的rpm包可以前往https://pkgs.org/下载*

####  Centos 7.5

- 下载安装`mysql`，下载mysql的安装包，[下载地址](<http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.18-1.el7.x86_64.rpm-bundle.tar>)，解压后`rpm`列表如下：

```bash
tar -xvf mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar
# ls
mysql-community-client-5.7.26-1.el7.x86_64.rpm
mysql-community-common-5.7.26-1.el7.x86_64.rpm
mysql-community-devel-5.7.26-1.el7.x86_64.rpm
mysql-community-embedded-5.7.26-1.el7.x86_64.rpm
mysql-community-embedded-compat-5.7.26-1.el7.x86_64.rpm
mysql-community-embedded-devel-5.7.26-1.el7.x86_64.rpm
mysql-community-libs-5.7.26-1.el7.x86_64.rpm
mysql-community-libs-compat-5.7.26-1.el7.x86_64.rpm
mysql-community-server-5.7.26-1.el7.x86_64.rpm
mysql-community-test-5.7.26-1.el7.x86_64.rpm
```

- 依赖包

  1. `numactl`软件包

     ```bash
     numactl-2.0.9-4.el7_2.x86_64.rpm
     numactl-devel-2.0.9-4.el7_2.x86_64.rpm
     numactl-libs-2.0.9-4.el7_2.x86_64.rpm
     ```

  2. `perl`软件包

     ```bash
     perl-Data-Dumper-2.145-3.el7.x86_64.rpm
     ```

     - `perl`相关

       ```bash
       perl-5.16.3-293.el7.x86_64.rpm 
       perl-Carp-1.26-244.el7.noarch.rpm
       perl-Encode-2.51-7.el7.x86_64.rpm
       perl-Exporter-5.68-3.el7.noarch.rpm
       perl-File-Path-2.09-2.el7.noarch.rpm
       perl-File-Temp-0.23.01-3.el7.noarch.rpm
       perl-Filter-1.49-3.el7.x86_64.rpm
       perl-Getopt-Long-2.40-3.el7.noarch.rpm
       perl-HTTP-Tiny-0.033-3.el7.noarch.rpm
       perl-PathTools-3.40-5.el7.x86_64.rpm
       perl-Pod-Escapes-1.04-293.el7.noarch.rpm
       perl-Pod-Perldoc-3.20-4.el7.noarch.rpm
       perl-Pod-Simple-3.28-4.el7.noarch.rpm
       perl-Pod-Usage-1.63-3.el7.noarch.rpm
       perl-Scalar-List-Utils-1.27-248.el7.x86_64.rpm
       perl-Socket-2.010-4.el7.x86_64.rpm
       perl-Storable-2.45-3.el7.x86_64.rpm
       perl-Text-ParseWords-3.29-4.el7.noarch.rpm
       perl-Time-HiRes-1.9725-3.el7.x86_64.rpm
       perl-Time-Local-1.2300-2.el7.noarch.rpm
       perl-constant-1.27-2.el7.noarch.rpm
       perl-libs-5.16.3-293.el7.x86_64.rpm
       perl-macros-5.16.3-293.el7.x86_64.rpm
       perl-parent-0.225-244.el7.noarch.rpm
       perl-podlators-2.5.1-3.el7.noarch.rpm
       perl-threads-1.87-4.el7.x86_64.rpm
       perl-threads-shared-1.43-6.el7.x86_64.rpm
       ```

  3.  其他软件包

     `libaio-0.3.109-13.el7.x86_64.rpm`

  4. 查询并卸载系统自带的`Mariadb`（会与`mysql`冲突）

     ```bash
     # 查询mariadb
     rpm -ga | grep mariadb
     rpm -e --nodeps 查询出来的版本
     ```

- 安装`mysql`

  ```bash
  rpm -ivh libaio-0.3.109-13.el7.x86_64.rpm
  rpm -ivh numactl*
  rpm -ivh perl-*
  rpm -ivh  mysql-community-*
  ```

- 启动`mysql`服务

  ```bash
  systemctl start mysqld
  ```

- 设置开机启动

  ```bash
  systemctl enable mysqld
  
  systemctl daemon-reload
  ```

- 查看`mysql root`临时密码

  ```bash
  cat /var/log/mysqld.log | grep password
  # 修改密码
  # mysql -uroot -p
  # SET PASSWORD FOR ‘root’@’localhost’ = PASSWORD(‘newpass’);
  ```

- 新建用户并授权

  ```mysql
  create user newuser@’%’ identified by ‘password’;
  grant all privileges on db.* to newuser;
  # grant all privileges on *.* to newuser;
  ```

#### 参考链接

- [Centos7离线安装MySql](https://wangzhigang.org/2018/03/12/CentOs7%E7%A6%BB%E7%BA%BF%E5%AE%89%E8%A3%85MySql/)