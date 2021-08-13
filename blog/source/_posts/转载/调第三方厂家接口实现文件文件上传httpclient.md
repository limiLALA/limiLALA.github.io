layout: post
title: 调第三方厂家接口实现文件文件上传httpclient
author: QuellanAn
categories: 
  - springBoot番外篇
tags:
  - springboot
  - java
  - 上传文件
  - httpclient
---

# 前言
这种情况算是很少见的，前端上传文件到服务端，服务端接收文件，再调第三方接口，将文件存到第三方服务器。

从客户端接收文件的就不说了，比较常见，就记录下调第三方接口带参数。

# 代码
url为路径
jsonObject为常规的请求参数
token 为鉴权
file 为文件。大伙根据自己的需求进行修改。
核心代码为：

```
MultipartEntityBuilder reqEntity = MultipartEntityBuilder.create();
reqEntity.addBinaryBody("file", new FileInputStream(file), ContentType.DEFAULT_BINARY, file.getName());
            Iterator iter = jsonObject.entrySet().iterator();

            while (iter.hasNext()) {
                Map.Entry entry = (Map.Entry) iter.next();
                System.out.println(entry.getKey().toString());
                System.out.println(entry.getValue().toString());

                StringBody value = new StringBody(entry.getValue().toString(), ContentType.create("text/plain", Consts.UTF_8));
                reqEntity.addPart(entry.getKey().toString(),value);
            }

            HttpEntity httpEntity = reqEntity.build();
            HttpPost httppost = new HttpPost(urlBuilder.toString());
            httppost.setEntity(httpEntity);
```
主要是使用MultipartEntityBuilder 将文件通过body 进行传输。
完整方法代码。
```
public static String sendPost(String url, JSONObject jsonObject, String token,File file) {
        StringBuilder urlBuilder = new StringBuilder(baseURLPath);
        urlBuilder.append(url);
        log.info("URL:" + url);
        log.info("Parm:" + jsonObject);
        FileBody fileBody=new FileBody(file);
        MultipartEntityBuilder reqEntity = MultipartEntityBuilder.create();

        try {
            reqEntity.addBinaryBody("file", new FileInputStream(file), ContentType.DEFAULT_BINARY, file.getName());
            Iterator iter = jsonObject.entrySet().iterator();

            while (iter.hasNext()) {
                Map.Entry entry = (Map.Entry) iter.next();
                System.out.println(entry.getKey().toString());
                System.out.println(entry.getValue().toString());

                StringBody value = new StringBody(entry.getValue().toString(), ContentType.create("text/plain", Consts.UTF_8));
                reqEntity.addPart(entry.getKey().toString(),value);
            }

            HttpEntity httpEntity = reqEntity.build();
            HttpPost httppost = new HttpPost(urlBuilder.toString());
            httppost.setEntity(httpEntity);
            setHttpHeader(httppost, token);

            RequestConfig config = RequestConfig.custom()
					.setConnectTimeout(1000)
					.setConnectionRequestTimeout(1000)
					.setSocketTimeout(10 *1000)
					.build();
            //数据传输的超时时间
            httppost.setConfig(config);
            String result = getPostResult(httppost);
            log.info(result);
            return result;
        } catch (Exception e) {
            log.error(e);
            return "";
        }

    }

private static void setHttpHeader(HttpPost httppost, String token) {
		httppost.setHeader("Authorization", "Bearer " + token);
	}
/**
	 * 获取post请求返回结果
	 * 
	 * @param httppost
	 * @return
	 */
	private static String getPostResult(HttpPost httppost) {
		String result = null;
		try (CloseableHttpResponse response = httpclient.execute(httppost);) {
			HttpEntity entity = response.getEntity();
			result = EntityUtils.toString(entity);
			if (result.contains("Invalid token")) {
				result = "Token 过期，请重新登录";
			}
		} catch (Exception e) {
			log.error(e.toString());
			result = "HTTP请求异常,请重试";
		}
		return result;
	}

```

使用的是httpclient。
```
<dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore-nio</artifactId>
            <version>4.4.4</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore</artifactId>
            <version>4.4.4</version>
        </dependency>

        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient-win</artifactId>
            <version>4.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient-cache</artifactId>
            <version>4.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpasyncclient</artifactId>
            <version>4.0-beta3</version>
        </dependency>
```
