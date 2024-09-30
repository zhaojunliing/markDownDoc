# Centos防火墙设置与端口开放

## 一、iptables

1.打开/关闭/重启防火墙

```shell
chkconfig iptables on 		#开启防火墙(重启后永久生效)
chkconfig iptables off		#关闭防火墙(重启后永久生效)
service iptables start		#开启防火墙(即时生效，重启后失效)
service iptables stop		#关闭防火墙(即时生效，重启后失效)
service iptables restartd	#重启防火墙
```

2.查看打开的端口

```shell
/etc/init.d/iptables status
```

3.打开某个端口(以8080为例)

（1）开启端口

```shell
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
```
（2）保存并重启防火墙
```shell
/etc/rc.d/init.d/iptables save
/etc/init.d/iptables restart
```
4.打开49152~65534之间的端口
```shell
iptables -A INPUT -p tcp --dport 49152:65534 -j ACCEPT
```
同样，这里需要对设置进行保存，并重启防火墙。

5.其他打开方式

我们还可以通过修改/etc/sysconfig/iptables文件的方式开启端口，如下
```shell
vi /etc/sysconfig/iptables
```
然后在文件中增加一行
```shell
-A RH-Firewall-1-INPUT -m state –state **NEW** -m tcp -p tcp –dport 8080 -j ACCEPT
```

参数说明:

–A 参数就看成是添加一条规则
–p 指定是什么协议，我们常用的tcp 协议，当然也有udp，例如53端口的DNS
–dport 就是目标端口，当数据从外部进入服务器为目标端口
–sport 数据从服务器出去，则为数据源端口使用
–j 就是指定是 ACCEPT -接收 或者 DROP 不接收

禁止ping主机

```shell
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
sudo iptables -A OUTPUT -p icmp --icmp-type echo-reply -j DROP
```

## 二、firewalld

Centos7默认安装了firewalld，如果没有安装的话，可以使用 yum install firewalld firewalld-config进行安装。

1.启动防火墙
```shell
systemctl start firewalld
```
2.禁用防火墙
```shell
systemctl stop firewalld
```
3.设置开机启动
```shell
systemctl enable firewalld
```
4.停止并禁用开机启动

```shell
systemctl disable firewalld
```
5.重启防火墙
```shell
firewall-cmd --reload 
```
6.查看状态
```shell
systemctl status firewalld或者 firewall-cmd --state
```
7.查看版本

```shell
firewall-cmd --version
```
8.查看帮助
```shell
firewall-cmd --help
```
9.查看区域信息
```shell
firewall-cmd --get-active-zones
```
10.查看指定接口所属区域信息
```shell
firewall-cmd --get-zone-of-**interface**=eth0
```
11.拒绝所有包

```shell
firewall-cmd --panic-on
```
12.取消拒绝状态
```shell
firewall-cmd --panic-off
```
13.查看是否拒绝
```shell
firewall-cmd --query-panic
```
14.将接口添加到区域(默认接口都在public)
```shell
firewall-cmd --zone=public --add-interface=eth0(永久生效再加上 --permanent 然后reload防火墙
```
15.设置默认接口区域
```shell
firewall-cmd --set-default-zone=public(立即生效，无需重启)
```
16.更新防火墙规则
```shell
firewall-cmd --reload或firewall-cmd --complete-reload(两者的区别就是第一个无需断开连接，就是firewalld特性之一动态添加规则，第二个需要断开连接，类似重启服务)
```
17.查看指定区域所有打开的端口
```shell
 firewall-cmd --zone=public --list-ports  
 
 firewall-cmd --zone=public --list-all
```
18.在指定区域打开端口（记得重启防火墙）
```shell
firewall-cmd --zone=public --add-port=80/tcp(永久生效再加上 --permanent)
```
> 说明：
> –zone 作用域
> –add-port=8080/tcp 添加端口，格式为：端口/通讯协议
> –permanent #永久生效，没有此参数重启后失效



参考：https://www.imsxm.com/2018/05/using-firewalls-on-centos-7.html

### 四、常规配置示例

FirewallD 有两个配置集：“运行时”和“持久”。
运行时：在系统重新启动或重新启动 FirewallD时，不会保留运行时的配置更改；
持久： 持久配置集的更改不会应用于正在运行的系统。（重新加载配置可以生效）

默认情况下**，firewall--cmd 命令适用于运行时配置，但使用 --permanent 标志将保存到持久配置中**。

```
firewall-cmd --zone=public --add-service=ssh --timeout=5m
```

说明：该命令的有效期为5分钟（s 秒  m分  h时）

　　1、启动或禁止http服务

```
[root@text01 ~]# firewall-cmd --zone=public --add-service=http --permanent
success
[root@text01 ~]# firewall-cmd --zone=public --remove-service=http --permanent
success
```

2、只允许指定IP地址访问ssh（端口修改成了20000）

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="116.255.126.42/32" port protocol="tcp" port="50000" accept

firewall-cmd --permanent --remove-service=ssh
firewall-cmd --reload
```

![img](F:\github\markDownDoc\linux\assets\f44ac588f23c881ac57a1339c260e13f.png)

1、禁止ping主机

```
sudo firewall-cmd --permanent --add-icmp-block=echo-request
sudo firewall-cmd --permanent --add-icmp-block=echo-reply
sudo firewall-cmd --reload
```

2、允许或禁用12345端口的TCP流量

```
firewall-cmd --zone=public --add-port=12345/tcp --permanent
firewall-cmd --zone=public --remove-port=12345/tcp --permanent
```

　　3、端口转发

 　在用一台服务上将80端口的流量转发到12345端口上

```
    [root@text01 ~]# firewall-cmd --zone="public" --add-forward-port=port=80:proto=tcp:toport=12345
success
```

　　将端口转发到另外一台服务器上：

　　　　在需要的区域激活masquerade

```
firewall-cmd --zone=public --add-masquerade
```

　　　　将本地的80端口的流量转发到IP地址为：10.0.0.90的远程服务器上的8080端口上

```
firewall-cmd --zone="public" --add-forward-port=port=80:proto=tcp:toport=8080:toaddr=10.0.0.90
```

　　　　4、删除规则，用--remove替换--add

```
firewall-cmd --zone=public --remove-masquerade
```

### 五、高级配置示例

服务和端口适用于基本配置，但对于高级情景可能会限制较多。 丰富Rich规则和直接Direct接口允许你为任何端口、协议、地址和操作向任何区域 添加完全自定义的防火墙规则。

可以使用 nam firewalld.richlanguage

使用 `--add-rich-rule`、`--list-rich-rules` 、 `--remove-rich-rule` 和 firewall-cmd 命令来管理它们。

### 常见例子：

　　1、允许来自主机 192.168.0.14 的所有 IPv4 流量

```
firewall-cmd --zone=public --add-rich-rule 'rule family="ipv4" source address=192.168.0.14 accept'
```

　　2、拒绝来自主机 192.168.1.14 到 22 端口的 IPv4 的 TCP 流量。

```
 firewall-cmd --zone=public --add-rich-rule 'rule family="ipv4" source address="192.168.1.14" port port=22 protocol=tcp reject'
```

　　3、允许来自主机 10.1.0.3 到 80 端口的 IPv4 的 TCP 流量，并将流量转发到 6532 端口上。 

```
firewall-cmd --zone=public --add-rich-rule 'rule family=ipv4 source address=10.1.0.3 forward-port port=80 protocol=tcp to-port=6532'
```

　　　4、将主机 172.31.4.2 上 80 端口的 IPv4 流量转发到 8080 端口（需要在区域上激活 masquerade）。

```
firewall-cmd --zone=public --add-rich-rule 'rule family=ipv4 forward-port port=80 protocol=tcp to-port=8080 to-addr=172.31.4.2'
```

　　　5、列出你目前的丰富规则：

```
firewall-cmd --list-rich-rules
```

​     6、指定一个固定IP访问指定端口

```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="16.25.12.2" port protocol="tcp" port="21" accept"
```

### 六、iptables的直接接口

FirewallD 提供了一个直接Direct接口，允许你给它传递原始 iptables 命令。 直接接口规则不是持久的，除非使用 `--permanent`。

要查看添加到 FirewallD 的所有自定义链或规则：

```
firewall-cmd --direct --get-all-chains
firewall-cmd --direct --get-all-rules
```

###  七、开放一定范围的端口

```
Firewall开启常见端口命令：
firewall-cmd --zone=public --add-port=80/tcp --permanent

Firewall关闭常见端口命令：
firewall-cmd --zone=public --remove-port=80/tcp --permanent

批量添加区间端口
firewall-cmd --zone=public --add-port=4400-4600/udp --permanent

重启防火墙命令：
firewall-cmd --reload  或者   service firewalld restart

查看端口列表：
firewall-cmd --permanent --list-port

对指定IP地址开放一定范围的端口
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="16.25.12.2" port protocol="tcp" port="10000-20000" accept"
```