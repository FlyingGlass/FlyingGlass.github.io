---
title: nginx反爬策略配置
date: 2018-06-02 11:39:00
categories: 
- [Nginx]
---

*网站部署上线后，为了防止爬虫的侵扰，一般会进行反爬策略限制。在Web开发中Nginx一般作为Gateway和Router，本文主要介绍如何使用Nginx进行User-Agent和RateLimit两个方面来进行反爬*

#### User-Agent

`cat /etc/nginx/agent_deny`

```nginx
#禁止Scrapy等工具的抓取
if ($http_user_agent ~* (Scrapy|Curl|HttpClient)) {
     return 403;
}

#禁止指定UA及UA为空的访问
if ($http_user_agent ~* "FeedDemon|Indy Library|Alexa Toolbar|AskTbFXTV|AhrefsBot|CrawlDaddy|CoolpadWebkit|Java|Feedly|UniversalFeedParser|ApacheBench|Microsoft URL Control|Swiftbot|ZmEu|oBot|jaunty|Python-urllib|lightDeckReports Bot|YYSpider|DigExt|HttpClient|MJ12bot|heritrix|EasouSpider|Ezooms|^$" ) {
     return 403;
}

#禁止非GET|POST方式的抓取
if ($request_method !~ ^(GET|POST)$) {
    return 403;
}
```

#### RateLimit

*RateLimit传统的算法主要分为token bucket和leaky bucket，nginx和传统的leaky bucket算法略微有些区别，leaky bucket算法主要处理方式Traffic Shaping和Traffic Policing，Traffic Shaping的核心理念是"等待"，Traffic Policing的核心理念是"丢弃"，在bucket满后，常见的处理方式为：*

- 暂时拦截住上方水的向下流动，等待桶中的一部分水漏走后，再放行上方水
- 溢出的上方水直接抛弃

```nginx
# Rate Limit
# 此设置只能放在 http 节点下
limit_req_zone $binary_remote_addr zone=ratelimit:10m rate=2r/s;
# 默认为503 Service Unavailable，返回429 Too Many Request更为合适
limit_req_status 429;

# Server
server {
    listen 80;
    server_name localhost;
    charset utf-8;
    
    location / {
        # 此设置在 http, server, location 节点都可以设置；设置了 nodelay 将不会等待
        limit_req zone=ratelimit burst=20 nodelay;
        include agent_deny;
        proxy_pass http://127.0.0.1:8080/java-web;
    }
}
```

#### 参考链接

- [Nginx下limit_req模块burst参数超详细解析](https://blog.csdn.net/hellow__world/article/details/78658041)
- [Nginx模块开发（10）—limit_req模块分析](http://cjhust.blog.163.com/blog/static/17582715720111017114121678/)
- [漏桶算法和 NGINX 的 limit_req 模块](https://www.chrisyue.com/leaky-bucket-and-nginx-limit_req-module.html)