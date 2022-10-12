Linux 下配置 tomcat 服务

1、下载安装包，并解压到指定位置。

```shell
[root@h150 ~]# curl -#O https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.54/bin/apache-tomcat-9.0.54.tar.gz
######################################################################## 100.0%
[root@h150 ~]# mkdir /opt/apache
[root@h150 ~]# tar -zxvf apache-tomcat-9.0.54.tar.gz -C /opt/apache/
[root@h150 ~]# mv /opt/apache/apache-tomcat-9.0.54/ /opt/apache/tomcat-9.0.54/
```

2、将 tomcat 设置为系统服务

```shell
[root@h150 ~]# vi /usr/lib/systemd/system/tomcat9.service
```


 并在 tomcat9.service 中编写如下内容：

```shell
[Unit]
Description=Apache Tomcat Web Application Container
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
Environment=JAVA_HOME=/usr/local/jdk1.8.0_311
Environment=CATALINA_HOME=/data/tomcat-face-web
Environment=CATALINA_BASE=/data/tomcat-face-web
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment=CATALINA_PID=/data/tomcat-face-web/tomcat9.pid
PIDFile=/data/tomcat-face-web/tomcat9.pid
ExecStart=/data/tomcat-face-web/bin/startup.sh
ExecStop=/data/tomcat-face-web/bin/shutdown.sh
ExecReload=/bin/kill -s HUP $MAINPID

[Install]
WantedBy=multi-user.target
```

3、将 tomcat 服务启动并设置为开机自动启动

```shell
[root@h150 ~]# systemctl start tomcat9.service
[root@h150 ~]# systemctl enable tomcat9.service
```