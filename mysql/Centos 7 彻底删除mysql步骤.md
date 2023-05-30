# Centos 7 彻底删除mysql步骤

## 一、删除Mysql安装包

用两种方式检出是否删除干净

### 1. yum检查

```shell
yum list installed | grep mysql
```

有安装则直接删除，删除掉你自己查询后显示的安装包，示例如下：

```shell
yum remove mysql mysql-server mysql-libs compat-mysql
yum remove mysql-community-release
```

注意: 再次执行检查命令，有则删除，直到查询不出结果

## 2. rpm检查
```shell
rpm -qa | grep -i mysql
````

有则直接删除，删除掉你自己查询后显示的安装包，示例如下：

```
rpm -e --nodeps mysql-community-libs-5.7.22-1.el7.x86_64

rpm -e –nodeps mysql57-community-release-el7-11.noarch
```

注意: 再次执行检查命令，有则删除，直到查询不出结果

删除mysql安装目录和残存文件

```
whereis mysql
find / -name mysql
```

将以上命令运行查找到的结果，全部rm -r -f 删除

## 二、删除mysql配置文件
my.cnf为例，一般在/etc/my.cnf，直接rm即可

如果设置了开机启动，也需要关闭

```
chkconfig --list | grep -i mysql
chkconfig --del mysqld
```

重复上述查询操作，删除查询结果文件，直到查询不出结果
注意: 以上操作可以和删除centos7上mysql的绝大部分数据，但是难免有漏掉的配置，还可以自己有针对性的进行查找修改