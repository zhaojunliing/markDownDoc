### Linux常用命令总结

#### chown
```
chown -R weblogic:weblogic /home/weblogic 
指定weblogic目录所有者为weblogic用户组下面的weblogic用户
```

#### chmod
```
chmod 777 /home/weblogic/tomcat/bin/startup.sh 
赋予startup.sh读写执行权限给weblogic。
```

#### mv
```
mv /home/oracle /home/weblogic
移动文件夹oracle到weblogic下面
```

#### cp
```
cp -R /home/oracle /home/webligic
复制文件夹oracle到weblogic下面
```
#### scp
```
可以拷贝远程Linux系统的文件
scp -r local_folder remote_username@remote_ip:remote_folder
```
#### df
```
df -h
查看磁盘使用情况
```

#### du
```
du -h --max-depth=1 /oracle/tomcat7/apache-tomcat-7.0.77
查看深度为1的文件占用情况
```

#### ls
```
ls
显示文件夹下面的文件列表
```

#### ll
```
ll
显示文件或文件夹的详细信息
```

#### zip

#### unzip
```
unzip -o test.zip -d tmp/
解压文件到指定目录
```

#### tar
```
tar -zxvf qwe.tar.gz
解压文件
```

#### yum
```
yum list | grep gcc
yum install gcc
yum cleall all
配置yum源后通过yum源安装软件
```

#### ps -ef|grep java
```
查找某个进程id
```

#### kill
```
kill 5555 -9
kill 5555
杀死指定进程
```
#### mkdir
```
创建文件夹
```

#### touch
```
创建文件
```

#### vi
```
编辑文件
```

#### find
```
find /etc -name xxx 
查找某个文件夹下的名称为xxx的文件
```

#### ifconfig
```
查看网卡信息
```

#### route
```
查看路由表信息
```

#### top
```
查看正在运行的进程的信息
```

#### netstat

#### lsof

#### uptime
```
uptime命令会显示当前的时间，同样也会显示系统已经运行的时间。从这些信息中，你就可以计算系统最后启动的时间了
```
#### nohup
```
可以使程序在后台运行，而不被挂断，退出工具的时候使用exit命令，而不是直接关闭窗口。
nohup /root/start.sh &  --不输出日志文件类型
nohup sh hcqs_blsj18081902.sh > nohup.file 2>&1 &   --打印日志文件到nohup.file中
```
#### jobs
```
查看正在后台运行的命令
```

#### fg
```
使用jobs命令查询后台运行的程序命令，然后使用fg将起调回前台运行
[root@monitor snow]# jobs
[1]+  Stopped                 nohup scp -P9922 -r src snow@192.168.6.165:/home/snow/data/
[root@monitor snow]# fg 1
nohup scp -P9922 -r src snow@192.168.6.165:/home/snow/data/
```
#### bg
```
同fg
```
