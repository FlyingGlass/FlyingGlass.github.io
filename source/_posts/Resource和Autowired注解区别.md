---
title: Resource和Autowired注解区别
date: 2018-03-13 14:29:18
categories: 
- [Web开发]
---

*最近在使用Mybatis处理通用Mapper封装时，使用@Resource进行注入bean时，遇到一些问题，本文特此记录：*

@Autowired默认按照类型进行装配，要求依赖对象必须存在，如果允许为null，可以设置required属性为false；可以结合@Qualifier注解使用名称装配。

@Resource主要有两种属性type和name。如果使用name属性，则使用byName进行注入；如果使用type属性，则使用byType进行注入；如果注入策略既不制定name也不指定type属性，将通过反射机制使用byName进行注入。其中@Resource的装配顺序如下：

- 指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常
- 指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常
- 指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
- 既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配

在泛型装配时，@Resouce和@Autowired的区别：

```java
/**
* 针对@Resouce会出现无法装配的情形
* @Autowired按照类型装配，在装配泛型是按照T的真实类型装配
*/
public abstract class AbstractService<T> implements IService<T> {
    @Autowired
    protected IMapper<T> IMapper;
}
```

#### 参考链接

- [Spring注解@Autowired和@Resource区别](http://blog.51cto.com/12941821/2063203)
- [Spring @Autowired+@Qualifier与@Resource的区别](http://bluerhino.github.io/2016/06/02/Spring%20@Autowired+@Qualifier%E4%B8%8E@Resource%E7%9A%84%E5%8C%BA%E5%88%AB/)

