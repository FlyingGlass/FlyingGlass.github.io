---
title: jenkins安装
date: 2018-04-08 15:32:01
categories: 
- [Web开发]
---

*最近spring-boot项目用到了持续集成（CI）工具Jenkins进行部署，使用nginx作为反向代理*

#### [jenkins的安装](https://jenkins.io/download/)

```bash
# ubuntu 16.04 LTS 安装
apt-get update
apt-get install -y jenkins
```
#### 使用`nginx`配置`jenkins`

- `nginx`反向代理配置

```nginx
# jenkins
location ^~ /jenkins {
    proxy_pass http://127.0.0.1:8080/jenkins;
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

- `/etc/default/jenkins`添加`--prefix=$PREFIX`配置

  ```bash
  JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT --prefix=$PREFIX"
  ```

- 启动`jenkins`

  ```bash
  service jenkins start
  ```

#### 参考链接

- [jenkins download](https://jenkins.io/download/)
- [how-to-configure-jenkins-with-ssl-using-an-nginx-reverse-proxy](https://www.digitalocean.com/community/tutorials/how-to-configure-jenkins-with-ssl-using-an-nginx-reverse-proxy)