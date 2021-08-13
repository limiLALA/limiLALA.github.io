layout: post
title: 四、Spring Cloud之熔断处理 Hystrix
author: QuellanAn
categories: 
  - springCloud
tags:
  - springCloud
  - java
  - Hystrix
---
# 前言
熔断处理什么呢？在微服务项目中，有很多的服务，在服务消费者调用服务提供者的时候可能会出现网络异常或者请求超时或者阻塞等等一系列问题，不过不进行处理的话，就可能导致，长时间等待，进程阻塞，最终导致系统瘫痪。

所以就有了熔断处理，当服务提供者的接口不能访问或者异常异常时，进行降级处理，服务消费者能够正常的处理返回特定是数据。从而达到容灾的目的。

看了一下Hystrix ，其实有很多东西，我们就先来看看Hystrix 的简单使用，由于上节我们也说了Fegin 中集成了Hystrix  和ribbon .所以我们就直接使用Fegin 来实现一个简单的熔断处理的操作。

# pom.xml
其实我们完全可以在上一节的ribbon-consumer 的基础上来，我这里为了保持独立性，所以copy 了一份ribbon-consumer 改成hystrix-consumer。
pom文件引入fegin 的依赖
```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```
在主类中增加@EnableFeignClients，其实这里仅仅使用hystrix 的话，可以使用@EnableCircuitBreaker。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115165813644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
# application.properties
配置文件中开启熔断处理,将feign.hystrix.enabled设置为tue
```
#开启熔断处理
feign.hystrix.enabled=true
```

# fallback
重点来啦，我们上一节在HelloService 中调服务提供者的接口，所以要做熔断处理的话，就需要在这里进行降级处理，就需要写一个降级处理的方法，如果服务提供者的接口不通，就调用这个方法，返回给客户端。
所以我们在server层创建一个fallback 文件夹，在fallback 包下创建HelloServiceFallback类实现 HelloService。内容如下：
```
@Component
public class HelloServiceFallback implements HelloService {
    @Override
    public String hello() {
        return "hello error";
    }

    @Override
    public String hello2(@RequestParam(value = "name") String name) {
        return "hello2 error";
    }

    @Override
    public String hello3(@RequestParam(value = "name") String name, @RequestParam(value = "age") String age) {
        return "hello3 error";
    }
    
    @Override
    public String hello4(@RequestBody Map<String, Object> parms) {
        return "hello4 error";
    }
}
```
注意给整个类加上@Component 注解，然后就是实现HelloService 中的方法啦。

然后我们需要在HelloService 中的@FeignClient 注解做修改
```
@FeignClient(name = "ribbon-provider",fallback = HelloServiceFallback.class)
```
name 用来指定服务提供者的服务名，fallback 用来指定降级处理的类。这里就是我们刚刚写HelloServiceFallback。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115171313707.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
好了我们来启动项目测试一下。启动这三个项目，分别是注册中心，服务提供者hystrix-consumer，服务消费者ribbon-provider-9004
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115172641776.png)
然后我们在界面输入
```
http://localhost:9006/fegin
```
然后关闭服务提供者，再调接口试试。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115173213537.gif)
可以看到服务提供者的接口断掉之前和之后返回的结果是不一样的。说明我们的熔断处理是生效的啦。

# 番外
今天就说这么多吧，就先学习一下Hystrix 的基本使用，其他的一些功能，我们后续再说


 代码上传到github：
 [https://github.com/QuellanAn/springcloud](https://github.com/QuellanAn/springcloud)

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
