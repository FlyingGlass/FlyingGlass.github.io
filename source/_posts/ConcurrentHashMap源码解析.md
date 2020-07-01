---
title: ConcurrentHashMap源码解析
date: 2020-06-30 13:59:18
categories: 
- [Java]
---

*ConcurrentHashMap为Java中常用的并发容器，用于解决HashTable锁住整个hash表的问题，JDK8针对ConcurrentHashMap作了改进和优化，摒弃JDK7的分段锁机制，采用Node + CAS + synchronized保证并发安全。*

#### 源码分析

- 类视图

  ![类视图](ConcurrentHashMap源码解析/1593517421.png)

- 类注释

- 类常量

- 插入

  

#### 总结