# ORA-21561: OID generation failed

解决方法
打开终端，查看 hostname

```
hostname
```

发现是 local 而不是 localhost

查看 hosts 文件

```
cd /etc  
open hosts

```
内容为
```shell
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1   localhost
255.255.255.255 broadcasthost
::1             localhost 
```

问题已经找到了，因为 hostname 和 hosts 文件里的 hostname 不一致
解决办法，修改 hostname 与 hosts 里保持一致

```
sudo scutil --set HostName localhost
```

重新连接 Oracle