---
title: nexus本地仓库搭建
date: 2018-04-08 11:27:23
categories: 
- [Devops]
---

*nexus是可以搭建maven，npm，docker，pypi私有仓库的工具，本文记录本地nexus的安装配置*

#### docker-compose安装nexus

- 其中`docker-compose.yml`的配置：

```yaml
version: '2'
  # nexus
  nexus:
    image: sonatype/nexus3:3.2.0
    restart: always
    ports:
      - "8081:8081"
    volumes:
      - /srv/docker/nexus/nexus-data:/nexus-data:Z
    environment:
    - NEXUS_CONTEXT=nexus
```

- 配置挂载目录的权限：

```bash
mkdir -p /srv/docker/nexus/nexus-data && chown -R 200 /srv/docker/nexus/nexus-data
```

#### 配置nexus的仓库组

*nexus默认的仓库类型有以下3种：*

- group：组仓库，用于方便开发人员自己设定的仓库
- hosted：宿主仓库，内部项目的开发仓库
- proxy：代理仓库，从远程中央仓库中寻找数据的仓库

#### [示例代码-github](https://github.com/flyongrass/docker)

#### 参考链接

- [使用nexus3搭建私有仓库](https://blog.csdn.net/qq_34618853/article/details/70172116)
- [Sonatype Nexus3 Docker: sonatype/nexus3](https://github.com/sonatype/docker-nexus3)
- [maven私服nexus3.x环境配置](https://www.xncoding.com/2017/09/02/tool/nexus.html)