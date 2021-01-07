# Nginx的五种路由策略方法

## 轮询<默认开启>

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除，配置如下：

```
upstream myserver {
    server 192.168.0.1;
    server 192.168.0.2;
}
```

轮询策略是默认的负载均衡策略

## 指定权重

即在轮询的基础之上，增加权重的概念，weight和访问比率成正比，用于后端服务器性能不均的情况，配置如下：

```
upstream myserver {
    server 192.168.0.1 weight=1;
    server 192.168.0.2 weight=10;
}
```

## IP Hash

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题，配置如下：

```
upstream myserver {
    ip_hash;
    server 192.168.0.1;
    server 192.168.0.2;
}
```

## fair

第三方提供的负载均衡策略，按后端服务器的响应时间来分配请求，响应时间短的优先分配，生产环境中有各种情况可能导致响应时间波动，需要慎用

```
upstream myserver {
    server 192.168.0.1;
    server 192.168.0.2;
    fair;
}
```

## url_hash

第三方提供的负载均衡策略，按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器

```
upstream myserver {
    server 192.168.0.1;
    server 192.168.0.2;
    hash $request_uri;
    hash_method crc32;
}
```