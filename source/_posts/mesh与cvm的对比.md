---
title: mesh与cvm的对比
date: 2023-03-22 14:52:41
categories: 
- [架构之路,Mesh]
---

### Mesh与CVM的对比

#### 物理对比

- 物理架构

    [mesh腾讯云物理架构.md](mesh腾讯云物理架构.md) 

- 产品分布

  1. CVM：云服务器
  2. Mesh：容器服务（TKE），只读权限

#### 监控对比

- 日志
  1. 业务日志
     - CVM: ES，通过host.hostname区分
     - Mesh：ES，通过pod.namespace，pod.ip，pod.name区分
  2. Tomcat访问日志
     - CVM：cvm机器，通过jumpserver访问cvm机器
     - Mesh：ES，通过pod.namespace，pod.ip，pod.name区分
  3. GC日志
     - CVM：同Tomcat访问日志
     - Mesh：原理是通过kubectl查询，UI可采用Zadig或者Tke控制台查看
  4. Sentinel日志
     - CVM：同Tomcat访问日志
     - Mesh：存储介质为cfs，可通过cvm机器挂载后查看
- 指标
  1. Prometheus
     - CVM：原理是通过JVM应用获取并上传，新增cvm需运维手动绑定
     - Mesh：原理同cvm，新增pod会自动绑定
  2. Cat
     - CVM：Cat Client埋点上传
     - Mesh：同cvm
- 链路
  1. Skywalking
     - CVM：javaagent引入Skywalking Agent，由插件织入埋点上传
     - Mesh：同cvm，sidecar埋点暂缺失

#### 排障对比

- 日志
  1. 报错日志
     - CVM：结合具体日志，通过metadata前往cvm下监控系统的日志、指标、链路定位和处理，对象主要为cvm机器
     - Mesh：结合具体日志，通过metadata前往Mesh下监控系统的日志、指标、链路定位和处理，对象主要为k8s
- 权限
  1. 权限划分
     - CVM：用户账号划分
     - Mesh：登录容器与应用进程为同一用户，看运维最终如何处理？
- 工具
  1. 安装
     - CVM：申请运维安装，cvm安装一次后终身存在
     - Mesh：申请运维安装，pod安装一次后，重启即消失，常用工具可提前放置镜像
  2. 调试
     - CVM：进入cvm简单调试，深度调试需要申请运维进行账号权限提级，并且可以单独摘除流量查看
     - Mesh：通过k8s简单调试，深度调试情况比较复杂，目前借助nacos在应用层面可以单独摘除流量查看
