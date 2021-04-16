# FastDFS安装笔记
id: fastdfs-install
title: FastDFS安装笔记
date: 2015-07-29 21:10:03
tags: [FastDFS]
---

## 版本说明
1. 系统版本为CentOS 7
2. fastdfs版本选用的5.05

注意：系统版本不一样，相关命令会有差异

## 下载准备
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-1.png)
<!-- more -->
选用fastdfsz的版本是5.05，区别于以往版本，这个版本将公共的一些函数等单独封装成了libfastcommon这个jar包，所以在安装fastdfs之前，我们必须先安装libfastcommon。

## 安装libfastcommon
``` sh
cd /opt
wget https://github.com/happyfish100/libfastcommon/archive/master.zip
unzip master.zip
cd libfastcommon-master
./make.sh
./make.sh install
```
缺少依赖包，出现如下错误：
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-2.png)

安装依赖包
``` sh
yum -y install gcc perl
```
再次安装libcofastcommon
``` sh
cd ~/opt/libfastcommon-master && ./make.sh && ./make.sh install
```

## 安装fastdfs
``` sh
wget  https://github.com/happyfish100/fastdfs/archive/V5.05.tar.gz
tar -zxvf V5.05.tar.gz 
cd fastdfs-5.05/
./make.sh
./make.sh install
```

安装完成后，所有可执行文件在目录/usr/bin下，以fdfs_开头：
``` sh
ls /usr/bin/fdfs_*
/usr/bin/fdfs_appender_test   /usr/bin/fdfs_monitor
/usr/bin/fdfs_appender_test1  /usr/bin/fdfs_storaged
/usr/bin/fdfs_append_file     /usr/bin/fdfs_test
/usr/bin/fdfs_crc32           /usr/bin/fdfs_test1
/usr/bin/fdfs_delete_file     /usr/bin/fdfs_trackerd
/usr/bin/fdfs_download_file   /usr/bin/fdfs_upload_appender
/usr/bin/fdfs_file_info       /usr/bin/fdfs_upload_file
```
配置文件在目录/etc/fdfs下：
```sh
ls /etc/fdfs
client.conf.sample  storage.conf.sample  tracker.conf.sample
```

## 配置Tracker跟踪器
开放tracker监听端口访问
``` sh
firewall-cmd --zone=public --add-port=22122/tcp --permanent
firewall-cmd --reload
```
修改Tracker配置文件
``` sh
mkdir -p /home/fastdfs
cd /etc/fdfs
cp tracker.conf.sample tracker.conf
sed -i 's:base_path=.*:base_path=/home/fastdfs:g' tracker.conf
```
设置Tracker便捷管理命令
``` sh
bash -c 'cat > /usr/lib/systemd/system/fdfs_trackerd.service << EOF
[Unit]
Description=fastdfs tracker server
After=network.target
[Service]
Type=forking
PIDFile=/home/fastdfs/data/fdfs_trackerd.pid
ExecStart=/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf
ExecReload=/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
ExecStop=/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf stop
[Install]
WantedBy=multi-user.target
EOF'
systemctl enable fdfs_trackerd.service
```
启动tracker
``` sh
systemctl start fdfs_trackerd.service
```
查看启动tracker日志，确认tracker是否启动成功
``` sh
cat /home/fastdfs/logs/trackerd.log
```
## 配置Storage存储节点
开放Storage监听端口访问
``` sh
firewall-cmd --zone=public --add-port=23000/tcp --permanent
firewall-cmd --reload
```
修改Storage配置文件
``` sh
mkdir -p /home/fastdfs
cd /etc/fdfs
cp storage.conf.sample storage.conf
cp /opt/fastdfs-5.05/conf/http.conf .
cp /opt/fastdfs-5.05/conf/mime.types .
sed -i 's:base_path=.*:base_path=/home/fastdfs:g' storage.conf
sed -i 's:store_path0=.*:store_path0=/home/fastdfs:g' storage.conf
sed -i 's/tracker_server=.*/tracker_server=192.168.0.80:22122/g' storage.conf
```
设置Storage便捷管理命令
``` sh
bash -c 'cat > /usr/lib/systemd/system/fdfs_storaged.service << EOF
[Unit]
Description=fastdfs storage server
After=network.target
[Service]
Type=forking
PIDFile=/home/fastdfs/data/fdfs_storaged.pid
ExecStart=/usr/bin/fdfs_storaged /etc/fdfs/storage.conf
ExecReload=/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart
ExecStop=/usr/bin/fdfs_storaged /etc/fdfs/storage.conf stop
[Install]
WantedBy=multi-user.target
EOF'
systemctl enable fdfs_storaged.service
```
启动Storage
``` sh
systemctl start fdfs_storaged.service
```
查看启动Storage日志，确认Storage是否启动成功
``` sh
cat /home/fastdfs/logs/storaged.log
```
## 配置Client客户端
这里搭建的是测试环境，所以Client与Tracker、Storage都安装在同一台机器上
``` sh
mkdir -p /home/fastdfs
cd /etc/fdfs
cp client.conf.sample client.conf
sed -i 's:base_path=.*:base_path=/home/fastdfs:g' client.conf
sed -i 's/tracker_server=.*/tracker_server=192.168.0.80:22122/g' client.conf
```
## FastDFS测试
上传测试
``` sh 
/usr/bin/fdfs_upload_file /etc/fdfs/client.conf /opt/Tulips.jpg
```
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-3.png)

查看文件信息
``` sh
/usr/bin/fdfs_file_info /etc/fdfs/client.conf group1/M00/00/00/wKgAUFXByOmAVJ5kAABzaUPC-FA256.jpg
```
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-4.png)
下载测试
```  sh
/usr/bin/fdfs_download_file /etc/fdfs/client.conf group1/M00/00/00/wKgAUFXByOmAVJ5kAABzaUPC-FA256.jpg downtest.jpg
```
重启命令
``` sh
/usr/bin/fdfs_trackerd  /etc/fdfs/tracker.conf  restart
/usr/bin/fdfs_storaged  /etc/fdfs/storage.conf  restart
```

## Nginx安装
安装缺少的依赖包
``` sh
yum install gcc-c++ 
yum install pcre pcre-devel 
yum install zlib zlib-devel 
yum install openssl openssl--devel 
```
解压nginx-1.7.12.tar.gz，编译安装Nginx
``` sh
tar -zxvf nginx-1.7.12.tar.gz
cd /opt/nginx-1.7.12
./configure --prefix=/opt/nginx --user=nginx --group=nginx --with-pcre --with-debug --add-module=/opt/fastdfs-nginx-module-master/src
make
make install
```
安装完最后会列出配置信息
``` sh
Configuration summary
  + using PCRE library: /usr/lib64
  + OpenSSL library is not used
  + using builtin md5 code
  + sha1 library is not found
  + using zlib library: /usr/lib64
  nginx path prefix: "/opt/nginx"
  nginx binary file: "/opt/nginx/sbin/nginx"
  nginx configuration prefix: "/opt/nginx/conf"
  nginx configuration file: "/opt/nginx/conf/nginx.conf"
  nginx pid file: "/opt/nginx/logs/nginx.pid"
  nginx error log file: "/opt/nginx/logs/error.log"
  nginx http access log file: "/opt/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```
防火墙开发HTTP服务
``` sh
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --reload
```
创建nginx用户
``` sh
useradd nginx
passwd
```
启动nginx
``` sh
/opt/nginx/sbin/nginx 
```
查看端口
``` sh
netstat -tnlp|grep nginx
```
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-6.png)

浏览器访问
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-7.png)

其他命令
Nginx -s stop         快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。（quick exit）
Nginx -s quit         平稳关闭Nginx，保存相关信息，有安排的结束web服务。（graceful exit）
Nginx -s reload       因改变了Nginx相关配置，需要重新加载配置而重载。（changing configuration，start a new worker,quitting an old worker gracefully.)
Nginx -s reopen       重新打开日志文件。(reopenging log files)

## fastdfs-nginx-module 配置
``` sh
cp /opt/fastdfs-nginx-module-master/src/mod_fastdfs.conf /etc/fdfs/
touch /home/fastdfs/logs/mod_fastdfs.log
chown nginx:nginx /home/fastdfs/logs/mod_fastdfs.log
vi /etc/fdfs/mod_fastdfs.conf
```
修改
```
base_path=/home/fastdfs
tracker_server=192.168.0.80:22122
store_path0=/home/fastdfs
log_filename=/home/fastdfs/logs/mod_fastdfs.log
```
##
Storage服务器Nginx配置
/usr/local/nginx/conf/nginx.conf 上server{} 里添加
```
location ~/group1/M00 {
    alias /home/fastdfs/data;
    ngx_fastdfs_module;
}
```
Nginx重新加载配置
``` sh
/opt/nginx/sbin/nginx -s reload
```

## 问题总结
1、当上传文件时发现总是失败返回response status 2 !=0，查看storaged.log发现如下错误：
![](http://7xnocv.com1.z0.glb.clouddn.com/fastdfs-install-5.png)
用fdfs_monitor命令查看storage节点状态
``` sh
/usr/bin/fdfs_monitor /etc/fdfs/storage.conf
```
发现两个离线状态的无效storage节点，用以下命令删除之
``` sh
fdfs_monitor /etc/fdfs/client.conf delete group1 192.168.0.80
```
重启tracker及storage服务，问题解决。
未深入分析原因。

2、访问图片出现400错误
查看日志/home/fastdfs/logs/mod_fastdfs.log
```
[2015-07-30 16:24:13] ERROR - file: ../common/fdfs_global.c, line: 52, the format of filename "group1/M00/00/00/wKgAUFW53n-AZ5X2AABzaUPC-FA370.jpg" is invalid
```
/opt/nginx/logs/error.log
```
192.168.0.170 - - [30/Jul/2015:16:13:06 +0800] "GET /group1/M00/00/00/wKgAUFW5w4iAViuiAABVNkmGVeE577.xml HTTP/1.1" 400 575 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/43.0.2357.124 Safari/537.36"
```
后查明是因为url中带了group名字，FastDFS是默认关闭的。
```
url_have_group_name=false
```
将以上参数改为true即可
``` sh
sed 's:url_have_group_name.*:url_have_group_name=true:g' /etc/fdfs/mod_fastdfs.conf
```