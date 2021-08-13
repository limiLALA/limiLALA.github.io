layout: post
title: 五、Spring Cloud之网关服务 zuul
author: QuellanAn
categories: 
  - springCloud
tags:
  - springCloud
  - java
  - zuul
---
# 前言
问：什么是网关服务？
答：给外部提供单一的访问接口，并做过滤和拦截处理的服务。

问：微服务架构中网关服务有什么作用?
答：我们微服务架构中项目众多，如果直接抛给外部，将会很容易引起调用错误并且大大增加了维护成本，所以我们需要提供单一访问接口，外部请求全部通过统一端口网关，然后在分发到不同的服务器。如果熟悉nginx 的同学想必就知道，其实就是nginx 反向代理的功能。

问：那为什么不使用nginx，而是使用zuul
答： nginx 确实可以实现网关的功能，但是我们同样的要维护nginx.conf 文件，如果项目够多，是很容易出问题的，使用zuul 的话，可以和eureka 天然的融合，使得管理维护起来更加方便。

下面我们就来看下怎么实现zuul 吧。

# pom.xml
我们创建一个zuul 的模块，pom.xml 文件中引入zuul 和erueka
```
	<dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

    </dependencies>
```

# 启动类
在启动类中我们加入@EnableZuulProxy 注解
将springBootApplication 注解换成@SpringCloudApplication 注解。
EnableZuulProxy 注解表示启动zuul 网关服务。
SpringCloudApplication 注解，我们来看下源码
```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootApplication
@EnableDiscoveryClient
@EnableCircuitBreaker
public @interface SpringCloudApplication {
}
```
可以看到包含的注解主要是SpringBootApplication、EnableDiscoveryClient、EnableCircuitBreaker 而这三个注解，我们前面都接触过的，SpringBootApplication就是Springboot项目启动的专用注解，EnableDiscoveryClient注解是将服务注册到服务中心，并发现服务的。EnableCircuitBreaker 是实现熔断处理的注解，所以说SpringCloudApplication 注解是对三个的一层封装，所以我们以后构建微服务的时候，使用SpringCloudApplication 注解会更方便。

# application.properties
接下来我们在配置问价中增加如下配置
```
server.port=9007
spring.application.name=zuul
eureka.client.serviceUrl.defaultZone=http://localhost:8000/eureka/

zuul.routes.test-a.path=/a/**
zuul.routes.test-a.service-id=ribbon-consumer
```
server.port和spring.application.name 用来指定项目启动的端口和项目在注册中心的名称，eureka.client.serviceUrl.defaultZone用来指定注册中心的地址。
zuul.routes.\*.path 和zuul.routes.\*.service-id 用来指定我们的目的项目。
比如我这里配置的ribbon-consumer 项目，将localhost:9007/a/** 转发到ribbon-consumer 对应的接口上。
 # 测试
 好了，我们现在就把网关配置好了。我们现在启动一下项目，启动如下几个项目进行测试吧就。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126143522555.png)
 其中EurekaServer是注册中心，ribbon-consumer 是服务消费者，ribbon-consumer 是服提供者，zuul 是网关。我们启动好这几个项目后，我们输入一下地址：
 ```
 http://localhost:9007/a/index
 ```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126145203341.png)
可以看到其实访问的是
```
 http://localhost:9003/index
```

# 默认映射
上面的可以看到，我们主要的配置就是在配置文件中配置好目标地址的路径。但是这样的话，和nginx 有什么区别呢，如果项目足够多配置起来还是会出错的，所以前面说zuul 和eureka 可以无缝连接，所以，这里zuul 做了一个默认映射，为所有注册到注册中心的服务提供了一个唯一对应的默认映射。怎么说呢，我们看一下服务中心的控制台。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126150434144.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
zuul 将eureka  中服务名作为映射前缀，比如
```
http://localhost:9007/ribbon-consumer/index
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126150807711.png)
可以看到，达到了一样的效果，ribbon-consumer 就是服务名。

# 请求过滤
前面我们讲了zuul 网关可以转发请求，但是它还有一个强大的功能，那就是请求过滤，我们知道具体项目中我们的接口会做安全限制，所以在具体的项目中会写过滤器和拦截器。在微服务项目中，我们既然提供了统一的网关服务，所以我们可以将安全校验和具体业务剥离出来，将安全校验放在zuul 网关中来统一处理，这样减少了冗余代码，也方便维护。
那么怎在zuul 中实现请求过滤呢？
继承ZuulFilter 类。

我们创建一个AccessFilter你类来继承ZuulFilter 。代码如下：
```
@Slf4j
public class AccessFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        RequestContext ctx=RequestContext.getCurrentContext();
        HttpServletRequest request=ctx.getRequest();
        String token=request.getParameter("token");

        if(token == null || !token.equals("123456")){
           log.info("token is error!");
           ctx.setSendZuulResponse(false);
           ctx.setResponseStatusCode(500);
           return "error";
        }
        log.info("token is ok");
        return null;
    }
}
```
直接将书上的解释拿出来了。主要的方法是run方法，获取请求中的request 和参数，对参数进行校验从而过滤。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126162523899.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
这里我就是对token 进行简单的校验，也就是说只有检验通过了才能访问目标接口。
昨晚上面这些还不够，还差一步，在项目zuul 服务启动的时候，需要将 AccessFilter  bean 注册服务中。所以我们在启动类中注入
```
    @Bean
    public AccessFilter accessFilter(){
        return new AccessFilter();
    }
```
好了，我们来启动一下项目看看。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126163227212.gif)
可以看到就可以起到拦截的作用。

# 番外
 到此为止，zuul 网关服务搭建好了，并运行一个非常简单的例子。
 
 代码上传到github：
 [https://github.com/QuellanAn/SpringCloud](https://github.com/QuellanAn/SpringCloud)

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)

[阅读原文](https://quellanan.blog.csdn.net/article/details/104036996)
