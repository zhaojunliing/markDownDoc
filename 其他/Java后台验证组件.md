# Java后台验证组件
id: fun-validation
title: Java后台验证组件
date: 2015-06-20 20:54:49
tags: [Validation,Java]
---
在项目中前端和后端的验证都必不可少，公司使用的框架是基于Spring MVC开发，但未对验证做相应的解决方案，在网上找到了Validation.FO的验证框架，顿时被其简洁的设计所吸引，经过对其自定义的拓展开发，集成到了项目中。

## Validation.FO 框架说明
1. 这是一个验证框架，并且是一个 独立的验证框架 ，不依赖与其他已有的框架；
2. 可以自由的嵌入到其他框架，比如Spring、Struts等流行框架，但实质来说他是独立的，所以无所谓嵌入到哪里，如果需要在GUI桌面应用中，也是完美的；
3. 配置简单，可自由扩展验证器，实际只要实现IValidator接口，以及在rules.fo.xml中添加相关的配置即可；
4. 支持Spring接口
5. 使用过程中，你会感觉好像只用了 IValidateService.validate() 一个方法，这会让人感觉良好
<!-- more -->

## Validation.FO 相关资料
[Validation.FO 使用指南](http://www.cnblogs.com/41zone/p/4041727.html)

[Validation.FO 项目主页](https://github.com/41zone/Validation.FO/wiki)

## 配置繁多的缺点
虽然Validation.FO这款验证框架有诸多的有点，但配置起来很是繁琐，基本没有可供公用的配置，而项目中很多地方的参数校验是通用的。

## 改进后的Fun.Validation

基于Validation.FO修改后，虽然加入了引用和继承公用的配置，但这套设计是基于配置不可避免的仍然需要很多配置，目前尚未找到更好的设计方案。

[Fun.Validation的项目主页](https://github.com/fun90/fun-validation)