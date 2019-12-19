---
title: cdh下Sqoop2的安装
date: 2018-03-13 14:51:03
categories: 
- [大数据]
---

*本文中采用的cdh版本为5.12.0，如何部署cdh可以参考之前的博文[cdh安装准备](http://flyflyfish.com/2018/02/04/cdh%E5%AE%89%E8%A3%85%E5%87%86%E5%A4%87/)和[cdh5.12.0的安装](http://flyflyfish.com/2018/02/04/cdh5-12-0%E5%AE%89%E8%A3%85/)*

#### sqoop2配置

- cdh安装sqoop2
- 配置Drivers
- 配置hadoop依赖

#### cdh安装sqoop2

cdh5.12.0下安装sqoop2，参见官方的[安装手册](https://www.cloudera.com/documentation/enterprise/5-6-x/topics/cdh_ig_sqoop2_installation.html)，直接在搭建好的cdh管理界面添加Sqoop2服务组件，然后一路选择`继续`安装，等待Sqoop2服务启动完毕。

##### 配置Drivers

由于本文是采用hdfs导出数据到mysql，故需配置mysql的connector的jar依赖包，参考[官方的指南](https://www.cloudera.com/documentation/enterprise/5-4-x/topics/cdh_ig_jdbc_driver_install.html)，下载mysql-connector-java.jar包，然后拷贝到对应的`cdh`的默认`sqoop2`的目录`/var/lin/sqoop2`目录中，最终的目录结构如下：

```bash
[~]ls /var/lib/sqoop2
mysql-connector-java.jar  postgresql-9.0-801.jdbc4.jar  repository  tomcat-deployment
```

##### 配置hadoop超链接

```bash
cd /opt/cloudera/parcels/CDH/lib/hadoop/client
sudo ln -s ../../hadoop-hdfs/lib/jackson-mapper-asl-1.8.8.jar .
sudo ln -s ../../hadoop-hdfs/lib/jackson-core-asl-1.8.8.jar .
```

***Tips：配置完毕后在cdh管理界面重启Sqoop2组件服务，否则配置不生效***

#### 参考链接

- [安装文档](https://www.cloudera.com/documentation/enterprise/5-4-x/topics/cdh_ig_sqoop2_installation.html)
- [JDBC Drviers for Sqoop](https://www.cloudera.com/documentation/enterprise/5-4-x/topics/cdh_ig_jdbc_driver_install.html)
- [Sqoop Error: Could not start job](https://community.cloudera.com/t5/Data-Ingestion-Integration/Sqoop-Error-Could-not-start-job/m-p/45359#M1890)

