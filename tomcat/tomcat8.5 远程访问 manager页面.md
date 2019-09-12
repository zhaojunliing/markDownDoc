### tomcat8.5 远程访问 manager页面

​	tomcat 由8.0 升级到8.5，以前使用jenkins进行远程部署，现在部署不了。相关用户已经添加，发现在本地仍然无法访问管理页面，应该是tomcat 8.5升级了某些默认配置。

​	网上搜索一圈，竟然没有很明确的开启manager页面的教程，自己搜索找到了答案。

​	直接远程登录manager页面会提示403页面，拒绝了地址，会提示你修改manger项目下的context.xml文件，这个文件在tomcat/webapps/manager/META-INF/文件夹下，默认内容是：

```xml
<Context antiResourceLocking="false" privileged="true" >
  <!--
    Remove the comment markers from around the Valve below to limit access to
    the manager application to clients connecting from localhost
  -->
  <!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
  -->
</Context>
```

context 标签下 有个属性 是

```java
org.apache.catalina.valves.RemoteAddrValve
```

这个是设置远程过滤的属性，这个属性有两个 属性值可以设置，allow和deny，就相当于访问白名单和黑名单，也相当于ACL的访问过滤，allow和deny的值可以写IP用正则表达式匹配，也可以写域名和计算机名称。

  把这个属性直接注释掉外部就可以直接访问到manager页面了，当然我并不建议这样做，manager页面的用户名密码很容易被破解，导致其他人随意上传项目或其他东西，所以，如果没有特殊需要的话，最好有过滤。

