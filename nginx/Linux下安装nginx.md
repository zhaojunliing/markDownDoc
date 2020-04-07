# linux下安装nginx

【yum 安装最新版nginx】

在安装nginx前首先要确认系统中安装了**gcc、pcre-devel、zlib-devel、openssl-devel**。

Linux下检查是否安装过某软件包：

安装命令：

```
yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
```

nginx下载地址：https://nginx.org/download/

下载“nginx-1.9.9.tar.gz”，移动到/usr/local/下。

```
## 解压
tar -zxvf nginx-1.9.9.tar.gz

##进入nginx目录
cd nginx-1.9.9
## 配置
./configure --prefix=/usr/local/nginx

# make
make
make install
```

执行make、make install命令

测试是否安装成功

```
# cd到刚才配置的安装目录/usr/loca/nginx/
./sbin/nginx -t
```

正常情况的信息输出：

nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful

启动nginx 

```
cd /usr/local/nginx/sbin
./nginx #启动nginx
```

在浏览器中输入服务器的ip地址，如：xxx.xxx.xxx.xxx

正常情况下可以正常访问

**====================== 分割线 ====================**

配置完毕！

2、配置nginx开机自启动

```
vim /etc/rc.d/rc.local

##添加nginx开机启动
/usr/local/nginx/sbin/nginx
```