# Zabbix如何通过ODBC对接Oracle获取相关数

据

## 1. 什么是ODBC

ODBC数据源全称是开放数据库互连（Open Database Connectivity），在微软公司开放的数据库结构中的一部分，其实是一个应用程序的接口，主要用于提供数据库的编写应用程序的能力。因为是微软公司发布的软件，ODBC也提供了[SQL语句](https://host.zzidc.com/shujuku/420.html)的支持，用户可以通过ODBC提供的API接口来使用和调度ODBC驱动程序，然后ODBC驱动程序通过SQL语言与数据库管理系统进行联系。

![图片](F:\github\markDownDoc\Zabbix\assets\640.png)



ODBC数据源在每台计算机中基本都存在，以WIN10为例，打开控制面板—系统工具—数据源 (ODBC)，很见到可以找到。双击打开ODBC数据源管理器，该管理器主要用于配置、添加、删除各种不同的数据源，ODBC数据源对此专门进行了简洁化，可以让用户更加方便的进行数据源的配置。

![图片](F:\github\markDownDoc\Zabbix\assets\640-1685410518237-1.png)

ODBC数据源管理器的主要功能如下：

1. 用户DSN：ODBC用户数据源存储了如何与指定数据提供者连接的信息。用户数据源只对当前的用户可见，而且只能应用在本机上。
2. 系统DSN：ODBC系统数据源存储了如何与指定数据提供者连接的信息。系统数据源对当前机器上的所有用户可见。
3. 文件DSN：ODBC文件数据源允许用户连接数据提供者。文件DSN可以由安装了相同驱动程序的用户共享。
4. 驱动程序：ODBC驱动程序允许那些支持ODBC的程序通过ODBC数据源获取信息。如果安装新的驱动程序，要使用其安装程序。
5. 跟踪：ODBC跟踪允许创建调用ODBC驱动程序的日志，以供技术人员查看；也可以辅助调试应用程序。Visual Studio跟踪启动Microsoft Visual Studio的ODBC跟踪。
6. 连接池：连接池允许应用程序重新打开连接句柄，此操作将往返过程存入服务器。

以上是ODBC数据源管理器的主要功能，用户也可以通过ODBC数据源管理器删除原有的DNS，或者对以前配置的数据源进行修改，操作起来也很简单快捷。今天介绍的是Zabbix如何通过ODBC对接Oracle获取相关数据。

Zabbix官方文档请[点击查看](https://www.zabbix.com/documentation/5.0/manual/config/items/itemtypes/odbc_checks)。

### 2. 安装Oracle-instantclient

Instant Client Downloads for Linux x86-64 (64-bit)[下载地址](https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html)。

我这边的Oracle数据库环境是Oracle11GR2 v11.2.0.4；首先去Oracle的官方网站下载最新的Oracle客户端。并在Zabbix主机上进行安装：

```shell
# 我们先把需要用到的四个rpm离线包下载到本地
mkdir -p oracle
cd oracle

wget https://download.oracle.com/otn_software/linux/instantclient/199000/oracle-instantclient19.9-basic-19.9.0.0.0-1.x86_64.rpm
wget https://download.oracle.com/otn_software/linux/instantclient/199000/oracle-instantclient19.9-sqlplus-19.9.0.0.0-1.x86_64.rpm
wget https://download.oracle.com/otn_software/linux/instantclient/199000/oracle-instantclient19.9-devel-19.9.0.0.0-1.x86_64.rpm
wget https://download.oracle.com/otn_software/linux/instantclient/199000/oracle-instantclient19.9-odbc-19.9.0.0.0-1.x86_64.rpm
    
# 在安装客户端之前我们还需要在zabbix上安装unixODBC和unixODBC-devel（这两个包我们直接在线安装即可）
yum -y install unixODBC unixODBC-devel

# 安装完成之后我们直接通过下面的命令安装 oracle-instantclient
yum localinstall oracle-instantclient19.9-*

# oracle-instantclient的安装位置如下
/usr/lib/oracle/19.9/client64
/usr/share/oracle/19.9/client64
/usr/include/oracle/19.9/client64
```

### 3. 配置SQLPlus连接

在设置环境变量之前，需要在/usr/lib/oracle/19.9/client64下创建 network/admin 文件夹，用来配置 tnsnames.ora ：

```shell
# 创建 network/admin 文件夹
mkdir /usr/lib/oracle/19.9/client64/network/admin -p

# 配置 tnsnames.ora 文件
Oracle=
   (DESCRIPTION =
     (ADDRESS = (PROTOCOL = TCP)(HOST = 172.16.200.110)(PORT = 1521))
     (CONNECT_DATA =
       (SERVER = DEDICATED)
       (SERVICE_NAME = 数据库SID)
     )
   )
```

注：这里也可以去创建 listener.ora和sqlnet.ora 配置文件。创建 sqlnet.ora 作用是配置解析顺序， name/password@xxxxx xxxx会先去tnsnames寻找这个名字，没有的话会解析为IP NAME_DIRECTORY_PATH=(TNSNAMES,EZCONNECT) 。感兴趣的小伙伴可以自定测试，这里不再演示。

还需要配置环境变量。环境变量必须配置，否则会造成 [01000] [unixODBC](https://blog.z0ukun.com/?p=3084#)Can’t open lib ‘/usr/lib64/lxxxxx.so’ : file not found （执行一次即可）

```shell
export ORACLE_HOME=/usr/lib/oracle/19.9/client64
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/usr/lib64:$LD_LIBRARY_PATH
export TNS_ADMIN=$ORACLE_HOME/network/admin
export PATH=$PATH:$ORACLE_HOME/bin:$HOME/bin
```

配置需要的类库

```shell
chmod +x /usr/lib/oracle/19.9/client64/lib/libsqora.so.19.1
cd /usr/lib64/
ln -s libodbcinst.so.2.0.0 libodbcinst.so.1
```

添加Oracle驱动

```shell
# Example driver definitions
# vi /etc/odbcinst.ini
[Oracle]
Description     = Oracle ODBC driver for Oracle 11g
Driver          = /usr/lib/oracle/19.9/client64/lib/libsqora.so.19.1
```

添加Oracle数据源

```shell
# vi /etc/odbc.ini
# 配置示例
[DSN名称]                               将在zabbix item key中使用
Driver = oracle                         tnsnames.ini 配置的驱动名称
ServerName = ip:port/sid               ip:port/sid
UserID = 用户名                         用户名
Password = 密码                         密码

# 具体配置
[Oracle]
Driver = Oracle
ServerName = 172.16.200.241:1521/数据库SID
UserID = XXX
Password = XXX
```

ISQL测试

```shell
isql -v Oracle
# 检测是否可连接及中间如遇到报错的错误信息（这里的Oracle要和DSN名称一致）
[root@ZabbixServer]# isql -v Oracle
+---------------------------------------+
| Connected!                           |
|                                       |
| sql-statement                         |
| help [tablename]                     |
| quit                                 |
|                                       |
+---------------------------------------+
SQL>
```

SQLPlus测试连接

```sql
# sqlplus 用户名/密码@tnsnames.ora中配置的连接名称
[root@ZabbixServer ~]# sqlplus 用户名/用户密码@DSN名称

SQL*Plus: Release 19.0.0.0.0 - Production on Sun Dec 6 11:34:11 2020
Version 19.9.0.0.0

Copyright (c) 1982, 2020, Oracle. All rights reserved.


???: 
Oracle Database 11g Enterprise Edition Release 11.2.0.4.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL> 
```

### 4. 配置Zabbix监控项

测试正常之后就可以去Zabbix里面添加数据库架监控项，需要填入键值、用户名称、密码、具体的SQL查询语句、更新时间；信息类型修改为文本。具体配置信息如下：

![图片](F:\github\markDownDoc\Zabbix\assets\640-1685410518237-2.png)

![图片](F:\github\markDownDoc\Zabbix\assets\640-1685410518238-3.png)

配置完成以后我们可以点击下方的测试按钮，如果在结果里能获取到Result converted to 文本 OPEN 信息就代表成功了；可以去检测-最新数据里面看到已经成功拿到的数据。

![图片](F:\github\markDownDoc\Zabbix\assets\640-1685410518238-4.png)

![图片](F:\github\markDownDoc\Zabbix\assets\640-1685410518238-5.png)