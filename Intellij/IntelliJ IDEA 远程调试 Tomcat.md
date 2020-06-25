# IntelliJ IDEA 远程调试 Tomcat

## 准备工作


- 明确远程服务器的 IP 地址，比如我是：192.168.10.72
- Linux 关掉服务器防火墙：service iptables stop
- Windows手动关闭防火墙，保证远程调试的端口可以连接上，也可以尝试使用telnet 192.168.10.72 xxxx 测试远程服务器已经打开的端口。
- Tomcat版本保持一致

## 本地 Remote Server 配置

1. 添加 Remote Server，如下图
2. 填写服务器信息
3. 查看参数，复制 Remote Server 自动生成的 JVM 参数，等下有用，如下图，

比如我的是：
```shell
agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=62188
```
把刚刚复制参数加个前缀，变成：
Linux（有单引号）：

```shell
export JAVA_OPTS='-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=62188'
```
Windows（没有单引号）：
```shell
set JAVA_OPTS=-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=62188
```
>备注：切记勿部署任何项目，idea会自动查找本地的项目源码，修改源码后也会同步更新到服务器。

