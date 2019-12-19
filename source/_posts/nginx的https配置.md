---
title: nginx的https配置
date: 2018-05-27 21:38:26
categories: 
- [Nginx]
---

*在web开发中，一般使用nginx作为网关，本文介绍nginx下的https的配置*

#### 证书申请

主要介绍阿里云免费`SSL`证书申请

- 购买`SSL`证书：[阿里云免费SSL地址](https://common-buy.aliyun.com/?spm=5176.2020520163.cas.1.2arDtO&commodityCode=cas#/buy)
- 修改`CNAME`配置：`SSL`的`DNS`验证需要在`DNS`解析规则添加`TXT`记录，需要删除`CNAME`或者改为`A`记录
- 补全信息：一个免费`SSL`证书只能绑定一个域名，选择`DNS验证`

#### Nginx配置

- 创建证书目录：`mkdir /etc/nginx/cert`

- 进行`https`配置`vim /etc/nginx/sites-available/ssl`：

  ```nginx
  server {
          listen 443;
          server_name your.domain.com;
          root /var/www/html;
          index index.html index.htm;
          ssl on;
          ssl_certificate   cert/cert.pem;
          ssl_certificate_key  cert/cert.key;
          ssl_session_timeout 5m;
          ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
          ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
          ssl_prefer_server_ciphers on;
          location / {
                  try_files $uri $uri/ =404;
          }
  }
  ```

- 配置`http`重定向到`https`：

  ```nginx
  # HTTP server
  server {
          listen 80;
          server_name your.domain.com;
  
          #rewrite ^(.*)$ https://$host$1 permanent; # Nginx 旧写法,$1匹配()的内容
          return 301 https://$host$request_uri; # Nginx 新写法
  }
  ```

#### 参考链接

- [使用阿里云免费SSL证书实现全站HTTPS化](https://segmentfault.com/a/1190000009220479)
- [域名验证推送到阿里云DNS失败了，该怎么办？](https://help.aliyun.com/knowledge_detail/48058.html?spm=a2c4g.11186631.2.4.HeXUW1)
- [Nginx的https配置记录以及http强制跳转到https的方法梳理](https://zhuanlan.zhihu.com/p/32055783)
- [nginx配置location总结及rewrite规则写法](http://seanlook.com/2015/05/17/nginx-location-rewrite/)
