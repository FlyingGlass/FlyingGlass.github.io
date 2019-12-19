---
title: web常见漏洞
date: 2018-02-26 15:13:29
categories: 
- [Web开发]
---

*项目采用springboot作为web框架，涉及到常见的漏洞，特此记录*

---

#### 启用了不安全的HTTP 方法

- 描述：启用了不安全的 HTTP 方法， 可能会在 Web 服务器上上载、修改或删除 Web 页面、脚本和文件

- 原因：Web 服务器或应用程序服务器是以不安全的方式配置的

- 解决方案：

  - 方案一：

    如果采用springboot内嵌的tomcat容器，可以在application.yml或application.properties增加如下配置：

    ```java
    #解决不安全的HTTP方法漏洞  
    server.tomcat.port-header=HEAD,PUT,DELETE,OPTIONS,TRACE,COPY,SEARCH,PROPFIND   

    ```

  - 方案二：

    代码增加对tomcat的配置，代码如下：

    ```java
    @Configuration  
    public class TomcatConfig {  
      
        @Bean  
        public EmbeddedServletContainerFactory servletContainer() {  
            TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory() {// 1  
                protected void postProcessContext(Context context) {  
                    SecurityConstraint securityConstraint = new SecurityConstraint();  
                    securityConstraint.setUserConstraint("CONFIDENTIAL");  
                    SecurityCollection collection = new SecurityCollection();  
                    collection.addPattern("/*");  
                    collection.addMethod("HEAD");  
                    collection.addMethod("PUT");  
                    collection.addMethod("DELETE");  
                    collection.addMethod("OPTIONS");  
                    collection.addMethod("TRACE");  
                    collection.addMethod("COPY");  
                    collection.addMethod("SEARCH");  
                    collection.addMethod("PROPFIND");  
                    securityConstraint.addCollection(collection);  
                    context.addConstraint(securityConstraint);  
                }  
            };  
            return tomcat;  
        }  
    }  
    ```

- 测试：

  ```bash
  #使用curl测试：
  curl -v -X OPTIONS http://www.example.com/test/
  查看响应的 Allow: GET, HEAD, POST, PUT, DELETE, OPTIONS

  # 看是否能上载来判断攻击是否生效。
  curl -v -T test.html  http://www.example.com/test/test.html

  # 找一个存在的页面，如test2.html，如果删除成功，则攻击有效。
  curl -X DELETE http://www.example.com/test/test2.html
  ```

- 参考链接：

  - [验证启用了不安全的HTTP方法](http://www.cnblogs.com/qmfsun/p/6169641.html)
  - [spring boot使用内嵌的tomcat解决不安全的HTTP方法安全漏洞](http://blog.csdn.net/liuchuanhong1/article/details/76667343)


----


#### Apache JServ protocol service

  - 描述：Apache JServ协议(AJP)是一种二进制协议，可以从web服务器代理入站请求。位于web服务器后的应用服务器，不建议将AJP服务公开访问。如果AJP被错误配置，它可能允许攻击者访问内部资源。


  - 解决方案：

    - 找到tomcat的配置文件目录，路径如下${tomcat目录}/conf/server.xml
    - 注释如下行 `<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />`

  - 参考链接：

     - [stackoverflow禁用AJP服务](https://stackoverflow.com/questions/40262315/how-to-disable-apache-jserv-protocol-service)
     - [The Apache Tomcat Connector](https://tomcat.apache.org/connectors-doc/ajp/ajpv13a.html)

---

#### Host header attack

- 描述：在许多情况下，开发人员信任HTTP主机头值并使用它来生成链接、导入脚本，甚至生成密码重置与它的值的链接。这是一个非常糟糕的想法，因为HTTP主机头可以由攻击者控制，可以利用网络缓存中毒和滥用替代渠道的密码重置邮件。

-  解决方案：
     - 使用getServerName()代替getHeader("host");
     - 在Apache和nginx通过设置一个虚拟机来记录所有的非法Host header，或者在Apache和nginx里指定一个ServerName名单;
     - 同时，Apache开启UseCanonicalName选项

  ```bash
  # Apache在extra下的httpd-default.conf文件或者虚拟主机加入如下配置
  UseCanonicalName On
  # Nginx配置
  server_name example.com;
  ```

- 参考链接：

  - [被检测出有HTTP HOST头部攻击](https://www.oschina.net/question/1455558_2218206)
  - [host头攻击解决方法](http://blog.csdn.net/zzzzzyl/article/details/78583251)

  ---

#### Slow HTTP Denial of Service Attack

  - 描述：web服务器容易受到HTTP DoS(拒绝服务)攻击的影响，HTTP POST DoS攻击依赖于HTTP协议的设计要求。在处理之前完全接收到服务器，如果HTTP请求没有完成，或者传输速率非常低，服务器保持它的资源忙着等待其余的数据。如果服务器保存了太多的资源，这会造成拒绝服务。

  - 解决方案：

    配置防火墙 `iptables -A INPUT -p tcp --syn --dport 端口号 -m connlimit --connlimit-above 50 -j REJECT`

  - 参考链接

    - [how-to-protect-tomcat-7-against-slowloris-attack](https://security.stackexchange.com/questions/42618/how-to-protect-tomcat-7-against-slowloris-attack)


​    
