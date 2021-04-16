id: xmlhttprequest-cros
title: AJAX跨域POST问题(HTTP,HTTPS)
date: 2014-02-05 22:48:36
tags: [Web,JavaScript]
---
浏览器遵循[同源策略(same-origin policy)](https://en.wikipedia.org/wiki/Same-origin_policy)，它不允许当前站点的脚本与不同源的站点之间进行数据交互，只要协议、域名、端口有任何一个不同，都被视为不同源。但有时确实又必须在当前站点通过Ajax请求其他不同源的资源，这明显与同源策略相悖，于是就有了JavaScript跨域问题。

至于为什么要有同源策略，可以Google一下同源策略的详解，这里只记录如何解决Ajax跨域POst的问题，包括不同协议(HTTP, HTTPS)之间的Ajax请求。跨域问题的解决办法有很多，对于端口和协议的不同，只能通过后台来解决。

## 跨域资源共享(CORS)

### CORS原理
CORS（Cross-Origin Resource Sharing）跨域资源共享，定义了一种跨域访问的机制，当必须在访问跨域资源时，浏览器与服务器应该如何沟通。CORS背后的基本思想就是使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功还是失败。
<!-- more -->
## CORS实现
通过CORS的机制可以让AJAX实现跨域访问，一个域上的网络应用向另一个域提交跨域AJAX请求，开发人员只需在接受请求的服务端新增响应头Access-Control-Allow-Origin即可，剩下的就交给浏览器与服务端去沟通了。

举个例子，浏览器打开的站点A.com给站点B.com发送AJAX请求，并得到B.com返回的数据进行处理，浏览器与B.com的服务端的交互过程如下：
1. 在A.com的脚本发出跨域请求时，浏览器会先发出一个OPTIONS请求到B.com站点，用于查明这个跨域请求对于B.com站点是不是安全可接受的。
2. B.com后台服务端收到请求时：
  - 如果响应头中新增了Access-Control-Allow-Origin来表明允许A.com的跨域请求，那么A.com的跨域AJAX请求可以正常执行下去。
  - 否则，A.com的浏览器会报错：`XMLHttpRequest cannot load http://B.com/api?p=a. No'Access-Control-Allow-Origin' header is present on the requested resource。 Origin 'A.com' is therefore not allowed access.`

## CORS浏览器支持情况
![](http://7xnocv.com1.z0.glb.clouddn.com/xmlhttprequest-cros-02.png)

## 所遇问题
1. 通过给XHR实例设置withCredentials属性的，必须为同源才可以。
   如：`xhr.withCredentials = true`

2. 对于是POST和存在自定义HTTP头时无数据响应
   这两种情况可以通过添加Access-Control-Allow-Methods/Access-Control-Allow-Headers来解决。

3. 对于多个站点域要跨域调用怎么办？
   搜了一下，有说通过空格分隔开`Access-Control-Allow-Origin: http://a.com http://c.com`，有说通过逗号分隔开的`Access-Control-Allow-Origin: http://a.com, http://c.com`，没有进行验证测试。
   (Access-Control-Allow-Origin: *)表示允许任何域都可以提交请求。

### 示例
```java
response.setHeader("Access-Control-Allow-Origin", "http://A.com");
response.addHeader("Access-Control-Allow-Methods", "GET, POST, OPTIONS");
response.setHeader("Access-Control-Allow-Headers", "Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With");
```
