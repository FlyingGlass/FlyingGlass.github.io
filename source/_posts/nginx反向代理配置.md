---
title: nginx反向代理配置
date: 2018-05-12 10:48:41
categories: 
- [Web开发]
---

*在web开发中，nginx一般被用作网关，配置路由功能，在使用nginx进行反向代理的过程中，碰到一些配置问题，特此文记录*

由于项目中涉及到`php`和`java`的`web`服务，在使用`nginx`进行转发过程中，需要通过`rewrite`和`location`配合反向代理，配置略微繁琐，废话不多说直接上`nginx`的配置。

#### Nginx的配置

```nginx
location / {
    # Filename Not Found
    if (!-e $request_filename) {
        rewrite "^/(.*)$" /index.php last;
    }
    # Uri contains php-web url
    if ( $uri ~ /php-web ) {
        rewrite ^/$/index.php last;
    }
    # Uri not contains php-web url
    if ( $uri !~ /php-web ) {
        rewrite / /java-web last;
    }
    try_files $uri $uri/ /index.php;
}
# Filter java-web uri
location ^~ /java-web {
    proxy_pass http://ip:port/java-web;
}
```

由于`nginx`没有`if else`之类的结构，通过多个`if`结构进行过滤，上述配置的项目实现了:

- 如果请求的`uri`中含有`php-web`将进行`php web`处理
- 如果请求的`uri`中不含有`php-web`将进行`java web`处理

#### 参考链接

- [nginx配置url重写](https://xuexb.com/post/nginx-url-rewrite.html)
- [nginx location 匹配规则](https://gist.github.com/luxixing/7262911)