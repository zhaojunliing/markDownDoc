# nginx隐藏版本号和标识

## 一、隐藏版本号
隐藏版本号比较简单，只要修改配置文件即可。 在配置文件中加入以下配置重启即可。

```
server_tokens off;

```
这个可以加在http块，server块。。 看自己的需求

## 二、隐藏nginx标识

> nginx.h
>
> ngx_http_special_response.c
>
> ngx_http_header_filter_module.c 

### 1、src/core/nginx.h 大约在14行

```shell
#没改动前

#define nginx_version      1018000
#define NGINX_VERSION      "1.18.0"
#define NGINX_VER          "nginx/" NGINX_VERSION
```

```SHELL
#改动后

#define nginx_version      1023001
#define NGINX_VERSION      "1.23.1"
#define NGINX_VER          "fhServer/" NGINX_VERSION
```

### 2、src/http包下 ngx_http_special_response.c 和 ngx_http_header_filter_module.c 

ngx_http_header_filter_module.c  大约49行

```shell
# 改动前
49 static u_char ngx_http_server_string[] = "Server: nginx" CRLF;
50 static u_char ngx_http_server_full_string[] = "Server: " NGINX_VER CRLF;
51 static u_char ngx_http_server_build_string[] = "Server: " NGINX_VER_BUILD CRLF;
```

```shell
# 改动后
49 static u_char ngx_http_server_string[] = "Server: fhServer" CRLF;
50 static u_char ngx_http_server_full_string[] = "Server: " NGINX_VER CRLF;
51 static u_char ngx_http_server_build_string[] = "Server: " NGINX_VER_BUILD CRLF;
```
ngx_http_special_response.c 大约35行

```shell
# 改动前
static u_char ngx_http_error_tail[] =
"<hr><center>nginx</center>" CRLF
"</body>" CRLF
"</html>" CRLF
;
```

```shell
# 改动后
static u_char ngx_http_error_tail[] =
"<hr><center>fhServer</center>" CRLF
"</body>" CRLF
"</html>" CRLF
;
```

3、其他正常安装即可
