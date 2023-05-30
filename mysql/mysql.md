# MySQL8.0.30(zip版)windows安装过程

这篇文章主要介绍了MySQL8.0.30(zip版)windows安装,进入到mysql下载页面后选择zip版本点击下载，下载完以后把它解压到自己想存放的位置

## 一、下载

mysql下载地址

```
https://dev.mysql.com/downloads/mysql/
```

进入到mysql下载页面后选择zip版本点击下载

点击下载以后出现以下页面,点击 No thanks, just start my download 开始下载

## 二、解压

下载完以后把它解压到自己想存放的位置,我的是D盘

它里面是没有my.ini这个配置文件的所以我们要手动创建一个my.ini配置文件



## 三、创建my.ini配置文件

首先在解压好的mysql目录下新建一个txt文件,用记事本打开新建的txt文件,输入以下内容

```ini
[mysqld]
# 设置3306端口
port=12345
# 设置mysql的安装目录
basedir=D://software/mysql
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
#回滚
server_id = 1
log_bin = mysql-bin.log　　
#设置log文件保存路径，默认为mysql的data目录下
max_binlog_size = 512M　　
#最大log
binlog_format = row
binlog_row_image = full
# 事件调度器启动状态
event_scheduler = on
#重置密码用
#skip-grant-tables
# 解决无法写入空时间，解除严格限制模式
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=12345
default-character-set=utf8
```

## 四、配置环境变量

```SHELL
MYSQL_HOME
D:\Program Files10\mysql-8.0.30-winx64

Path增加
%MYSQL_HOME%\bin
```

## 五、安装

1.接下来就开始正式安装mysql了,首先以管理员身份运行cmd,并输入以下命令进入mysql安装目录的bin目录下

```
进入到D盘
D:
进入到mysql安装目录
cd D:\Program Files10\mysql-8.0.30-winx64\bin
```

```shell
C:\WINDOWS\system32>d:

D:\>cd Program Files10\mysql-8.0.30-winx64\bin
```

2.进入到安装目录后输入以下命令进行mysql的初始化

```shell
mysqld --initialize --user=mysql --console
```

初始化完成以后回出现以下内容,红框圈起来的地方是临时密码记下来后面登录mysql时要用到

```shell
D:\Program Files10\mysql-8.0.30-winx64\bin>mysqld --initialize --user=mysql --console
2022-12-09T01:46:19.376541Z 0 [Warning] [MY-010915] [Server] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2022-12-09T01:46:19.377098Z 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
2022-12-09T01:46:19.377114Z 0 [System] [MY-013169] [Server] D:\Program Files10\mysql-8.0.30-winx64\bin\mysqld.exe (mysqld 8.0.30) initializing of server in progress as process 16960
2022-12-09T01:46:19.378496Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2022-12-09T01:46:19.480260Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-12-09T01:46:29.893754Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-12-09T01:46:36.658716Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: F8CLbWAhmx.:

D:\Program Files10\mysql-8.0.30-winx64\bin>
```

3.MySQL服务安装

初始化完成以后就开始mysql服务的安装,输入以下命令进行mysql服务的安装

```shell
#安装mysql服务
mysqld --install mysql
#移除musql服务
mysqld --remove
```

出现下面这个信息就表示服务安装成功了

```shell
D:\Program Files10\mysql-8.0.30-winx64\bin>mysqld --install mysql
Service successfully installed.
```

4.启动mysql服务

mysql服务安装成功以后就是启动服务,输入以下命令启动mysql服务

```shell
#启动服务 
net start mysql
#停止服务 
#net stop mysq
```

```shell
D:\Program Files10\mysql-8.0.30-winx64\bin>net start mysql
mysql 服务正在启动 .....
mysql 服务已经启动成功。

```

## 六、登录并修改初始密码

mysql服务启动成功以后,就开始修改初始密码了

1.登录mysql

输入以下命令登录mysql

```
mysql -uroot -p
```

输入命令回车以后会让输入密码.这个密码就是初始化时的密码

```shell
C:\WINDOWS\system32>mysql -uroot -p
Enter password: ************
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.30

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

2.修改默认密码

输入以下命令修改mysql默认密码

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

```sql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'xxxxxxx';
Query OK, 0 rows affected (0.06 sec)
```

修改root用户权限默认密码

```sql
 create user 'root'@'%'IDENTIFIED WITH mysql_native_password BY '新密码';
```

```sql
mysql>  create user 'root'@'%'IDENTIFIED WITH mysql_native_password BY 'xxxxxxx';
Query OK, 0 rows affected (0.05 sec)
```

3.刷新权限

密码修改完以后需要刷新下权限,命令如下

```sql
flush privileges;
```

```shell
mysql> flush privileges;
Query OK, 0 rows affected (0.02 sec)
```

权限刷新以后输入quit退出,然后就可以用navicat或者其他一些可视化软件链接MySQL啦