# Linux常用命令之配置及硬件检测

## 前言

>  使用Linux系统，很有必要熟悉一些常用的命令。本文收集整理一些常用的用于检测服务器的配置和硬件信息的命令，需要时可查阅。包括：操作系统、CPU、内存、硬盘分区、系统时间、负载、网络相关、进程、用户、开关机、启动等方面，适用于主流Linux操作系统比如centos、ubuntu、debian等 
>

## 操作系统版本

```shell
$ uname -a      # 查看系统，主机名，内核版本，系统架构等命令

$ top     # 总览系统全面信息命令，Ctrl + C 退出界面

$ hostname     # 查看服务器主机名命令

$ cat /etc/issue     # 查看 Ubuntu Debian 系发行版版本命令

$ cat /etc/redhat-release     # 查看 CentOS RedHat 系发行版版本命令

$ cat /etc/os-release     # 查看通用 Linux 发行版版本命令
```

## CPU相关命令

```shell
$ cat /proc/cpuinfo     # 查看 CPU 核心数，架构，名字，频率，缓存，指令集等命令
$ grep name /proc/cpuinfo     # 查看 CPU 名字命令
$ grep cores /proc/cpuinfo     # 查看 CPU 核心数命令
$ grep MHz /proc/cpuinfo     # 查看 CPU 频率命令
```

## 查看内存命令

```shell
$ cat /proc/meminfo     # 查看内存硬件相关信息命令
$ free -m     # 查看内存总量，使用量，swap 信息等命令
$ swapon -s     # 查看 swap 交换分区的路径，大小命令
```

## 查看硬盘分区及相关信息

```shell
$ df -h     # 查看硬盘分区以及占用情况命令
$ du -sh [指定路径]     # 查看指定路径文件或目录大小命令
$ fdisk -l     # 查看硬盘大小，数量，类型命令
```

## 查看系统时间负载及相关

```shell
$ uptime     # 查看开机时间，系统用户数，平均负载命令
$ cat /proc/loadavg     # 查看系统负载命令
$ w     # 查看系统时间，负载，登入用户，用户使用资源情况命令
$ top     # 总览系统全面信息命令，Ctrl + C 退出界面
```

## 查看网络相关情况

```shell
$ ifconfig     # 查看网卡及本机 ip 情况命令（需要系统安装了 net-tools 工具）
$ ip addr show     # 功能同上，新的 Linux 发行版已经逐渐用 ip 命令替代 ifconfig 相关功能命令
$ iptables -L     # 查看防火墙等相关情况命令
$ netstat -s     # 查看系统网络连接情况统计信息命令
$ netstat -tunlp     # 查看服务器端口监听使用情况命令
$ netstat -auntp     # 查看已经建立连接的端口情况命令
$ lsof -i:[端口]     # 查看指定端口占用情况命令
$ route -n     # 查看路由表命令
```

## 查看进程相关命令

```shell
$ ps -aux     # 列出所有进程以及相关信息命令
$ kill -9 [进程PID]     # 从上命令取到相关进程的PID后，高权限 kill 杀死进程命令命令
$ top     # 总览系统全面信息命令，Ctrl + C 退出界面
```

## 查看用户相关命令

```shell
$ w     # 查看系统时间，负载，登入用户，用户使用资源情况命令
$ cut -d: -f1 /etc/passwd     # 查看系统所有用户命令
$ last     # 查看系统前几次登陆情况
$ crontab -l     # 查看用户计划任务情况命令
$ crontab -e     # 编辑计划任务命令
```

## 查看开机启动相关命令

```shell
$ chkconfig     # 查看开机启动服务命令
$ ls /etc/init.d     # 查看开机启动配置文件命令
$ cat /etc/rc.local     # 查看 rc 启动文件
```



摘自：[ https://www.xcnte.com/archives/611/ ]()