# rsync的基本操作
----------------------
rsync是linux系统下的数据镜像备份工具。使用快速增量备份工具Remote Sync可以远程同步，支持本地复制，与其他SSH、rsync主机同步数据。

## 一、rsync命令的用法：
基本格式：rsync [选项] 原始位置 目标位置
常用选项：

    -a 归档模式，递归并保留对象属性，等同于 -rlptgoD
    -v 显示同步过程的详细(verbose)信息
    -z 在传输文件时进行压缩(compress)
    -H 保留硬链接文件
    -A 保留ACL属性
    --delete 删除目标位置有而原始位置没有的文件
    -r 递归模式，包含目录及子目录中所有文件
    -l 对于软链接文件仍然复制为软链接文件
    -p 保留文件的权限标记
    -t 保留文件的时间标记
    -g 保留文件的属组标记（仅超级用户使用）
    -o 保留文件的属主标记（仅超级用户使用）
    -D 保留设备文件及其他特殊文件

## 二、配置rsync
在配置rsync前，先来做个小测试：

**服务端**
	
```shell
#在服务端网站首页写入一些内容
[root@localhost Desktop]# cd /var/www/html
[root@localhost html]# vim index.html
[root@localhost html]# cat index.html 
Hello World!
Hello Jaking!
[root@localhost html]# ifconfig
eth0  Link encap:Ethernet  HWaddr 00:0C:29:BE:68:3F  
  inet addr:192.168.142.132  Bcast:192.168.142.255  Mask:255.255.255.0
  inet6 addr: fe80::20c:29ff:febe:683f/64 Scope:Link
  UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
  RX packets:580 errors:0 dropped:0 overruns:0 frame:0
  TX packets:390 errors:0 dropped:0 overruns:0 carrier:0
  collisions:0 txqueuelen:1000 
  RX bytes:57739 (56.3 KiB)  TX bytes:41856 (40.8 KiB)

loLink encap:Local Loopback  
  inet addr:127.0.0.1  Mask:255.0.0.0
  inet6 addr: ::1/128 Scope:Host
  UP LOOPBACK RUNNING  MTU:16436  Metric:1
  RX packets:16 errors:0 dropped:0 overruns:0 frame:0
  TX packets:16 errors:0 dropped:0 overruns:0 carrier:0
  collisions:0 txqueuelen:0 
  RX bytes:960 (960.0 b)  TX bytes:960 (960.0 b)
[root@localhost rsync]# service httpd restart
Stopping httpd:[  OK  ]
Starting httpd: httpd: Could not reliably determine the server's fully qualified domain name, using localhost.localdomain for ServerName [  OK  ]
```

**客户端**

```shell
#客户端能成功访问服务端网站首页的内容
[root@localhost Desktop]# curl 192.168.142.132
Hello World!
Hello Jaking!
```

刚刚的小测试其实是基于SSH实现的，rsync有两种同步源，一种是基于SSH的同步源，另一种是基于rsync的同步源。

## 三、基于SSH的同步源
**设置ACL权限：setfacl -m user:用户名:rwx /服务器目录  
下行同步：rsync -avz 用户名@服务器地址：/服务器目录 /本地目录  
上行同步：rsync -avz /本地目录 用户名@服务器地址：/服务器目录**

为确保服务端的数据能同步到客户端，接下来，我先从SSH的同步源开始配置：
在配置前，分别在服务端和客户端上执行yum install -y rsync，确保rsync已安装。

### 1.在服务端授权一个用户，也就是创建一个用户：
```shell
[root@localhost html]# useradd server
[root@localhost html]# passwd server
Changing password for user server.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
```

### 2.在客户端创建ssh目录，同步服务端数据：
```shell
[root@localhost Desktop]# mkdir /client
[root@localhost Desktop]# cd /client/
[root@localhost client]# mkdir ssh
[root@localhost client]# rsync -avz server@192.168.142.132:/var/www/html/* /client/ssh
server@192.168.142.132's password: 
receiving incremental file list
index.html

sent 68 bytes  received 219 bytes  114.80 bytes/sec
total size is 27  speedup is 0.09
 30 bytes  received 104 bytes  15.76 bytes/sec
total size is 27  speedup is 0.20
[root@localhost client]# cd ssh
[root@localhost ssh]# ls
index.html
[root@localhost ssh]# cat index.html 
Hello World!
Hello Jaking!
#客户端已成功同步服务端数据
```

### 3.刚刚的同步是下行同步，即从服务器端把数据同步到客户端。接下来我将演示一遍上行同步，即把客户端的数据同步到服务端：

```shell
#在客户端创建新文件，准备同步到服务端。
[root@localhost ssh]# touch a.txt b.txt
[root@localhost ssh]# ls
a.txt  b.txt  index.html
[root@localhost ssh]# rsync -avz /client/ssh/* server@192.168.142.132:/var/www/html
server@192.168.142.132's password: 
sending incremental file list
a.txt
b.txt
rsync: mkstemp "/var/www/html/.a.txt.6JDDzO" failed: Permission denied (13)
rsync: mkstemp "/var/www/html/.b.txt.p7hCLz" failed: Permission denied (13)

sent 131 bytes  received 50 bytes  40.22 bytes/sec
total size is 27  speedup is 0.15
rsync error: some files/attrs were not transferred (see previous errors) (code 23) at main.c(1052) [sender=3.0.9]
#同步失败，从报错结果可以server用户权限不足，server用户对/var/www/html目录没有写权限。
```

### 4.在服务端设置比较安全的ACL权限

```shell
[root@localhost html]# setfacl -m user:server:rwx /var/www/html
```

### 5.再次在客户端执行上行同步操作：
```shell
[root@localhost ssh]# rsync -avz /client/ssh/* server@192.168.142.132:/var/www/html
server@192.168.142.132's password: 
sending incremental file list
a.txt
b.txt

sent 131 bytes  received 50 bytes  51.71 bytes/sec
total size is 27  speedup is 0.15
#由同步的过程可以看出，index.html没有被上传，由此可知rsync使用的同步机制是增量备份的机制。
在服务端查看：

[root@localhost html]# ls
a.txt  b.txt  index.html
#客户端数据已成功同步到服务端
```

## 四、基于rsync的同步源

**/etc/rsyncd_users.db文件权限必须是600  
做上行同步时，nobody需要有写入权限  
rsync -avz 用户名@服务器地址::共享模块名 /本地目录  
rsync -avz rsync://用户名@服务器地址/共享模块名 /本地目录**

使用SSH的同步源需要创建用户，对于服务器来说，存在过多的用户不是一件好事。而用基于rsync的同步源则不需要创建用户，指定的用户只需写在配置文件里即可，这样的用户是虚拟用户。

### 1.修改配置文件：

**服务端**

```shell
[root@localhost html]# vim /etc/rsyncd.conf
#若配置文件不存在则直接创建
[root@localhost html]# cat /etc/rsyncd.conf
address = 192.168.142.132
port 873
pid file = /var/run/rsyncd.pid
log file = /var/log/rsyncd.log

[share]
	comment = soft
	path = /server/rsync
	read only = yes
	dont compress = *.gz *.bz2 *.zip
	auth users = wang
	secrets file = /etc/rsyncd_users.db
[root@localhost html]# vim /etc/rsyncd_users.db
[root@localhost html]# cat /etc/rsyncd_users.db
wang:123456 #rsync不支持复杂密码，尽量设简单一点。
[root@localhost html]# vim /etc/xinetd.d/rsync
[root@localhost html]# cat /etc/xinetd.d/rsync
# default: off
# description: The rsync server is a good addition to an ftp server, as it \
#   allows crc checksumming etc.
service rsync
{
	disable = yes
	flags   = IPv6
	socket_type = stream
	wait= no
	user= root
	server  = /usr/bin/rsync
	server_args = --daemon
	log_on_failure  += USERID
}

[root@localhost html]# rsync --daemon #启动rsync
[root@localhost html]# netstat -pantu | grep 873
tcp0  0 192.168.142.132:873 0.0.0.0:*   LISTEN  6779/rsync  
[root@localhost html]# mkdir -p /server/rsync
[root@localhost html]# cd !$
cd /server/rsync
[root@localhost rsync]# touch rsync.txt
[root@localhost rsync]# ls
rsync.txt
[root@localhost rsync]# chmod 600 /etc/rsyncd_users.db #一定要给密码文件赋予600权限，否则同步数据将出错！
```

### 2.执行同步操作：

**客户端**
```shell
  [root@localhost rsync]# rsync -avz wang@192.168.142.132::share /client/rsync
  Password: 
  receiving incremental file list
  ./
  rsync.txt
```

```shell
sent 77 bytes  received 151 bytes  50.67 bytes/sec
total size is 0  speedup is 0.00
[root@localhost rsync]# ls
rsync.txt
#数据同步成功
[root@localhost rsync]# pwd
/client/rsync
```

下行同步已完成，接下来我将演示上行同步：

**服务端**

```shell
#在执行上行同步前一定要修改模块权限和ACL权限
[root@localhost rsync]# vim /etc/rsyncd.conf 
[root@localhost rsync]# cat /etc/rsyncd.conf
address = 192.168.142.132
port 873
pid file = /var/run/rsyncd.pid
log file = /var/log/rsyncd.log

[share]
	comment = soft
	path = /server/rsync
	read only = no #这里一定要改为no
	dont compress = *.gz *.bz2 *.zip
	auth users = wang
	secrets file = /etc/rsyncd_users.db
[root@localhost rsync]# setfacl -m u:nobody:rwx /srver/rsync #设置ACL权限
[root@localhost rsync]# pkill rsync #关闭rsync
[root@localhost rsync]# rsync --daemon #启动rsync
```

**客户端**

```shell
[root@localhost rsync]# touch client.txt
[root@localhost rsync]# rsync -avz /client/rsync/* wang@192.168.142.132::share
Password: 
sending incremental file list
client.txt

sent 85 bytes  received 27 bytes  32.00 bytes/sec
total size is 0  speedup is 0.00
#上行同步成功
在服务端查看：

[root@localhost rsync]# ls
client.txt  rsync.txt
[root@localhost rsync]# pwd
/server/rsync
```

### 3、上行同步的另一种格式

**客户端**

```shell
[root@localhost rsync]# ls
client.txt  rsync.txt
[root@localhost rsync]# touch test.txt
[root@localhost rsync]# rsync -avz /client/rsync/* rsync://wang@192.168.142.132/share
Password: 
sending incremental file list
test.txt

sent 102 bytes  received 27 bytes  28.67 bytes/sec
total size is 0  speedup is 0.00
```

**服务端**

```shell
[root@localhost rsync]# ls
client.txt  rsync.txt  test.txt
```

## 五、配置免密码验证

### 1、基于SSH的同步源

通过秘钥对实现

**客户端**

```shell
[root@localhost ssh]# pwd
/client/ssh
[root@localhost ssh]# ls
a.txt  b.txt  index.html
[root@localhost ssh]# rm -rf *
[root@localhost ssh]# ssh-keygen
Generating public/private rsa key pair.

Enter file in which to save the key (/root/.ssh/id_rsa): Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
3d:fe:c8:0e:2c:b7:90:b0:f4:0d:31:af:b4:d3:9e:87 root@localhost.localdomain
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|      o          |
|       + .       |
|    o o S o      |
|   . = O . .     |
|    . O *..      |
|       *E=.o     |
|        +o+ .    |
+-----------------+
[root@localhost ssh]# 
[root@localhost ssh]# ssh-copy-id server@192.168.142.132
server@192.168.142.132's password: 
Now try logging into the machine, with "ssh 'server@192.168.142.132'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.

[root@localhost ssh]# id server #server用户在服务端
id: server: No such user
[root@localhost ssh]# ssh server@192.168.142.132
[server@localhost ~]$ ifconfig
#成功登录服务端
eth0      Link encap:Ethernet  HWaddr 00:0C:29:BE:68:3F  
          inet addr:192.168.142.132  Bcast:192.168.142.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:febe:683f/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:935 errors:0 dropped:0 overruns:0 frame:0
          TX packets:660 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:112043 (109.4 KiB)  TX bytes:89842 (87.7 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:16 errors:0 dropped:0 overruns:0 frame:0
          TX packets:16 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:960 (960.0 b)  TX bytes:960 (960.0 b)

[server@localhost ~]$ exit
logout
Connection to 192.168.142.132 closed.
[root@localhost ssh]# ls
[root@localhost ssh]# pwd
/client/ssh
[root@localhost ssh]# rsync -avz server@192.168.142.132:/var/www/html/* /client/ssh/
receiving incremental file list
a.txt
b.txt
index.html
#现在执行同步操作不需要输入密码
sent 68 bytes  received 219 bytes  191.33 bytes/sec
total size is 27  speedup is 0.09
[root@localhost ssh]# ls
a.txt  b.txt  index.html
#被删除的文件又从服务端同步过来了
```

### 2、基于rsync的同步源

通过系统变量实现

RSYNC_PASSWORD

**客户端**

```shell
[root@localhost client]# cd rsync/
[root@localhost rsync]# ls
client.txt  rsync.txt  test.txt
[root@localhost rsync]# rm -rf *
[root@localhost rsync]# export RSYNC_PASSWORD=123456 #123456为虚拟用户wang的密码
[root@localhost rsync]# rsync -avz wang@192.168.142.132::share /client/rsync
receiving incremental file list
./
client.txt
rsync.txt
test.txt
#现在执行同步操作不需要输入密码
sent 115 bytes  received 265 bytes  760.00 bytes/sec
total size is 0  speedup is 0.00
[root@localhost rsync]# ls
client.txt  rsync.txt  test.txt
#被删除的文件又从服务端同步过来了
```