# Spring MVC接收List集合和数组参数
id: spring-mvc-list
title: Spring MVC接收List集合和数组参数
date: 2014-02-26 22:38:06
tags: [Spring]
cover: http://7xnocv.com1.z0.glb.clouddn.com/20170911152348_DQ458N_spring-framework.jpeg
categories: 技术
---

Spring MVC在接收集合请求参数时，需要根据参数情况对Controller和前端JS代码做相应的调整。在接收基本类型的List或数组时，只需使用`@RequestParam`指定对应名称即可完成转换。但是在接收对象实体类型的List时，需要在Controller方法的集合参数里前添加`@RequestBody`，而`@RequestBody`默认接收的enctype (MIME编码)是`application/json`，因此发送POST请求时需要设置请求报文头信息，否则Spring MVC在解析集合请求参数时不会自动的转换成JSON数据再解析成相应的集合。

<!-- more -->

## 基本类型的List或数组
`List<Long> ids;`
JavaScript:
```javaScript
$.ajax({
    url: "/save"
    data: {ids: [1,2,3], msg: ['a', 'b', 'c']},
    dataType: "json"
)};
```
Controller:
```java
@PostMapping("/save")
public RestResultResponse save(@RequestParam("ids[]") List<Long> ids, @RequestParam("msg[]") String[] msg)) {
     return new RestResultResponse(SUCCESS);
}
```

不使用@RequestParam指定名字报错
```java
org.springframework.beans.BeanInstantiationException: Failed to instantiate [java.util.List]: Specified class is an interface
    at org.springframework.beans.BeanUtils.instantiateClass(BeanUtils.java:99)
    at org.springframework.web.method.annotation.ModelAttributeMethodProcessor.createAttribute(ModelAttributeMethodProcessor.java:141)
    at org.springframework.web.servlet.mvc.method.annotation.ServletModelAttributeMethodProcessor.createAttribute(ServletModelAttributeMethodProcessor.java:81)
    at org.springframework.web.method.annotation.ModelAttributeMethodProcessor.resolveArgument(ModelAttributeMethodProcessor.java:101)
   ....
```

## 单个实体类List或数组
`List<Menu>;`
Menu结构
```java
public class Menu {
    private Long id;
}
```
JavaScript：
```javaScript
var params = [{id:1},{id:2}];
$.ajax({
    url: "/save"
    data: JSON.stringify(params), // 必须将对象序列化成JSON字符串 
    dataType: "json",
    contentType : 'application/json;charset=utf-8', // 必须设置请求头信息
)};
```
Controller:
```java
@PostMapping("/save")
public RestResultResponse save(@RequestBody List<Menu> menus)) {
     return new RestResultResponse(SUCCESS);
}
```
另外，将`List<Menu>`替换成`List<Map<String, Long>>`也可以。

## 多个实体类List
`List<Menu> menus, List<Button> buttons;`
这样的需要建立一个包含`menus`和`buttons`的实体类
Panel.java
```java
public class Panel {
    List<Menu> menus;
    List<Button> buttons;
}
```
JavaScript：
```javaScript
var params = {menus: [{id: 1}, {id: 2}], buttons: [{text: 'a'}, {text: 'b'}]};
$.ajax({
    url: "/save"
    data: JSON.stringify(params), // 必须将对象序列化成JSON字符串 
    dataType: "json",
    contentType : 'application/json;charset=utf-8', // 必须设置请求头信息
)};
```
Controller:
```java
@PostMapping("/save")
public RestResultResponse save(@RequestBody Panel panel)) {
     return new RestResultResponse(SUCCESS);
}
```
