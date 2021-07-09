# Ubuntu中防火墙的使用和开放、关闭端口

## Ubuntu查看防火墙的状态
Ubuntu系统默认是安装了ufw防火墙的。
查看防火墙的状态：

```
sudo ufw status

root@psca:~# ufw status
Status: inactive
```

inactive表示防火墙没有开启，并不是没有安装防火墙。
安装防火墙：

```
sudo apt-get install ufw

#Ubuntu开启防火墙
sudo ufw enable

root@psca:~# sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? 
```

命令可能会中断现有的ssh连接。继续操作(y|n)?

因为是在远程的Xshell进行连接开启防火墙的，有的系统是没有将SSH的22端口设置为public的，所以会有这样的提示，这里分为两种情况，如果开启防火墙时在防火墙之中检测到22端口已添加为防火墙的开放端口，那么输入y继续操作以后，当前Xshell会自动断开连接；相反，如果开启防火墙时在防火墙之中没有检测到22端口，那么输入y继续操作以后22端口将会不再支持其他连接，只支持当前已有的这个连接，保持当前连接的原因是可以通过该连接开放22端口。

这里之前没有设置过，直接输入y继续执行。

查看防火墙的状态：

```
root@psca:~# sudo ufw status
Status: active
```

可以看到系统没有对外开发任何端口。
在这里通常会有些错觉，22端口没有开放，但是依然是连接状态，这是系统做的人性化优化，便于远程管理服务器，虽然22端口没有开放，但是用户通过当前的连接开启防火墙后，该连接依然处于连接状态，只要不关闭当前连接还是可以在当前连接中正产操作的。如果是重新开启一个连接是连不上的。

## Ubuntu防火墙添加开放普通端口
开放22端口：

```
sudo ufw allow 22
```

开启完成，需要重启防火墙生效：

```
sudo ufw reload
```

查看防火墙的状态：

```
root@psca:~# sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere                  
22 (v6)                    ALLOW       Anywhere (v6)  
```

22端口已经开放。
查看22端口的监听状态：

```
sudo netstat -tunlp | grep 22
```

```
root@psca:~# sudo netstat -tunlp | grep 22
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1744/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      1744/sshd   
```

22端口已经处于监听状态。

## Ubuntu防火墙关闭普通端口

```
sudo ufw delete allow 21
```

## Ubuntu防火墙开放规定协议的端口

```
sudo ufw allow 8001/tcp
```

## Ubuntu防火墙关闭指定协议端口

```
sudo ufw delete allow 8001/tcp 
```

## Ubuntu防火墙开放限定ip地址端口
1、开放指定ip所有操作

```
sudo ufw allow from 192.168.1.11
```

```
root@psca:~# ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere                  
80                         ALLOW       Anywhere                  
8001/tcp                   ALLOW       Anywhere                  
Anywhere                   ALLOW       192.168.1.11              
```

2、关闭指定ip所有操作

```
sudo ufw delete allow from 192.168.1.11
```

3、开放指定ip对应端口操作

```
sudo ufw allow from 192.168.1.12 to any port 3306
```

```
root@psca:~# ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere                  
80                         ALLOW       Anywhere                  
8001/tcp                   ALLOW       Anywhere                  
Anywhere                   ALLOW       192.168.1.11              
3306                       ALLOW       192.168.1.12    
```

4、关闭指定ip对应端口操作

```
sudo ufw delete allow from 192.168.1.12 to any port 3306
```

## Ubuntu关闭防火墙

```
sudo ufw disable
```

