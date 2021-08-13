layout: post
title: 升级nginx ,为nginx配置https证书
author: QuellanAn
categories: 
  - nginx
tags:
  - nginx
  - https
  - ssl
---

# 前言
买了服务器和域名后真的是为所欲为，发现自己的网站总是提示不安全，所以就想着要弄一个证书。刚好腾讯云上有免费申请的证书，所以就弄了一个。

#  申请证书
我的证书是在腾讯云上申请的，很快也很方便中，具体怎么操作，就不说了。申请好之后下载。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200303172515647.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
解压后获取如下文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200303175735195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
# 升级nginx
我发现我服务器上的nginx 上次竟然安装的是1.6.2 版本太低了，不支持ssl.支持ssl 需要nginx 1.10.1 以上。所以我直接将版本升级到了1.16.1了。
升级也很简单，将下载的安装包解压。
```
tar -zxvf nginx-1.16.1.tar.gz
cd nginx-1.16.1
#重新添加这个ssl模块
./configure --with-http_ssl_module
make
```
不用make install .将nginx 命令copy 过去就可以了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200303180520645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
```
cp objs/nginx /usr/local/nginx/sbin/nginx
```
升级成功。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200304105534497.png)

# 配置证书
我们在nginx.conf 中增加配置
```
server {
     #SSL 访问端口号为 443
     listen 443 ssl; 
     #填写绑定证书的域名
     server_name quellanan.xyz/; 
     #证书文件名称
     ssl_certificate 1_quellanan.xyz_bundle.crt; 
     #私钥文件名称
     ssl_certificate_key 2_quellanan.xyz.key; 
     ssl_session_timeout 5m;
     #请按照以下协议配置
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
     #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
     ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
     ssl_prefer_server_ciphers on;
     location / {
        #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
        root   /var/www/hexo;
        index index.php index.html index.htm default.php default.htm default.html;
     }
 }
```
还要增加一个80 端口的映射。
```
  server {
       listen       80;
       server_name quellanan.xyz;
       rewrite ^/(.*)$ https://quellanan.xyz:443/$1 permanent;
    }
```



这样就配置好了，重启nginx 服务，但是发现https 并不能访问，弄了一晚上没有出来。
详细问题可以见这个：
[腾讯云配置了ssl 证书，浏览器却无法访问？](https://cloud.tencent.com/developer/ask/232076)
