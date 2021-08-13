layout: post
title: kafka也没那么难--kafka的安装与简单使用
author: QuellanAn
categories: 
  - springCloud
tags:
  - springCloud
  - java
  - kafka
---

# 前言
前短时间在腾讯云上买了一个linux 服务器，决心把kafka这一模快的知识补充起来啦。所以就搞起来。


# 安装
安装算是比较简单的，可以直接用wget 下载，也可以将安装包下载下来，上传到服务器上，都是一样的。
kafka 安装包网址：
```
http://mirror.bit.edu.cn/apache/kafka
```
我选择的版本2.4.0：
```
wget http://mirror.bit.edu.cn/apache/kafka/2.4.0/kafka_2.13-2.4.0.tgz 
```

下载zookeeper ,这个暂时不用也可以，kafka 中自带了zookeeper，暂时学习也可以直接用。
zookeeper 安装包网址：
```
http://mirror.bit.edu.cn/apache/zookeeper
```
我这里选择的是3.5.6
```
http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.6/apache-zookeeper-3.5.6.tar.gz  
```
将下载好了压缩包，解压就安装成功可以用了，可以直接运行的。
```
tar -zxvf apache-zookeeper-3.5.6.tar.gz  
tar -zxvf kafka_2.13-2.4.0.tgz
```
这里需要说明的是，需要jdk 环境，我直接装的openjdk8,一样可以用。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224164300595.png)

# 安装包文件
我们解压后的kafka 进文件夹，如下目录。我们主要用到的就是bin和config 这两个目录。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224164818481.png)
## bin 目录
bin 文件夹下都是一些执行的命令文件，我们暂时会用到图中圈出的这几个命令。具体用法后面再讲，先说说这几个分别干啥。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224165257866.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
1是消费者连接topic 消费消息的命令。
2是生产者连接topic 推送消息的命令。
3分别是启动和停止kafka服务的。
4是操作topic 的指令，比如查看topic 列表或者删除topic
5分别是启动和停止zookeeperd服务，这里的zookeeper 是kafka 自带的。

## config 目录
我们再来看看config 里面的文件。我们主要就用到server.propertie 和zookeeper.properties
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022417164730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
### server.propertie 
server.propertie 是启动kafka 时加载的配置文件。点击去看看，基本要改的就是下面这两个地方。
每一个broker都需要一个标识符，使用broker.id来表示。它的默认值是0，也可以被设置成任意其它整数。这个值在整个kafka集群里必须是唯一的。这个值可以任意选定。我这里设置的broker.id=1

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224172818867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
还有kafka 默认启动服务的默认端口是9092.如果我们想要修改的话，就需要在server.propertie 中加上
```
port = 9093
```
当然改了这里，还得改其他对应配置文件的连接。这里是网上截图的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225101804919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
### zookeeper.properties
zookeeper.connect 主要配置 zookeeper的链接。如果我们在其他地方安装的zookeeper ,就需要修改这里的配置了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224171905610.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
zookeeper.properties 文件是启动kafka 自带的zookeeper 时加载的配置。
里面的配置就比较少了，主要是
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224174917296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
libs 文件夹是kafka 运行依赖的jar 包，我们可以不用管，logs是kafka 运行产生的日志，我们排查问题时用到，暂时也不用管。

# 简单操作
## zookeeper 
我们按顺序来，因为kafka 启动要依赖zookeeper服务。所以我们来看zookeeper的命令。
```
# 启动zookeeper 服务
bin/zookeeper-server-start.sh ./config/zookeeper.properties

# 停止zookeeper 服务
bin/zookeeper-server-stop.sh
```

## kafka 服务
启动好zookeeper 后，我们来启动kafka 服务。
```
bin/kafka-server-start.sh ./config/server.properties
```
关闭kafka 服务命令
```
bin/kafka-server-stop.sh 
```

## topic
启动好kafka 服务后，我们就可以创建topic 啦。创建topic的命令
```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test1
```
这里可以看到创建topic 的命令算是比较复杂的， --zookeeper localhost:2181是指定zookeeper 服务。-replication-factor是指创建分区。
partitions 是创建备份。test1是topic 名称。

我们在创建一个tpoic test2. 然后查看topic 列表,需要指定zookeeper 连接
```
bin/kafka-topics.sh --list --zookeeper localhost:2181
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022511055970.png)
删除一个topic,需要指定zookeeper 和删除的topic
```
bin/kafka-topics.sh --delete --topic quellanan --zookeeper localhost:2181
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225111026371.png)

## producer

我们已经创建了topic 。接下来我们可以让生产者推送消息到这个topic 上。
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test1
```
--broker-list localhost:9092 连接上指定的kafka 服务器。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022513250510.png)
## consumer
生产者生产了消息，接下来就需要消费者消费消息啦。
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test1 --from-beginning
```
--bootstrap-server localhost:9092  是连接特定的kafka 服务
--from-beginning 读取历史未消费的数据。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225133001323.png)

# Springboot整合使用kafka
上面哪些都是在服务器上操作的，所以接下来我们需要在我们代码中使用kafka ,主要是推送消息和消费消息。

这里由于我们kafka 部署在服务器上，不是我们本地，所以需要kafka 配置文件中设置远程访问。主要修改config/server.properties
```
listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://192.168.1.51:9092
zookeeper.connect=192.168.1.51:2181
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225173945793.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
做如上修改就可以在远程访问啦。

## produecer
准备工作做好了，我们现在创建一个springboot 项目 ，名为kafka-producer，作为kafka 生产者。我这里Springboot 版本是最新的2.2.4。
在pom.xml 文件中引入kafka 依赖。
```
<!--引入Kafka-->
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
```
在配置文件中，配置kafka 连接
```
server.port=9000
spring.kafka.bootstrap-servers=192.168.1.51:9092
```
然后我们创建一个推动消息的接口。KafkaProducerController
内容如下：
```
@RestController
public class KafkaProducerController {
    @Resource
    private KafkaTemplate<String,String> KafkaTemplate;

    @RequestMapping("/send")
    public String sendMsg(@RequestParam(value = "topic")String topic,@RequestParam(value = "msg")String msg){
        KafkaTemplate.send(topic,msg);
        return "success";
    }
}
```
主要是通过KafkaTemplate 向topic 推送消息的。这样就可以了，我们启动项目，调接口
```
http://localhost:9000/send?topic=test3&msg=hello world
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022517592263.png)
控制台可以看到连接kafka 的信息。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225180004487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
并可以看到推送的是时间和commitID
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225180038546.png)

## consumer
接下来我们就需要创建一个kafka 消费者来监控topic ，如果有新的消息就接收。
pom.xml 文件和配置文件连接kafka 服务器都是一样的。
我们创建一个类KafkaConsumer。
内容如下
```
@Component
@Slf4j
public class KafkaConsumer {
    @KafkaListener(groupId = "test-group",topics = "test3")
    public void listen(String msg){
        log.info("接收消息："+msg);
    }

}

```
可以看到主要是使用KafkaListener的注解，groupId 随意写。topics 是我们需要监听的topic。至于listen方法的参数，看我们推送的是什么类型，就接收什么类型。
好了，我们启动消费者进行监听。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225180717810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
可以看到可以接收生产者推送的消息了。

这些都是比较简单的使用，我们后续接着学习kafka的内容吧，一起加油！
