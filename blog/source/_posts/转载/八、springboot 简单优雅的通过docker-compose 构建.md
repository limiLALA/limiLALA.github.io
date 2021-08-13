layout: post
title: 八、springboot 简单优雅的通过docker-compose 构建
author: QuellanAn
categories: 
  - springBoot
tags:
  - springboot
  - java
---
# 前言
这个项目有一段时间没有更新了，不过我可没有偷懒哟，是偷偷准备了一个大招，现在是时候展示啦哈哈。

我们今天要做的，就是将我们的项目通过docker-compose 构建成镜像运行。为什么要这样做呢？比我我前面的这些教程，用到了mysql,如果你们想要运行我的程序，就必须在自己电脑上装mysql 数据库才行，也就是项目用依赖了哪些环境，都必须先将这些环境部署好才能运行项目，那我们要做的，只用安装docker和docke-compose ，然后运行就完事了，不用管什么环境的。

初一听，好像还行，但是根本没有接触docker-compose 怎么办？不要慌，一个专题带你飞，虽不能让你所向披靡，但也足你叱咤江湖啦
传送门：[docker自我修炼从0到1](https://blog.csdn.net/qq_27790011/article/category/9429389)

还有我查看项目发现竟然没有配置Redis，但是Redis 使用也是很广泛的，我之前的文章有详细的讲解springboot项目怎么使用redis。我这里只是简单的将他配上去确保架构的完整性，就不做更多的讲解，需要详细了解的可以参考[Redis--从零开始随笔](https://blog.csdn.net/qq_27790011/article/category/9187012)

好了，前面说了这么多无非是想表达这篇文章分两个大部分，部署Redis和通过docker-compose 搭建。

# 配置Redis
## 增加配置
在pom.xml文件中增加Redis的依赖
```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```
在application.properties 中存在redis 连接信息。
```
#配置redis
# Redis数据库索引（默认为0）
spring.redis.database=0  
# Redis服务器地址
spring.redis.host=192.168.252.53
# Redis服务器连接端口
spring.redis.port=6389 
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制） 默认 8
spring.redis.lettuce.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
spring.redis.lettuce.pool.max-wait=-1
# 连接池中的最大空闲连接 默认 8
spring.redis.lettuce.pool.max-idle=8
# 连接池中的最小空闲连接 默认 0
spring.redis.lettuce.pool.min-idle=0
```

## 简单使用
配置好了，我们还是说一下简单使用，这些在之前也有讲，代码也是差不多的。我们在controller 层创建一个 redis 包，在Redis包下创建一个RedisController类。
代码如下：
```
@RestController
@RequestMapping("/redis")
@Slf4j
public class RedisController {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @RequestMapping(value = "/add",method = RequestMethod.GET)
    public String add(@RequestParam(value="key")String key,@RequestParam(value = "value") String value){
        ValueOperations ops=stringRedisTemplate.opsForValue();
        ops.set(key,value);
        return "success";
    }

    @RequestMapping(value = "/get",method = RequestMethod.GET)
    public String get(@RequestParam(value = "key")String key){
        ValueOperations ops=stringRedisTemplate.opsForValue();
        return (String) ops.get(key);
    }

}
```
![file](https://img-blog.csdnimg.cn/20191111184510325.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

好了项目整合Redis 就这么多了，至于测试我们待会后面部署好了一起测试。接下来开始重头戏。

# docker-compose 部署
我们现在有一个springboot项目，现在怎么构建成一个镜像放在服务器上运行呢？
我们一步步来，首先是增加配置。
## pom.xml
还是我们熟悉的pom.xml 我们需要在pom.xml 中 build-->plugins 中增加配置：
```
<!-- Docker -->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>1.0.0</version>
                <!-- 将插件绑定在某个phase执行 -->
                <executions>
                    <execution>
                        <id>build-image</id>
                        <!-- 用户只需执行mvn package ，就会自动执行mvn docker:build -->
                        <phase>package</phase>
                        <goals>
                            <goal>build</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <!-- 指定生成的镜像名 -->
                    <imageName>${docker.image.prefix}/${project.artifactId}:${project.version}</imageName>
                    <!-- 指定标签 -->
                    <imageTags>
                        <imageTag>${project.version}</imageTag>
                    </imageTags>
                    <!-- 指定 Dockerfile 路径 -->
                    <dockerDirectory>src/main/docker</dockerDirectory>
                    <!-- 指定远程 docker api地址 -->
                    <dockerHost>http://127.0.0.1:2375</dockerHost>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <!-- jar包所在的路径此处配置的对应target目录 -->
                            <directory>${project.build.directory}</directory>
                            <!-- 需要包含的jar包,这里对应的是Dockerfile中添加的文件名　-->
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
            </plugin>
```
上面都有注释没有什么好讲的，其中指定生成镜像名的${docker.image.prefix} 的值在properties 中配置。
![file](https://img-blog.csdnimg.cn/20191111184511576.jpeg)

## Dockerfile
我们在项目的src-->main 创建一个docker 包，包下创建一个Dockerfile 问价 ，内容如下：
```
FROM java:8
VOLUME /tmp
ADD zlflovemm-1.0.0.jar zlflovemm-1.0.0.jar
ENTRYPOINT ["java","-jar","/zlflovemm-1.0.0.jar"]
```


## mvn package
接下来我们使用maven package 打包，就可以将项目打包成镜像并发送到我们配置的服务器上。可以看到我们的镜像已经到我们的服务器了。
![file](https://img-blog.csdnimg.cn/201911111845128.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

## docker-compose.yml
我们现在项目镜像有了，现在需要通过docker-compose.yml 将项目，mysql .redis 都管理起来。
我们创建一个docker-compose.yml 内容如下：
```
version: "3"

services:
  webapp:
    image: quellanan/zlflovemm:1.0.0
    ports:
      - "9001:9090"
    volumes:
      - "/data"
    depends_on: 
      - redis
      - mysql

  redis:
    image: redis:latest
    restart: always
    ports:
      - "6389:6379"
    volumes:
      - /redis/redis.conf:/etc/redis/redis.conf
    command: redis-server /etc/redis/redis.conf

  mysql:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
      MYSQL_USER: 'root'
      MYSQL_PASS: '123456'
    ports:
       - "3306:3306"
    volumes:
       - "./db:/var/lib/mysql"
       - "./conf/my.cnf:/etc/my.cnf"
       - "./init:/docker-entrypoint-initdb.d/"

```

webapp 和Redis 配置我就不说了，上一篇文章已经说了，这里我们说一下mysql 的配置。
image:mysql:laster 我们选择最新的版本的mysql 镜像。
```
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
      MYSQL_USER: 'root'
      MYSQL_PASS: '123456'
```
environment 配置的是mysql 的root 用户密码普通的用户及密码。
配置我们自定义的mysql 配置，如果使用的默认的，这里的数据卷其实可以不用指定。
```
volumes:
       - "./db:/var/lib/mysql"
       - "./conf/my.cnf:/etc/my.cnf"
       - "./init:/docker-entrypoint-initdb.d/"
```
我们创建好了docker-compose 后，在同级目录下执行
```
docker-compose up
```
![file](https://img-blog.csdnimg.cn/20191111184512418.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
就可以看到镜像容器已经启动了。项目包含三个镜像,现在我们来验证一下项目正常启动没有。

## 测试
由于我们使用了docker镜像的mysql  。所以之前的项目使用的数据库需要重新执行一次，我们代码中有，我就不贴出来了。项目的接口我们现在调一下看看。
```
# 检测项目正常启动没有
http://192.168.252.53:9001/zlflovemm/
```
![file](https://img-blog.csdnimg.cn/20191111184512694.jpeg)

```
# 检测mysql 是否正常
http://192.168.252.53:9001/zlflovemm/user/list
```
![file](https://img-blog.csdnimg.cn/20191111184511594.jpeg)

```
# 检测Redis 是否正常
http://192.168.252.53:9001/zlflovemm/redis/add?key=a&value=123
http://192.168.252.53:9001/zlflovemm/redis/get?key=a
```
![file](https://img-blog.csdnimg.cn/20191111184514254.jpeg)


# 番外
到此为止，将springboot项目构建成镜像完成啦，感觉整篇文件将的比较粗糙，这篇主要是整合，详细的都在前面的博客已经讲了，所以这里就没有再讲了。


好了，就说这么多啦
代码上传到github：
https://github.com/QuellanAn/zlflovemm

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)

























