layout: post
title: 六、Spring Cloud之配置中心config
author: QuellanAn
categories: 
  - springCloud
tags:
  - springCloud
  - java
  - zuul
---
# 前言
前面我们讲了微服务的注册中心、负载均衡、熔断处理、网管服务。接下来我们讲配置中心，为什么要用配置中心呢？
其实我们接触一段时间就可以发现，我们的项目还是非常多的，每个项目都有自己的一份配置，这样管理起来就显得很不方便了，所以微服务中就提供了config 配置中心，将所有服务的配置都集中在config 服务中，这样方便统一管理。

怎么说呢？就好比每个项目都比如一个房间，每个房间都需要一把钥匙才能开启。而config 则是管理这些钥匙的，好比钥匙链，想要启动那个项目，就需要先从config中获取对应的钥匙，然后启动项目。

下面让我们来看下怎样部署一个config吧。配置中心分为服务端和客户端，和eureka 有点像，服务端是一个单独的项目，用来管理其他服务的配置，其他的服务就是客户端。

# 配置中心服务端
## 映入config-server 依赖
首先我们创建一个config 的子模块，用来做config 服务端，然后在pom.xml 文件中加入config-server依赖
```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
```

## 启动类
在启动类中，我们加入@EnableConfigServer 注解
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200128095839596.png)

## 配置文件
在配置文件中我们加入如下配置：
```
server.port=9008
spring.application.name=config-server

#使用本地属性文件
spring.profiles.active = native

#属性文件地址，只要指定文件夹的路径
spring.cloud.config.server.native.searchLocations=classpath:/properties
```
这里我们spring.profiles.active = native 表示你从本地加载配置文件，后面我们再从git 上加载配置文件。
如果不配置加载文件的地址，就会从src/main/resources 中加载文件。我这里配置了从properties文件夹下加载，所以在resources 文件夹下创建一个properties 文件夹。我们一eureka-server 服务为例。我们将这个项目的配置放到properties 文件夹下，并改名为
quellanan-eurekaserver.properties
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200128144254202.png)
# 客户端配置
好了，上面的服务端就已经配置好了，接下来我们来配置客户端。
## pom.xml
在pom.xml 文件中引入config 依赖
```
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
```

## bootstrap.properties
在resources 目录下创建一个 bootstrap.properties 文件，至于为什么要是这个而不是application.properties 文件，是由加载机制决定的，加载的时候会先加载bootstrap.properties 文件，然后加载application.properties ，
文件内容如下：
```
spring.application.name=quellanan
spring.cloud.config.profile=eurekaserver
spring.cloud.config.label=master
spring.cloud.config.uri=http://localhost:9008/
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200128145054916.png)
在本地也是一样的，spring.application.name和spring.cloud.config.profile拼起来就是文件名称。

# 测试
好了，服务端和客户端都配置好了，我们现在先将客户端的application.properties 文件删除掉，然后启动这两个项目，先启动config。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200128145547965.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
可以看到eureka-server 成功的从config 中加载到了配置文件并启动了项目。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200128150300869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

# 番外
 就这样简单的一个配置中心就已经实现了，最后说一个，既然我们有配置中心，那我们按在项目本身的application.properties 写的配置会加载么？答案是会加载的，至于比配置中心先加载还是后加载，我个人偏向于后加载，在application.properties 中写的属性可以覆盖配置中心中的属性。但是建议，依然使用了配置中心，就希望将所有的配置都放到配置中心里面，不要单独的在项目中新增配置，这样会增加管理的成本。
 
 代码上传到github：
 [https://github.com/QuellanAn/SpringCloud](https://github.com/QuellanAn/SpringCloud)

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)

[阅读原文](https://quellanan.blog.csdn.net/article/details/104093297)


