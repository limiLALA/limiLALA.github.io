layout: post
title: 二、Vue 页面渲染过程
author: QuellanAn
categories: 
  - vue
tags:
  - vue
  - 前端
---
# 前言
上篇博文我们依葫芦画瓢已经将hello world 展现在界面上啦，但是是不是感觉新虚虚的，总觉得这么多文件，项目怎么就启动起来了呢？怎么访问到8080 端口就能进入到我们的首页呢。整个的流程是怎么样的呢？

我也是刚刚接触，所以就会有这样的困惑，所以这篇就简单的理解一下项目页面渲染的过程。

# 渲染过程
我们上篇文章说main.js 是无用的，是废代码，只是起到支撑框架的。但是其实我们应该有感觉，把他删除了整个项目就跑步起来了。其实main.js 算是项目的入口了。我们就从这个文件看起。
```
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```
可以看到代码非常的少，就导入了vue.js、我们的APP.vue 以及index.js![在这里插入图片描述](https://img-blog.csdnimg.cn/20191217192219522.gif)
第一次做动图，操作像是老年人，大家见谅。上图可以大概的看到引入的三个文件是什么了。
Vue.config.productionTip = false 我们这里暂时不管，知道是一个配置信息就可以了，感兴趣的可以百度一下就知道什么意思了。
```
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```
上面这些，如果完全没有vue 语法知识的话，确实不知道什么意思，但是我们看官网教程，起步的时候都是在当个html 文件中使用vue 的。在js 中就会用到这个。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019121719314380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
可以看到，其实都是差不多的，所以这里的作用就是实例化一个Vue。当然我们项目中，这里是为整个项目实例化了一个Vue ,el 指定的元素，这里就是我们index.html 中的div啦。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191217193429774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
router 就指定路由，也就是我们在index.js 配置的路由信息。
components 指定的组件信息。项目有一个父组件就是APP.vue。我们自己写的所有组件都是在这个父组件之下的。
怎么说呢，也就是说所有的界面，最外层的div 就是APP.vue 定义的。div 中其他的div 才是我们自己写的。看下面这个应该就会有所感觉吧。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191217193917974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
所以这里我们就可以解答上篇文章，为什么我们只是写了一个hello world 。但是为什么界面上呈现的会有图标，还有样式。因为在APP.vue 中设置了这些动洗。我们APP.vue 中的这些内容注释掉就可以看到效果。

我们将APP.vue logo和样式去掉，再来看看内容
```
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>
```
是不是发现和我们在组件中自己写的Hello.vue 格式完全一样，哈哈没错，vue文件就是这样的格式。可以看到template 渲染的是id 为app 的盒子(div)。这里应该是覆盖了index.html中的d 也为app 的盒子。

所有的 router-view 中的内容，都会被自动替换。
script 中的代码则是脚本代码。 

至此，整个过程就出来了：项目启动首先会读取main.js 。实例化一个vue,然后渲染APP.vue 文件内容，我们自己写的vue 组件则是通过路由转接到父组件下的。


# 番外
我们项目的流程就讲到这里把，算是对上篇的补充，让我们对项目启动，界面渲染算是有一个大概的了解啦，我们接下来就按照官网上讲一下vue 的一些语法和特性，但是与官网上不同的是，官网上都是一个个的html,而我们就在这个项目的基础的上。将会是一个个的vue 文件。
 
 代码上传到github：
https://github.com/QuellanAn/zlflovemmVue

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
