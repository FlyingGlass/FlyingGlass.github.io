---
title: lily解决方案
date: 2021-08-30 14:35:13
categories: 
- [架构之路,微服务]
---

## 概述

#### 核心模块

主要分为注册中心、配置中心、管理中心、策略编排

- 注册中心

  基于Nacos注册中心，接管ServiceRegistry和ServerList处理Instance的Metadata(version，env，group，region，zone)

- 配置中心

  基于Nacos配置中心，处理本地、远程等配置更新

- 管理中心

  暴露Rest Endpoint接口，提供核心Api

- 策略编排

  1. 实现网关和服务的路由功能

  2. 处理Header参数的拦截、传递
  3. 处理策略的过滤匹配
  4. 输出调用链和日志

#### 全链路监控

- 调用链监控
  1. 基于SkyWalking链路
  2. 输出l-h-*链路策略信息
  3. 输出l-h-s-*输出服务自身信息
- 日志监控
  1. 基于MDC链路
  2. 输出l-h-*链路策略信息
  3. 输出l-h-s-*输出服务自身信息

#### 元数据自定义

- 本地配置

  本地Metadata(version，env，group，region，zone)配置

- header配置

  http请求header指定Metadata

- 注册中心配置

  注册中心动态化，依赖Nacos

