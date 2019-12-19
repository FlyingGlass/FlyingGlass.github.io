---
title: docker常用命令
date: 2019-06-17 16:06:33
categories: 
- [Devops]
---

##### `docker`常用命令

```bash
# 清理无效的images
docker rmi $(docker images -a -q)
# daemon启动指定的image
docker run -d fcoin:latest
# 进入container容器
docker exec -ti e7e325e08354 sh
# build 镜像
docker build -t fcoin:latest -f Dockerfile .
# 停止container
docker stop e7e325e08354

# 指定docker-compose.yml，设置project name，强制build
docker-compose -f docker-compose.yml -p docker up --build
# 查看日志
docker-compose logs -f
# 后台启动
docker-compose up -d
# 停止
docker-compose down

# 常用配置
cat /etc/docker/daemon.json
{
  "insecure-registries": [
    "doc.flyflyfish.com:8082"
  ],
  "registry-mirrors": [
    "http://doc.flyflyfish.com:8082"
  ],
  "debug": true,
  "experimental": false
}
```