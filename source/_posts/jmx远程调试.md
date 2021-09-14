---
title: jmx远程调试
date: 2021-09-14 16:44:22
categories: 
- [Web开发]
---

#### Remote Debug开启

- 开启远程调试

```shell
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8739
```

- idea开启debug

![img](jmx远程调试/idea远程调试.png)

