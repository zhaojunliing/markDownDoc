# Ubantu修改静态IP地址

## 一、修改静态IP

1、查看物理网卡配置信息
```shell
root@zzb-System-Product-Name:/home/baidu# ifconfig
docker0   Link encap:以太网  硬件地址 02:42:54:e1:31:54  
          inet 地址:172.17.0.1  广播:0.0.0.0  掩码:255.255.0.0
          inet6 地址: fe80::42:54ff:fee1:3154/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
          接收数据包:77173 错误:0 丢弃:0 过载:0 帧数:0
          发送数据包:73953 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:0 
          接收字节:4688415 (4.6 MB)  发送字节:6900636 (6.9 MB)

enp4s0    Link encap:以太网  硬件地址 40:b0:76:5f:6f:7e  
          inet 地址:192.168.10.205  广播:192.168.10.255  掩码:255.255.255.0
          inet6 地址: fe80::42b0:76ff:fe5f:6f7e/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
          接收数据包:6507642 错误:0 丢弃:4 过载:0 帧数:0
          发送数据包:5209672 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:1000 
          接收字节:6737143132 (6.7 GB)  发送字节:4251237554 (4.2 GB)

lo        Link encap:本地环回  
          inet 地址:127.0.0.1  掩码:255.0.0.0
          inet6 地址: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  跃点数:1
          接收数据包:5569 错误:0 丢弃:0 过载:0 帧数:0
          发送数据包:5569 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:1000 
          接收字节:612557 (612.5 KB)  发送字节:612557 (612.5 KB)
```

**物理网卡名称为 enp4s0**
2、编辑静态IP地址
```shell
vi /etc/network/interfaces

# 增加enp4s0网卡对应的信息
# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback
	
auto enp4s0
iface enp4s0 inet static
address 192.168.10.205
gateway 192.168.10.10
netmask 255.255.255.0
```

增加需要修改的静态IP即可，修改的IP地址不可与当前地址一样，否则无法修改成功。。
```shell
root@zzb-System-Product-Name:/# /etc/init.d/networking restart 
[ ok ] Restarting networking (via systemctl): networking.service.
root@zzb-System-Product-Name:/# ip addr flush dev enp4s0; ifdown enp4s0; ifup enp4s0
RTNETLINK answers: No such process
RTNETLINK answers: No such process
RTNETLINK answers: Cannot assign requested address
```
重启网络服务，然后刷新网卡并禁用启用网卡。
如果不进行禁用启用网卡的话会两个IP同时可以访问。

##二、修改DNS配置

```shell
vi /etc/resolv.conf

# 增加DNS地址
nameserver 192.168.10.10
# 刷新DNS
resolvconf -u
# 重启网络
/etc/init.d/networking restart 
# (关于 resolvconf 服务更多信息，可以用man查看：man resolvconf )
```