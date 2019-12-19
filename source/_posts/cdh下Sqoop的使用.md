---
title: cdh下Sqoop的使用
date: 2018-03-13 15:17:36
categories: 
- [大数据]
---


*最近项目需要导出Hdfs的数据到mysql中，采用sqoop作为主要的导出方案，在使用sqoop2和sqoop1的过程中，碰到一些问题以及对应的一些解决方案。思绪来的快，去的也快，特此记录*

项目中采用cdh搭建的hadoop大数据平台，其中cdh的版本为5.12.0，由于涉及到cdh中sqoop2的部署，使用sqoop2的Java api，sqoop2的源码修改以支持自定义分隔符以及如何采用sqoop1的Java api解决方案，故通过分小节来记录，主要小节分类如下：

#### Sqoop2的解决方案：

- [cdh下Sqoop2的部署](http://flyflyfish.com/2018/03/13/cdh%E4%B8%8BSqoop2%E7%9A%84%E9%83%A8%E7%BD%B2/)
- Sqoop2 Java api解析
- Sqoop2 自定义分隔符

#### Sqoop1的解决方案：

- cdh下Sqoop1的部署
- Sqoop1 Java api解析
- Sqoop1 remote ssh执行命令 


