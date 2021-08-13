layout: post
title: 九、Spring Boot 优雅的实现CORS跨域
author: QuellanAn
categories: 
  - springBoot
tags:
  - springboot
  - java
---
# 前言
我们的springboot 架手架已经包含了mysql,redis，定时任务，邮件服务，短信服务，文件上传下载，以及docker-compose 构建镜像等等。

接下来让我们解决另一个常见的问题。一般的情况下，都是前后端分离的，我这个架手架的初衷也是前后端进行分离，所以这里就涉及到一个很严重的问题啦，当协议，端口，IP三者有其一不同就会产生跨域，所以需要做跨域支持。




# 测试跨域的文件
在这之前，我们先写一个测试接口是否跨域的html ,这样下面的测试比较方便。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<link type="test/css" href="css/style.css" rel="stylesheet">

<body>

	<input type="text" style="width:600px;height:30px;font-size:14px;" id="urlText" value="" />
	<br>
	<input type="button" style="margin: 10px";  id="cors" value="判断是否可访问"/>

<p>http://localhost:9090/zlflovemm/</p>
<script type="text/javascript" src="https://code.jquery.com/jquery-3.2.1.min.js"></script>  
<script type="text/javascript">
	$(function(){
	$("#cors").click(
		function(){
			var url2 = $("#urlText").val();
			$.post({
				contentType:'application/x-www-form-urlencoded;charset=UTF-8',
				url:url2,
				data: "/rAIeKeSBG1LV XoIq82/O",
				success:function(data){
					alert("success");
				}
			})
		});
	});
</script>
</body>
</html>
```

接下来我们来学习下在springboot 项目中怎么实现支持跨域。
# @CrossOrigin 注解
这种方法是springboot 自带的，使用比较简单，在需要支持的跨域的接口上加上这个注解就可以了。
比如在我们项目的demo 接口加上注解.就表示这个接口支持跨域，其中origins = "*"
表示所有的地址都可以访问这个接口，也可以写具体的地址，表示只有这个地址访问才能访问到接口。 
 ```
@CrossOrigin(origins = "*")
```
![file](https://img-blog.csdnimg.cn/20191204095640478.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

## 测试
我们也来测试一下，启动项目后，在浏览器上运行我们的测试的html文件。
发现localhost:9090/zlflovemm/ 是可以访问的。
![file](https://img-blog.csdnimg.cn/20191204095636490.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
说明跨域是支持的。大伙可以先将注解去掉测试一下，然后加上注解测试一下进行对比。

这种方式虽然很简单，但是缺点也不小，需要跨域的接口都需要加上这个注解，这对前后端分离的项目是不友好的，所以这种方式基本上用的很少。

# 重写WebMvcConfigurer的addCorsMappings 方法。
这种方法在实际项目中也用的比较多，是一种全局支持跨域的方法。
我们创建一个CorsConfig 类。内容如下：
```
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")//项目中的所有接口都支持跨域
                .allowedOrigins("*")//所有地址都可以访问，也可以配置具体地址
                .allowCredentials(true)
                .allowedMethods("*")//"GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS"
                .maxAge(3600);// 跨域允许时间
    }
}
```
加上@Configuration 表示是配置类，在项目启动的时候会加载。实现WebMvcConfigurer 接口并重写addCorsMappings 方法。代码比较简单，也有注释。

测试的话，大家可以自行测试，我测试都是通过的和上面一样测试就可以，这里就不占篇幅了。

# Filter
除了上面方法外，也可以使用过滤器。我们创建一个CorsFilter 类，内容如下：
```

@Slf4j
@Component
public class CorsFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse)servletResponse;
        response.setHeader("Access-Control-Allow-Origin", "*");
        response.setHeader("Access-Control-Allow-Methods", "POST, PUT, GET, OPTIONS, DELETE");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, client_id, uuid, Authorization");
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        response.setHeader("Pragma", "no-cache");
        filterChain.doFilter(servletRequest,response);
    }
}
```
上面代码中设置response.setHeader("Access-Control-Allow-Origin", "*");表示所有的地址都可以访问项目接口。

# 番外
接下来我们再介绍一个常用的功能，前后端分离，在访问接口的时候，有的 公司往往会增加一下专属的后缀名才能访问。实际上没有什么太大的作用，能稍微增加一下系统的安全性。这里我就简单是实现一下。真个都非常简单。
一样的是实现WebMvcConfigurer 接口，重写configurePathMatch你方法和增加一个dispatcherServlet。

代码如下：
```
@Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.setUseRegisteredSuffixPatternMatch(true);
    }

    @Bean
    public ServletRegistrationBean servletRegistrationBean(DispatcherServlet dispatcherServlet) {
        ServletRegistrationBean bean = new ServletRegistrationBean(dispatcherServlet);
        bean.addUrlMappings("*.zlf");
        return bean;
    }
```
这个功能实现，就只用这个多代码，configurePathMatch方法中设置的configurer.setUseRegisteredSuffixPatternMatch(true); 主要是将index 和index.* 都指向我们controller 中配置的@RequestMapping("/index")。

下面的servletRegistrationBean 方法主要是增加自定义拦截器，只有后缀为“.zlf”的接口才放行。

这样两步就简单的实现了接口增加自定义的后缀名啦。



到此为止，springboot 支持跨域的方式就差不多了，当然还有其他的实现方式没有研究。这些希望对大家有帮助。


好了，就说这么多啦
代码上传到github：
https://github.com/QuellanAn/zlflovemm

后续加油♡

欢迎大家关注个人公众号 "程序员爱酸奶"

分享各种学习资料，包含java，linux，大数据等。资料包含视频文档以及源码，同时分享本人及投递的优质技术博文。

如果大家喜欢记得关注和分享哟❤

![file](https://img-blog.csdnimg.cn/20191015213334732.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)

































