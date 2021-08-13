layout: post
title: 一、Vue 世界初探
author: QuellanAn
categories: 
  - vue
tags:
  - vue
  - 前端
---
# 前言

我们后端用SpringBoot 框架已经搭建的差不多了之前，既然我们最初的梦想是做先后端分离的架手架，终于也开始学习一下前端的框架了。自己也算是前端小白，所以将自己学习vue 的过程记录系列博客。希望对学习vue 的小伙伴有帮助，用时文章中有不对的，希望大家及时提出一起探讨。

至于为什么要使用vue ,虽然是一个前端小白，但是还是知道当前主流的三大框架，Angular、React以及Vue .优劣我就不说了，我就说说我为什么选择vue 吧。其实还是因为毕竟是后端开发，对前端的东西不要求深入理解，做到能用能复制就好了。所以基本上是本着最小学习成本来的。所以相对Angular 和React来说，vue 算是上手最快的，所以也就入坑了。自己话了一周的时间预研，勉强算自己入门了吧，所以才开始写博客记录下来，这样也算是对自己学习的内容的整理，也可以记录下来方便大家。


# 学习地址
想要了解vue 是什么， 怎么学习？我也是参考网上的资料学习的。

vue.js 的官网：https://cn.vuejs.org/v2/guide/

菜鸟教程：https://www.runoob.com/vue2/vue-tutorial.html

gitBook: http://vue_book.siwei.me/preface.html

自己感觉官网上和菜鸟教程上，对自己的作用只是熟悉的了vue的语法，不足以我来搭建在项目中使用，但是又不能不看，不然基本的语法都不知道，怎么开展下一步。上面的gitBook 算是带我入门的，我也在网上找了很多资料，但是跟着gitBook一步步实现起来，整体流程算是清楚了，所以也推荐大家。自己记录这系列博客，也算自己vue入门吧，有不对的地方大家多多指教。

# 安装
好啦，说了这么多，我们正式开始吧。
我们直接使用vue-cli .当然大家亦可以使用其他的。我们首先电脑上 npm和git 并配置邮箱 ,至于怎么安装，网上有很多教程，这里就不说了，安装好之后，我们需要安装vue-cli 。
```
npm install vue vue-cli -g 
```

![file](https://img-blog.csdnimg.cn/20191217152520839.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

安装好之后，我们控制台我们想要创建项目的目录执行：
```
vue init webpack zlflovemmVue
```
![file](https://img-blog.csdnimg.cn/2019121715252191.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
![file](https://img-blog.csdnimg.cn/20191217152516658.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

这样就可以看到项目已经初始化成功了。我们现在用IDEA 打开这个项目，当然大家也可以用其他的，后端的用惯了idea ,所以也就用idea 来开发vue 啦。

# IDEA 配置vue 
我们既然使用idea,当然需要一些配置，不使用idea 的可以忽略。
1、我们打开settings 下载vue.js 插件，然后重启。打开我们创建的项目zlflovemmVue
![file](https://img-blog.csdnimg.cn/20191217152521604.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

2、配置js 版本 ECMAScript6 
![file](https://img-blog.csdnimg.cn/20191217152521850.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

3、HTML 增加 .vue 支持
![file](https://img-blog.csdnimg.cn/20191217152522158.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

4、启动项目，在edit Configurations 中增加npm 启动，配置如下图：
![file](https://img-blog.csdnimg.cn/20191217152522446.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
 
 配置好后，我们来启动就好啦，如下图就表示启动成功啦。
 ![file](https://img-blog.csdnimg.cn/20191217152522785.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
 我们启动成后，在浏览器上输入：
 ```
 http://localhost:8081
 ```
 ![file](https://img-blog.csdnimg.cn/2019121715252398.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
 
 证明我们项目已经初始化搭建完成啦。到这里我们已经完成了第一步。但是可以看到我们到现在为止还没有开始写代码，也不知道如何下手写。
 
 不要急，我们前面这些工作做好后，我们接下来就开始啦。
 
 # 项目结构
 虽然我们项目稀里糊涂的启动起来了，但是相比到此的小伙伴还是一头雾水，在那写我们的代码呢？整个流程是怎么样的呢？
在写代码之前，我们还是先来看看，vue-cli 初始化为我们创建的项目有哪些东西。
```
▸ build/                // 编译用到的脚本
▸ config/               // 各种配置
▸ dist/                 // 打包后的文件夹
▸ node_modules/         // node第三方包
▸ src/                  // 源代码
▸ static/               // 静态文件, 暂时无用
  index.html            // 最外层文件
  package.json          // node项目配置文件
```
![file](https://img-blog.csdnimg.cn/20191217152523389.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

## build 
保留各种打包脚本。不可或缺，不要随意修改。

展开后如下：
```
▾ build/
    build.js                  //打包使用， 不要修改。
    check-versions.js        //检查npm的版本， 不要修改。
    dev-client.js            //是在开发时使用的服务器脚本。不要修改。
    dev-server.js           //同上
    utils.js                // 不要修改。 做一些css/sass 等文件的生成。
    vue-loader.conf.js     //非常重要的配置文件，不要修改。内容是用来辅助加载vuejs用到的css source map等内容。
    webpack.base.conf.js     //下面这三个都是基本的配置文件。不要修改
    webpack.dev.conf.js
    webpack.prod.conf.js
```
我们初学者阶段，暂时不用管这些，也不改这些东西。

## config 
上图我们可以看到config 目录中就有
```
▾ config/
    dev.env.js
    index.js
    prod.env.js
		test.env.js
```
dev.env.js 开发模式下的配置文件，一般不用修改。
prod.env.js 生产模式下的配置文件，一般不用修改。
test.env.js 测试模式下的配置文件，一般不用修改。
index.js 很重要的文件， 定义了 开发时的端口（默认是8080），定义了图片文件夹（默认static)， 定义了开发模式下的 代理服务器. 我们修改的还是比较多的。

## node_modules
node项目所用到的第三方包，特别多，特别大。 $ npm install 所产生。
这个文件夹不要放到git中

## src
最最核心的源代码所在的目录。我们要写的代码就是写在这个里面啦。
```
▾ src/
  ▾ assets/
      logo.png
  ▾ components/
      Hello.vue
  ▾ router/
      index.js
    App.vue
    main.js
```
assets: 用到的图片

components: 用到的"视图"和"组件"所在的文件夹。（最最核心）

router/index.js 路由文件。 定义了各个页面对应的url.

App.vue 如果index.html 是一级页面模板的话，这个App.vue就是二级页面模板。 所有的其他vuejs页面，都作为该模板的 一部分被渲染出来。

main.js 废代码。没有实际意义，但是为了支撑整个vuejs框架，存在很必要。

# Hello World
好啦，我们已经知道了项目的结构了，现在就要开始实现我们自己的hello world 啦。不然我们当程序员还有什么意义。
其实我们程序已经帮我们写了一个helloworld 。但是我们还是自己来创建一个，这样自己才能熟悉点。最终添加的内容图如下：

![file](https://img-blog.csdnimg.cn/20191217152520333.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
## Hello.vue
我们在src--components 新建Hello.vue 。内容如下:
```
<template>
  <div>
    {{message}}
  </div>
</template>

<script>
    export default {
        data(){
          return {
            message: "hello world"
          }
        }
    }
</style>

```
可以看到内容很简单，就是返回一个hello world 。
## 修改index.js
接下来我们在src--router--index.js 中增加一个路由。

![file](https://img-blog.csdnimg.cn/20191217152524394.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

## 启动
这样的话我们就可以来启动项目啦。启动的时候报这种错误：
```
  ✘  http://eslint.org/docs/rules/indent  
```
![file](https://img-blog.csdnimg.cn/20191217152524963.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

启用eslint检测不通过导致的，我们这里的解决办法：

 在build/webpack.base.conf.js文件中，注释config.dev.useEslint?这行配置，然后重启项目就好了。
 ![file](https://img-blog.csdnimg.cn/20191217152525749.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
 
 启动之后，我们在界面上输入：
 ```
 http://localhost:8081/#/hello
 ```
 ![file](https://img-blog.csdnimg.cn/20191217152520889.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
 
 这里我们的hello world 出来了，但是我们可能会感觉到奇怪，我们只是仅仅写了helloworld 为什么还有logo ,并且还有居中的样式。我们这个问题留在下篇文章接着将。这里我们先记着。
 
 # 番外
 到此为止，我们也算是将vue安装成功了，并运行一个非常简单的例子。
 
 代码上传到github：
https://github.com/QuellanAn/zlflovemmVue

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
 



