# MySQL数字类型区别与选择
id: mysql-number-type
title: MySQL数字类型区别与选择
date: 2014-02-15 22:07:36
categories: 技术

tags: [Java]
---

![](http://7xnocv.com1.z0.glb.clouddn.com/java-regex-01.jpg)

在开发中遇到需要替换JSON字符串某个key对应的双引号字符串，最后选择使用正则表达式来解决，接下来看看如何解决？

<!-- more -->

## 需求

有JSON字符串：`{"code":"kokf","params":"{\"branchCode\":\"yNeWWnKb\",\"vendorCode\":\"zvuXEixA\"}","timestamp":"1499092515370"}`，需要匹配到params对应的值(包括引号)，即：**"{\"branchCode\":\"yNeWWsMnKb\",\"vendorCode\":\"zvuXEiqtxA\"}"**

## 解决

Java代码：

```java
Pattern paramsRegex = Pattern.compile("(\"params\"\\s*:\\s*)((?<!\\\\)\".*?(?<!\\\\)\")");
String sourceParams = matcher.group(2); // 匹配到params对应的参数内容
```
注意：在Java正则表达式中的反斜杠标识表示为\\\，而在字符串中\必须使用\\\来表示，所以最后正则表达式的字符串为"\\\\\\\"

## 延伸

| 断言类型       | 表达式      | 说明             |
| ---------- | -------- | -------------- |
| 零宽度负回顾后发断言 | (?<!exp) | 匹配前面不是exp的位置   |
| 零宽度负预测先行断言 | (?!exp)  | 匹配后面跟的不是exp的位置 |
| 零宽度正预测先行断言 | (?=exp)  | 匹配exp前面的位置     |
| 零宽度正回顾后发断言 | (?<=exp) | 匹配exp后面的位置     |

这里使用的是零宽度负回顾后发断言，`(?<!\\)"`匹配双引号『"』但是双引号『"』前面**不是**\