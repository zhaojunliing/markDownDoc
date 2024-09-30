## oracle归档日志怎么关闭

## 一、关闭归档。

1 sql> archive log list; #查看是否是归档方式

2 sql> alter system set log_archive_start=false scope=spfile; #禁用自动归档

3 sql> shutdown immediate;

4 sql> startup mount; #打开控制文件，不打开数据文件

5 sql> alter database noarchivelog; #将数据库切换为非归档模式

6 sql> alter database open; #将数据文件打开

7 sql> archive log list; #查看此时便处于非归档模式

## 二、更改Oracle为归档模式

  1.关闭oracle

 ```shell
SQL> shutdown immediate;
Database closed.
Database dismounted.
ORACLE instance shut down.
 ```

2.启动为mount状态

 ```
SQL> startup mount
ORACLE instance started.
Total System Global Area 	2572144640 bytes
Fixed Size 					2283984 bytes 
Variable  Size				738199088 bytes
Database Buffers     			1828716544 bytes 
Redo Buffers        			2945024 bytes 
Database mounted.
 ```

3.更改为归档模式

 ```
SQL> alter database archivelog 
Database altered.
 ```

备注：‘archivelog’为 归档模式；‘noarchivelog’为非归档模式。

4.更改数据库为‘打开’状态

 ```sql
SQL> alter database open
 ```

5.查看归档模式信息

 ```sql
SQL> archive log list;
 ```

它会提示归档的模式、是否启用、参数

 ```
Database log mode      	 		Archive Mode 
Automatic archival       			Enabled 
Archive destination      			USE_DB_RECOVERY_FILE_DEST 
Oldest online log  sequence		88 
Next log  sequence to archive	90 
Current log  sequence			90
 ```

