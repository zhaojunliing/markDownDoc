1.先登录sqlplus：

```shell
[oracle@fhdatabase ~]$ sqlplus / as sysdba;
```

2.关闭数据库并以mount模式启动 

```sql
SQL> shutdown immediate;
Database closed.
Database dismounted.
ORACLE instance shut down.

SQL> startup mount;
ORACLE instance started.

Total System Global Area 2505338880 bytes
Fixed Size		    2255832 bytes
Variable Size		  754975784 bytes
Database Buffers	 1728053248 bytes
Redo Buffers		   20054016 bytes
Database mounted.

```

3.移动表空间文件

```shell
[oracle@fhdatabase orcl]$ mv /home/oracle/app/oradata/orcl/lkj_wf02.dbf /u02/oradata/orcl/lkj_wf02.dbf
```

4.修改oracle表空间指向地址

```sql
SQL> alter database rename file '/home/oracle/app/oradata/orcl/lkj_wf02.dbf' to  '/u02/oradata/orcl/lkj_wf02.dbf';
```

5.关闭数据库并重新启动

```sql
SQL> shutdown immediate;
ORA-01109: database not open


Database dismounted.
ORACLE instance shut down.

SQL> startup
ORACLE instance started.

Total System Global Area 2505338880 bytes
Fixed Size		    2255832 bytes
Variable Size		  754975784 bytes
Database Buffers	 1728053248 bytes
Redo Buffers		   20054016 bytes
Database mounted.
Database opened.
```

6.查询表空间位置是否改变

```sql
select * from DBA_DATA_FILES;
```

7.附

注意如果报错出现  ora-01113   ora-01110

就执行

```
recover datafile '/u02/oradata/orcl/lkj_wf02.dbf';

alter database open;

alter tablespace SYSTEM offline;  ---表空间离线

alter tablespace SYSTEM online;   --表空间在线 
```
