layout: post
title: 三、Spring Cloud之软负载均衡 Ribbon
author: QuellanAn
categories: 
  - springCloud
tags:
  - springCloud
  - java
  - Ribbon
---

# 前言
上一节我们已经学习了Eureka 注册中心，其实我们也使用到了Ribbon ,只是当时我们没有细讲，所以我们现在一起来学习一下Ribbon。

# 什么是Ribbon
之前接触到的负载均衡都是硬负载均衡，什么是硬负载均衡呢？硬负载均衡就是在以往的大型系统中，会有单独一套系统来负责负载均衡策略，我们所以的请求都会先走到负载均衡的系统上，进行分配到不同的服务器处理。
比如我们熟悉的nginx 。其实就可以算作一个负载均衡的系统，客户端请求的接口会先通过nginx 的负载均衡策略分配到不同的服务器上。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114094757462.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
那Ribbon 不是这样的吗？那又是怎样的呢？
Ribbon 是和 Eureka 一样是Netflix 推出的开源产品，它可以和Eureka 完成无缝结合，Ribbon 主要实现客户端负载均衡。
那什么是客户端负载均衡呢？
就是在客户端请求的时候，就通过均衡策略将请求分发到不同的服务器上，如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114100801286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
这个图是根据上节的Eureka 的架构图改编来的，主要的流程还是没有变，服务消费者和服务提供者都会注册到服务中心，然后服务消费者会从服务中心获取可用实例列表 ，这里就会通过负载均衡策略选择其中一个实例进行访问。

好了我们来用代码来看一下。
# demo
我们为了简化，注册中心服务端，我们还是用上节的单节点。怎么配置我不说了。然后我们新建一个module 用来做服务提供者，其实也可以用上一节的服务提供者，但是我怕揉在一起不好，所以就全都分开了，不过很多代码都是一样的。
## 服务提供者
我们新建一个模块后pom.xml 文件如下：
```
<parent>
		<groupId>cn.quellanan</groupId>
		<artifactId>SpringCloud</artifactId>
		<version>1.0.0</version>
	</parent>

	<groupId>com.quellanan.springcloud</groupId>
	<artifactId>ribbon-provider-9004</artifactId>
	<version>1.0.0</version>
	<name>ribbon-provider-9004</name>
	<description>ribbon-provider-9004 服务提供者</description>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
	</dependencies>
```
主要就是引入了eureka-client 可以注册到注册中心去，然后启动类加上@EnableEurekaClient 注解
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114112831477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
在配置文件中加上配置如下：
```
server.port=9004
spring.application.name=ribbon-provider
eureka.client.service-url.defaultZone=http://localhost:8000/eureka/
```
我们在创建一个测试类：HelloController
```
@RestController
@Slf4j
public class HelloController {

    @Value("${server.port}")
    private String port;

    @RequestMapping("/hello")
    public String hello(){
        log.info(port);
        return "hello "+port;
    }
}
```
这样我们一个服务提供者就弄好了，为了看出负载均衡的效果，我们还需要创建一个一样的服务提供者。或者不同的端口启动。我们将端口改为9005.其他的和上面一样。

## 服务消费者
服务提供者有了，我们再来创建一个服务消费者的模块。pom.xml 较服务提供者就多了一个ribbon 的依赖
```
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>

```

然后启动类中加上@EnableEurekaClient 和 RestTemplate 
```
@SpringBootApplication
@EnableEurekaClient
public class RibbonConsumerApplication {

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

    public static void main(String[] args) {
        SpringApplication.run(RibbonConsumerApplication.class, args);
    }

}
```
 @LoadBalanced 注解就是来实现客户端负载均衡的。

配置文件中加入如下配置：
```
server.port=9003
#服务名，在注册时所用
spring.application.name=ribbon-consumer
eureka.client.serviceUrl.defaultZone=http://localhost:8000/eureka/
```
最后我们来写一个调用服务提供者的接口，创建一个IndexController类，内容如下
```
@RestController
public class IndexController {
    private static final String applicationName = "ribbon-provider";

    @Autowired
    private RestTemplate restTemplate;
    @RequestMapping("/index")
    public String getHello(){
        String url = "http://"+ applicationName +"/hello";
        return  restTemplate.getForObject(url,String.class);
    }
}
```
## 测试
我们现在来启动服务中心，两个服务提供者，一个服务消费者。启动之后我们输入：
```
http://localhost:8000/
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114131431914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
重点看下ribbon-provider 有两个端口，分别对应的我们的两个服务提供者。他们的appliaction.name是相同的。
再来调下面接口看看
```
http://localhost:9003/index
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020011413204342.gif)
可以看到每次调用访问了不同的服务提供者，两个服务端提供者是轮寻调用的。从而实现客户端的负载均衡

# RestTemplate 
上面说的负载均衡，其实还是RestTemplate 对象加上@LoadBalanced来实现的。并且前面只是简单的调用，没有涉及参数和请求方式，接下来我们看看常见的请求方式和有参数的调用。
## Get 请求
其实我们之前写的就是get 请求的方式，我们在来写一个有参数的请求
```
@RequestMapping("index2")
    public String getHello2(){
        String url = "http://"+ applicationName +"/hello2?name={1}";
        return  restTemplate.getForObject(url,String.class,"quellanan");
    }
```
可以看到url 中请求的参数有占位符代替，getForObject或者getForEntity的第三个参数就是我们实际传的参数。这里说明一下getForObject 是直接获取返回的内容，而getForEntity返回的是一个http对象，包含相应状态码，想要回去内容需要getForEntity().getBody() 才行。

那如果多个参数的呢？
多个参数的常用的有两种方式，一个是和上面一样，直接在后面加参数就好了如下：
```
@RequestMapping("index3")
    public String getHello3(){
        //多个参数拼接
        String url = "http://"+ applicationName +"/hello3?name={1}&age={2}";
        return  restTemplate.getForObject(url,String.class,"quellanan","18");
    }
```

还有一种方式就是将参数封装到map 中，传过去。一样的也可以解析
```
@RequestMapping("index4")
    public String getHello4(){
        //多参数组装
        Map<String,String> parms=new HashMap<>();
        parms.put("name","quellanan");
        parms.put("age","18");
        String url = "http://"+ applicationName +"/hello3?name={name}&age={age}";
        return  restTemplate.getForObject(url,String.class,parms);
    }
```

我们在提供者中写两个方法便于测试
```
 @RequestMapping("/hello2")
    public String hello2(@RequestParam("name") String name){
        log.info(name);
        return "hello "+name+port;
    }
    @RequestMapping("/hello3")
    public String hello3(@RequestParam("name") String name,@RequestParam("age") String age){
        log.info(name+age);
        return "hello "+name+age+port;
    }
```
我们启动来看下结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114140809718.gif)
可以看到参数是传递成功的啦。

## Post 请求
 
post 请求和get 请求差不多，我这里就将参数封装到map中了
postForEntity
```
    @RequestMapping("index6")
    public String getHello6(){
        //postForEntity
        JSONObject params=new JSONObject();
        params.put("name","quellanan");
        params.put("age","18");
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        HttpEntity request = new HttpEntity(params.toJSONString(), headers);
        String url = "http://"+ applicationName +"/hello4";
        return  restTemplate.postForEntity(url,request,String.class).getBody();
    }
```
postForObject
```
@RequestMapping("index7")
    public String getHello7(){
        //postForObject
        JSONObject params=new JSONObject();
        params.put("name","quellanan");
        params.put("age","18");
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        HttpEntity request = new HttpEntity(params.toJSONString(), headers);
        String url = "http://"+ applicationName +"/hello4";
        return  restTemplate.postForObject(url,params,String.class);
    }
```
主要是先将参数封装在JSONObject 中，然后设置HttpHeaders 和HttpEntity ，然后请求。
我们采用的application/json 的格式，我们在服务提供者中加一个方法。用来接收json格式的参数。
```
    @RequestMapping("/hello4")
    public String hello4(@RequestBody Map<String, Object> parms){
        return "hello "+parms.get("name")+parms.get("age")+port;
    }
```
现在我们启动看下效果。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114182744219.gif)
证明都是可以正常传递的。

# Feign 简化微服务调用
上面我们可以看到，我们进行微服务调用，不管是使用get 或者post 方法，带有参数过多就会导致代码变得很臃肿，所以我们就可以使用同样是netflix 推出的Feign 来简化微服务调用，Feign 结合了Ribbon 以及Hystrix.Ribbon的功能它都有，并且还进行了封装，更加方便我们使用。所以我们使用的时候，只用引入 Feign 的依赖就可以。

那我们现在对上面的这些进行调整一下。
## pom.xml
在我们的ribbon-consumer 的pom 文件中删除ribbon 的依赖，增加feign 的依赖。
```
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
      	</dependency>
```

## 增加@EnableFeignClients
在启动类中增加@EnableFeignClients 注解
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114195008175.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
在启动类中增加了@EnableFeignClients 注解，就可以在项目中使用Fegin 啦，那我们怎么使用呢？
其实还要使用@FeignClient 注解，这个注解是在具体的应用中注入的，用来指定服务提供者的服务名称。并且这个注解只能作用 interface 上。
前面我们调用服务提供者的接口需要写url,参数，返回类型等等非常的繁琐，所以Fegin 就帮我们进行了简化，让我们调用服务提供者的接口，可以像自身调用一样，非常放方便。

## HelloService 
我们来创建一个HelloService  的接口。内容如下
```
@FeignClient("ribbon-provider")
public interface HelloService {

    @RequestMapping("/hello")
    public String hello();

    @RequestMapping("/hello2")
    public String hello2(@RequestParam(value = "name") String name);

    @RequestMapping("/hello3")
    public String hello3(@RequestParam(value = "name") String name,@RequestParam(value = "age") String age);

    @RequestMapping("/hello4")
    public String hello4(@RequestBody Map<String, Object> parms);

}

```
可以看到，上面的接口内容主要是针对服务提供者暴露出的几个接口进行调用。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114195917363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
对比服务消费者中的接口，和服务提供者中的接口，可以发现其实是服务提供者中的接口对消费者中的HelloService 的实现。这个待会再说。从HelloService 中我们可以看到，我们调用服务提供者的接口一样的采用@RequestMapping，@RequestParam，@RequestBody来操作，这样更加简洁和方便。

## FeginController
我们再创建一个FeginController 来测试一下，内容如下：
```

@RestController
public class FeginController {

    @Autowired
    public HelloService helloService;


    @RequestMapping("/fegin")
    public String getHello(){
        return  helloService.hello();
    }

    @RequestMapping("/fegin2")
    public String getHello2(){
        String name="quellanan";
        return  helloService.hello2(name);
    }


    @RequestMapping("/fegin3")
    public String getHello3(){
        String name="quellanan";
        String age="18";
        return  helloService.hello3(name,age);
    }

    @RequestMapping("/fegin4")
    public String getHello4(){
        Map<String, Object> parms=new HashMap<>();
        parms.put("name","quellanan");
        parms.put("age","18");
        return  helloService.hello4(parms);
    }
}
```
可以看到就是普通的controller层调用service 层。

## 测试
好了我们现在来测试一下。
分别输入如下地址来看看效果：
```
http://localhost:9003/fegin
http://localhost:9003/fegin2
http://localhost:9003/fegin3
http://localhost:9003/fegin4
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200114200759478.gif)

## 继承
好了，来说最后一个问题，刚刚上面我们说了服务消费者中的HelloService 和服务提供者的HelloController很像，感觉像是HelloController 实现了HelloService 。不错，当我们正式开发的时候，会发现接口调用非常多，并且也很复杂，如果按照上面的方式来的话，会存在很多的重复代码且很容易出错，所以我们可以将服务调用单独提取成一个模块么，然后分别在服务提供者和服务消费者中引入其依赖，然后在消费中的HelloService 继承其对应的接口。而在服务提供者中实现其对应的接口。当然@FeignClient还是在服务消费者之中的。

这里只是提供了一种思路，没有给出实现方式，感兴趣的可以看看《Spring cloud 微服务实战》，也可以和我讨论下嘿嘿。

# 番外
总算是写完了，算是对ribbon 和fegin 有了一些了解，最起码现在可以使用他们，并将其使用到到项目中没有什么问题啦。

 代码上传到github：
 [https://github.com/QuellanAn/springcloud](https://github.com/QuellanAn/springcloud)

后续加油♡


很荣幸，今年参加了CSDN博客之星评选活动，帮忙投下票，可以投5票 ，谢谢您

[CSDN博客之星评选活动](http://m234140.nofollow.ax.mvote.cn/opage/86af3df8-3938-2a10-a907-7200157f6d99.html)


最后啦，
欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
