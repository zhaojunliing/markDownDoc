## Nginx服务器配置请求头

针对扫描出来的漏洞，修改nginx.conf，补全缺失的请求头。

在server中增加如下配置：



```shell
# 请求资源类型，防止篡改
add_header X-Content-Type-Options nosniff;
add_header Referrer-Policy origin;

#防止跨站脚本 Cross-site scripting (XSS)     
add_header X-XSS-Protection "1; mode=block";  
	
add_header X-Download-Options noopen;
	
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
	
add_header Content-Security-Policy "object-src 'self'";
add_header X-Permitted-Cross-Domain-Policies master-only;
	
#并不限制内容加载来源
add_header Content-Security-Policy "script-src * 'unsafe-inline' 'unsafe-eval'";                                                                                         
#将本站内部http链接自动改为https，并不限制内容加载来源
#add_header Content-Security-Policy "upgrade-insecure-requests;content *;img-src '*'";
	
add_header X-Frame-Options "SAMEORIGIN";
```



隐藏nginx版本信息，在http中增加配置。

```shell
server_tokens off;
```



首页存在nginx.org,nginx.com文件，对nginx安装文件夹中html文件夹的index.html，50x.html修改即可，也能直接删除。