# oracle 11g逻辑备份介绍-expdp/impdp #

Data Pump（数据泵） 是从oracle10g 引入的全新的，用于快速迁移数据的方式。Data Pump与exp/imp最大的区别是，Data Pump是一个位于服务器端的工具，通过Data Pump导出的转储文件只能位于数据库服务器端，而不能将该转储文件导出导客户端。 Data Pump 有以下优点：

1. 任务可以暂停，重启
2. 可以只处理某些对象，不处理某些对象，或者只处理某些对象中满足条件的数据。
3. 在不实际执行导出的情况下，估计需要的磁盘空间
4. 通过db link 将远程数据库导出到转储文件
5. 通过db link，直接将远程数据库中的数据导入到当前数据库
6. 导入时可以修改数据所在的schema名称，表空间名称以及数据文件名称。
7. 通过采样，导出部分数据。
8. 只导出元数据。
9. 并行操作。

## 一、expdp - 数据导出 ##

Data Pump导出程序expdp能够按照几种方式进行，其中包括数据库、对象模式、表和表空间.

Data Pump通过指定目录对象（directory object）来控制生成的转储文件应该放在服务器哪个目录下。

使用create directory语句创建目录对象

	SQL>create directory dump_dir as ‘/oracle/datapump/dumps’
	SQL>create directory log_dir as ‘/oracle/datapump/logs’

将目录对设置为公共读写

	SQL>grant read,write on directory dump_dir to public;

授权

	SQL>grant read,write on directory dump_dir to scott

### 1、数据库导出方式 ###

	$expdp system/passwd directory= dump_dir dumpfile=alldb.dmp full=y

### 2、对象模式导出方式 ###

	$expdp scott/tiger directory= dump_dir dumpfile=scott.dmp schemas=scott

### 3、表导出方式 ###

	$expdp scott/tiger directory= dump_dir dumpfile=tables.dmp nologfile=y content=metadata_only

**参数说明：**  
nologfile:表示不会操作日志写入磁盘
content=metadata_only:表示仅是导出元数据，而不会导出表中的数据，如果content=data_only则恰好相反，默认情况下二者全部导出

### 4、表空间导出方式 ###

	$expdp system/passwd dumpifle=backup:users_ts.dmp logifle=backup:users_ts.log tablespaces=users;

### 二、impdp - 数据导入 ###

Data Pump导入程序impdp同样可以按照全部、对象、表和表空间四种方式。在使用导入数据泵时，比较常用的选项为转换参数，如下：

REMAP_TABLESPACE：用于将对象从一个表空间导入到另一个表空间，比如指定
remap_tablespace=users:example
说明将位于users表空间中的对象导入到example表空间
REMAP_SCHEMA：用于将对象跨用户迁移。例如
remap_schema=hr:scott
指定将hr用户下的对象迁移到scott用户下。

举例：

### 1．导入指定的表 ###

	$impdp user1/passwd directory=backup dumpfile=exp01.dmp tables=(emp) remap_schema=hr:user1 remap_tablespace=users:example

### 2．导入整个数据库 ###

	$impdp system/passwd full=y dumpfile=backup:alldb.dmp nologfile=y sqlfile=backup:alldb.sql

参数sqlfile=backup:alldb.sql表示将元数据(DDL数据)写入到指定目录文件中。backup是一个数据库目录对象

### 3. 通过db link 进行跨平台的数据迁移 ###

创建数据库链接：

	SQL>create database link orcl connect to user1 identified by passwd using 'orcl'

创建impdb 参数文件：imp_par.txt

	network_link = orc1
	remap_schema = user1:hr
	remap_tablespace = example:users
	schemas = user1
	job_name = cross_network

上述参数文件的目的是将远程数据库orcl中用户user1下的所有对象导出到当前数据库中hr用户下。
其中，user1用户下的对象存放在example数据空间中，hr用户下的数据存放在users数据空间中。