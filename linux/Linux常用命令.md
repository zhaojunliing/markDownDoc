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
```
zip 命令用来压缩文件

参数：
		-A：调整可执行的自动解压缩文件；
		-b<工作目录>：指定暂时存放文件的目录；
		-c：替每个被压缩的文件加上注释；
		-d：从压缩文件内删除指定的文件；
		-D：压缩文件内不建立目录名称；
		-f：此参数的效果和指定“-u”参数类似，但不仅更新既有文件，如果某些文件原本不存在于压缩文件内，使用本参数会一并将其加入压缩文件中；
		-F：尝试修复已损坏的压缩文件；
		-g：将文件压缩后附加在已有的压缩文件之后，而非另行建立新的压缩文件；
		-h：在线帮助；
		-i<范本样式>：只压缩符合条件的文件；
		-j：只保存文件名称及其内容，而不存放任何目录名称；
		-J：删除压缩文件前面不必要的数据；
		-k：使用MS-DOS兼容格式的文件名称；
		-l：压缩文件时，把LF字符置换成LF+CR字符；
		-ll：压缩文件时，把LF+cp字符置换成LF字符；
		-L：显示版权信息；
		-m：将文件压缩并加入压缩文件后，删除原始文件，即把文件移到压缩文件中；
		-n<字尾字符串>：不压缩具有特定字尾字符串的文件；
		-o：以压缩文件内拥有最新更改时间的文件为准，将压缩文件的更改时间设成和该文件相同；
		-q：不显示指令执行过程；
		-r：递归处理，将指定目录下的所有文件和子目录一并处理；
		-S：包含系统和隐藏文件；
		-t<日期时间>：把压缩文件的日期设成指定的日期；
		-T：检查备份文件内的每个文件是否正确无误；
		-u：更换较新的文件到压缩文件内；
		-v：显示指令执行过程或显示版本信息；
		-V：保存VMS操作系统的文件属性；
		-w：在文件名称里假如版本编号，本参数仅在VMS操作系统下有效；
		-x<范本样式>：压缩时排除符合条件的文件；
		-X：不保存额外的文件属性；
		-y：直接保存符号连接，而非该链接所指向的文件，本参数仅在UNIX之类的系统下有效；
		-z：替压缩文件加上注释；
		-$：保存第一个被压缩文件所在磁盘的卷册名称；
		-<压缩效率>：压缩效率是一个介于1~9的数值。

用法
1.将/home/data 这个目录下的所有文件打包压缩为当前目录下的data.zip
	zip -q -r data.zip /home/data
2.如果现在在/home 这个目录下，则如下
	zip -q -r data.zip data
3.如果在/home/data 这个目录下，则如下
	zip -q -r data.zip *

```

#### unzip
```
unzip -o test.zip -d tmp/
解压文件到指定目录
```

#### tar
```
tar -zxvf qwe.tar.gz
解压文件

tar -zcvf qwe.tar.gz qwe/
压缩qwe目录下的文件
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

方法一：
	添加静态路由  
	route add -net 192.168.0.0/24 gw 192.168.0.1 
	route add -host 192.168.1.1 dev 192.168.0.1

	删除路由 
	route del -net 192.168.0.0/24 gw 192.168.0.1

	增加默认路由
	route add default gw 192.168.0.1
方法二：
	添加路由 
	ip route add 192.168.0.0/24 via 192.168.0.1 
	ip route add 192.168.1.1 dev 192.168.0.1 
	
	删除路由 
	ip route del 192.168.0.0/24 via 192.168.0.1

	增加默认路由 
	ip route add default via 192.168.0.1 dev eth0

参考网址：[https://blog.csdn.net/u010178308/article/details/80969259](https://blog.csdn.net/u010178308/article/details/80969259)

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

#### arp
```
查询arp信息
arp -a 


```

#### traceroute
```
查询路由跳数
traceroute X:X:X:X
```
