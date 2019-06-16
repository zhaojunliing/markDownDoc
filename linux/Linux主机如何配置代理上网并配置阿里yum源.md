# Linux主机如何配置代理上网并配置阿里yum源
---
## 一、修改系统环境变量
### 1、修改etc/profile文件，配置代理上网
```
vi /etc/profile

http_proxy=http://192.168.10.120:38080/
https_proxy=http://192.168.10.119:38080/
ssl_proxy=http://192.168.10.119:38080/
all_proxy=socks://192.168.10.119:38080/
export http_proxy
export https_proxy
export ssl_proxy
export all_proxy
```
执行 source /etc/profile使刚才的修改生效。

### 2、修改/etc/yum.conf文件，配置yum代理上网
```
vi /etc/yum.conf

proxy=http://admin:admin@192.168.10.120:38080
```

##二、测试配置是否成功
```
[root@localhost ~]# wget www.baidu.com
--2019-06-16 09:52:40--  http://www.baidu.com/
Resolving www.baidu.com... 61.135.169.121, 61.135.169.125
Connecting to www.baidu.com|61.135.169.121|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2039 (2.0K) [text/html]
Saving to: “index.html.2”

100%[==========================================>] 2,039       --.-K/s   in 0s      

2019-06-16 09:52:40 (472 MB/s) - “index.html.2” saved [2039/2039]

[root@localhost ~]# 

```

出现上面的页面说明配置成功！

##三、修改阿里云yum源
```
cd /etc/yum.repos.d
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
```

##四、处理缓存
```
yum clean all  	--清空缓存

yum makecache	--生成缓存

yum update 		--更新
```

##五、yum常用命令
```
yum list|grep java
yum install -y unzip zip
```