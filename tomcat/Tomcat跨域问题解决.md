# Tomcat 8.5.30+ 跨域问题解决， No 'Access-Control-Allow-Origin'

[官网文档连接](https://tomcat.apache.org/tomcat-8.5-doc/config/filter.html#CORS_Filter)

把下面这段，添加在 conf > web.xml中，（大概在 589行左右)

```xml
<filter>
  <filter-name>CorsFilter</filter-name>
  <filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
  <init-param>
    <param-name>cors.allowed.origins</param-name>
    <param-value>*</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>CorsFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

配置完成重启tomcat即可