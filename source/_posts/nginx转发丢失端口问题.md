---
title: nginx转发丢失端口问题
date: 2018-02-24 17:57:56
categories: 
- [Web开发]
---

*nginx对于redirect location的处理，会造成端口丢失的现象。针对nginx处理非80标准端口进行redirect时导致端口丢失的问题，本文主要介绍nginx的反向代理和访问目录缺失/这两种情形*

以下为两种情形的参考范例：

```nginx
# 反向代理
listen 26479 default_server;
location / {
    root /var/www/html;
    proxy_pass http://127.0.0.1:8080/;
    proxy_set_header Host             $host;
    proxy_set_header X-Real-IP        $remote_addr;  
    proxy_set_header X-Forwarded-For  $proxy_add_x_forwarded_for;
}

# 访问/gitlab的时候会自动加上/成为/gitlab/
listen 26479 default_server;
location /gitlab {
    root /var/www/html;
}
```



#### 反向代理

查看[nginx官方文档](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_set_header)提及的说明：

```
An unchanged “Host” request header field can be passed like this:

proxy_set_header Host       $http_host;
```

同时参考gitlab-ce使用非标准端口访问的方案，查看gitlab-ce的nginx配置如下：

`proxy_set_header Host	$http_host;`

通过查看nginx软件包，发现nginx已提供参考文件/etc/nginx/proxy_params

```nginx
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

由于我使用的centos下面手动编译的nginx并未找到proxy_params，我的nginx位于/usr/local/nginx，于是通过`vim /usr/local/nginx/conf/proxy_params`输入以上内容，配置nginx的location如下：

```nginx
location /gitlab {
    proxy_pass http://127.0.0.1:9090/gitlab;
    include proxy_params;
}
```

也可以手工输入proxy_params的内容到`location`配置段下

#### 访问目录缺失/

比如`$document_root`存在`data/index.html`文件，但是访问的时候最后没加`/`，nginx会自动给你带上`/`，返回一个301重定向(这个行为和[apache一致](https://httpd.apache.org/docs/2.4/mod/mod_dir.html))，但是当nginx监听的是非标准端口，这个301返回的`Location`没有端口号，导致浏览器请求出错。用curl可以很明显的看到这一点:

```
[~]# curl -v 127.0.0.1:9090/data
* About to connect() to 127.0.0.1 port 9090 (#0)
*   Trying 127.0.0.1...
* Connected to 127.0.0.1 (127.0.0.1) port 9090 (#0)
> GET /data HTTP/1.1
> User-Agent: curl/7.29.0
> Host: 127.0.0.1:9090
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
* Server nginx is not blacklisted
< Server: nginx
< Date: Mon, 26 Feb 2018 02:20:49 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 178
< Connection: keep-alive
< Cache-Control: no-cache
< Location: http://127.0.0.1/data/
<
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
* Connection #0 to host 127.0.0.1 left intact
```

可以看到Location端口号丢失了，这个和反向代理不一样。通过google搜索，在[stackoverflow的question](https://stackoverflow.com/questions/15555428/nginx-causes-301-redirect-if-theres-no-trailing-slash)找到了解决方案：

```nginx
if (-d $request_filename) {
    rewrite [^/]$ $scheme://$http_host$uri/ permanent;
}
```

通过对URL进行重写带上端口