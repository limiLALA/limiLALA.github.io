layout: post
title: centOS7 安装nginx
author: QuellanAn
categories: 
  - nginx
tags:
  - nginx
  - centos
---

# 前言
在家呆的时间很长，没有linux 服务器，所以就在腾讯云上买了一个云服务器折腾一下，重置秘密重装系统和用远程连接什么的就不讲了，都比较简单，在控制台上都可以操作。我安装后，想搭建一个nginx试试。

自己也是采坑一路，虽然以前也在Ubuntu上安装nginx 的，但是还是和centOS 上有些不一样的。

如果是在腾讯云上买的服务器，其实可以一步到位
```
yum install nginx
```
但是我是下载的nginx 安装包，上传到服务器，安装的。

# 下载
```
https://nginx.org/download/
```
想要各个什么版本，自己选，我这里选的是1.16.2
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022313252156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
# 上传&解压
因为服务器上什么都没有，要上传安装包的，需要先安装lrzsz.我这里有rz 明了上传的。
```
yum install lrzsz
```
安装好之后，就将安装包解压
```
 tar -zxvf nginx-1.6.2.tar.gz
```
解压后如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223133024492.png)

# 安装
刚开始我直接运行
```
cd nginx-1.6.2
./configure 
make && make install
```
会报错，就是安装不成功，提示没有什么包，最后想到 nginx 是c语言编写的，需要编译的话，可能是没有c语言环境，所以需要安装环境
```
yum install -y gcc gcc-c++
```
最后才安装成功。

# 操作
安装成功后，怎么启动呢，安装到什么位置了呢？
默认安装到了这个目录下，我们可以来到这个目录下找到他们。
```
/usr/local/nginx
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223133625951.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
可以看到主要就 conf 、logs、sbin 这三个目录啦。conf中的nginx.conf 就是我们主要配置的配置文件，这个我们稍候说，logs 就是nginx 的日志，对我们查看接口请求情况很有帮助。sbin 就是nginx 的命令目录，可以看到就一个nginx 的命令。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223133844262.png)
## 启动
那我们怎么启动nginx 呢？
```
sudo /usr/local/nginx/sbin/nginx
```

## 检查配置文件是否正确
```
cd  /usr/local/nginx/sbin/
./nginx -t
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223134102319.png)
如上表示成功。

## 重启
```
./nginx -s reload
```

## 停止
```
./nginx -s stop
```
当然我们可以通过查看进程号，来杀死进程。
查看nginx 的进程
```
ps -ef|grep nginx
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022313433980.png)

# 权限问题
在nginx 已经搭建好之后，我网上找了一个静态html 文件试了一下，结果配置好路劲后发现，一直访问不了，界面上报403f.。看了一下nginx 日志发现如下错误
```
/root/love5/index.html" failed (13: Permission denied), client: xx.xx.xx.xx, server: localhost, request: "GET /index/index.html HTTP/1.1", host: "xx.xx.xx.xx:8080"
```
造成这个原因是因为我们运行nginx 的时候权限不是root 权限，所以需要在nginx.conf 做如下修改
```
user  root;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223135555930.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
最后总算是通啦，在本地浏览器上成功的显示了静态文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223135707313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
网上找的资源么，但是女朋友不知道，把做好的这个给女朋友看，女朋友以为是我自己做的送给她的，着实开心了一下哈哈。

# 番外
安装nginx 不是终极目的，还是想把项目放到服务器上跑的，由于自己学的都是Java，所以就在服务器上安装jdk ，但是自带的都是openjdk ，而我们本地的用的都是Oracle jdk.所以想安装oracle jdk 。但是官网上不知道怎么回事，一直下载不下。
官网：https://www.oracle.com/java/technologies/javase-jdk8-downloads.html
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223140418865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
提示我需要勾选许可，但是没有地方勾选，把我气得不轻，没办法我就安装的open jdk。然后在本地打包一个测试spring boot jar 包，看在服务器上能否运行。发现是可以运行的。看来在服务器上安装open jdk 也是可以的。
