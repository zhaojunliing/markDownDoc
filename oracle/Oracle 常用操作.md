### Oracle 常用操作
#### 创建表空间及用户
```sql
/*第1步：创建临时表空间,一般不需要创建,使用系统temp就行,此步骤可以略过  */
create temporary tablespace xxx_temp  --临时表空间名称
tempfile '/u02/oradata/orcl//xxx_temp .dbf'  --临时表空间文件存储位置
size 10m  --临时表空间文件初始大小
autoextend on  --临时表空间自动增长
next 1m maxsize unlimited    --自动增长文件大小及最大空间
extent management local;  
 
/*第2步：创建数据表空间  */
create tablespace XXX_SPACE  --表空间名称
logging
datafile '/u02/oradata/orcl/XXX_SPACE.dbf'  --表空间文件存储位置
size 10m  --表空间文件初始大小
autoextend on  --表空间自动增长
next 10m maxsize unlimited  --自动增长文件大小及最大空间
extent management local; 

/*第3步：创建用户并指定表空间  */
create user XXX identified by XXX
default tablespace XXX_SPACE
temporary tablespace temp;

/*第4步：给用户授予权限  */
grant connect,resource,dba to XXX;  

---------------------------------------------------
--创建bigfile表空间，最大32TB
create bigfile tablespace testsimis
logging
datafile '/u02/oradata/orcl/testsimis.dbf' 
size 1G 
autoextend on
next 1G maxsize unlimited  --自动增长文件大小及最大空间
extent management local;
```

#### 导出用户表结构
工具--导出用户对象

![导出表结构](https://raw.githubusercontent.com/zhaojunliing/markDownDoc/master/youDaoYun/Oracle/2018-07-17_120313.png)

以单个文件形式导出用户表结构

#### 导出用户表数据
```
-- 导出XXX用户的dmp形式的数据文件
exp XXX/XXX@localhost:1521/orcl file=d:/file_name_YYYYMMdd00.dmp owner="XXX"
```
#### 导入用户表结构
1、工具--导入表

![导入表结构](https://raw.githubusercontent.com/zhaojunliing/markDownDoc/master/youDaoYun/Oracle/2018-07-17_132220.png)

2、导入表结构的时候视图存在空行问题 

在导出的表结构中增加 `SET SQLBLANKLINES ON`即可

```sql
set define off
SET SQLBLANKLINES ON
```

#### 导入用户表数据
```sql
-- 禁用触发器后再进行该操作,导入成功后需要重新启用触发器
-- 只导入表数据
imp YYY/YYY@localhost:1521/orcl file=e:/dbbak/file_name_YYYYMMdd00.dmp fromuser='XXX' touser='YYY' data_only=y

-- 导入指定表数据
imp YYY/YYY@localhost:1521/orcl file=e:/dbbak/file_name_YYYYMMdd00.dmp data_only=y tables=(table_name1,table_name2)
```

#### 数据库表空间操作
```sql
drop tablespace ZDCLBX including contents and datafiles;  --删除表空间及数据文件
select tablespace_name,sum(bytes)/1024/1024/1024 as GB from dba_free_space group by tablespace_name;  -- 查询表空间剩余量
select tablespace_name,sum(bytes)/1024/1024/1024 as GB from DBA_DATA_FILES group by tablespace_name;  -- 查询表空间总量
select tablespace_name,file_name,autoextensible from dba_data_files where autoextensible='YES';  -- 查询自增表空间
select tablespace_name,sum(bytes)/1024/1024/1024 as GB from DBA_TEMP_FILES group by tablespace_name;   -- 查询临时表空间总量


SELECT a.tablespace_name "表空间名",
        total "表空间大小",
        free "表空间剩余大小",
        (total - free) "表空间使用大小",
            total / (1024 * 1024 * 1024 ) "表空间大小(M)",
            free / (1024 * 1024 * 1024 ) "表空间剩余大小(M)",
            (total - free) / (1024 * 1024 * 1024 ) "表空间使用大小(M)",
            round((total - free) / total, 4) * 100 "使用率 %"
FROM
    (SELECT tablespace_name, SUM(bytes) free FROM dba_free_space GROUP BY tablespace_name) a ,
    (SELECT tablespace_name, SUM(bytes) total FROM dba_data_files GROUP BY tablespace_name) b
WHERE a.tablespace_name = b.tablespace_name;


-- 表空间数据文件操作
方法1：给表空间增加数据文件
ALTER TABLESPACE app_data ADD DATAFILE 'D:\ORACLE\PRODUCT\10.2.0\ORADATA\EDWTEST\APP03.DBF' SIZE 50M;
 
方法2：新增数据文件，并且允许数据文件自动增长
alter tablespace app_data
add datafile 'D:\ORACLE\PRODUCT\10.2.0\ORADATA\EDWTEST\APP03.DBF'
size 1g
autoextend on
next 300m maxsize unlimited ;
 
方法3：允许已存在的数据文件自动增长
ALTER DATABASE DATAFILE 'D:\ORACLE\PRODUCT\10.2.0\ORADATA\EDWTEST\APP03.DBF' AUTOEXTEND ON NEXT 5M MAXSIZE 100M;
 
方法4：手工改变已存在数据文件的大小
--查询最大占用空间
select tablespace_name, file_id, file_name, 
round(bytes/(1024*1024),0) total_space --表空间最大值
,(SELECT MAX(block_id)*8/1024 FROM dba_extents WHERE tablespace_name = 'GBLZ_CW') max_space --已使用表空间的最大值
from dba_data_files f
order by tablespace_name;

ALTER DATABASE DATAFILE 'D:\ORACLE\PRODUCT\10.2.0\ORADATA\EDWTEST\APP02.DBF' RESIZE 100M;

```

#### 数据库表空间大小和数据文件位置查询
```sql
select tablespace_name,file_id,bytes/1024/1024/1024,file_name from dba_data_files order by file_id;
```

#### 查询当前用户有多少表
```sql
--查询当前用户有多张表
select count(*) from user_tables;

--查询当前用户表
select wm_concat(object_name) from user_objects where lower(object_type)='table';  

--查询对象最后修改时间
select object_name, created,last_ddl_time from user_objects
```
#### 查看连接用户
```sql
select count(*) from v$process; --当前的数据库连接数  
  
select value from v$parameter where name ='processes'; --数据库允许的最大连接数  
  
alter system set processes = 150 scope = spfile; --修改最大连接数:  
  
shutdown immediate;  
startup;--重启数据库  
  
SELECT osuser, a.username,cpu_time/executions/1000000||'s', b.sql_text,machine from v$session a, v$sqlarea b where a.sql_address =b.address;  --查看当前有哪些用户正在使用数据  
  
select count(*) from v$session; --当前的session连接数  

select t.username,count(*) from v$session t group by t.username ; --当前用户占用的session连接数  
  
select count(*) from v$session where status='ACTIVE'; --并发连接数  
  
show parameter processes; --最大连接 
```
#### 清空表数据
```sql
truncate table table_name; --速度快，原理是删除表后重建表，正在使用的时候可能没办法删除成功。
delete from table_name;  --普通删除数据表操作
```

#### 禁用Oracle约束条件和触发器
```sql
alter table table_name enable constraint constraint_name;  --启用外键约束
alter table table_name disable constraint constraint_name;  --禁用外键约束
select 'alter table '||table_name||' enable constraint '||constraint_name||';' from user_constraints where constraint_type='R';
select 'alter table '||table_name||' disable constraint '||constraint_name||';' from user_constraints where constraint_type='R';
```

#### 查询连接的用户并杀死连接
```sql
--查看链接的用户session
select username,sid,serial# from v$session;
--杀死链接的用户
alter system kill session '18,5';
--批量杀死连接的用户
SELECT 'alter system kill session '||''''||sid||','||serial#||''''||';' username,sid,serial# from v$session t WHERE t.username = 'ZZKF0415' ;
```

#### 删除用户操作
```sql
以system用户登录，查找需要删除的用户：

--查找用户
select  *　from dba_users;

--查找工作空间的路径
select * from dba_data_files; 

--删除用户
drop user 用户名称 cascade;
--删除表空间
drop tablespace 表空间名称 including contents and datafiles cascade constraint;

例如：删除用户名成为LYK，表空间名称为LYK

--删除用户，及级联关系也删除掉
drop user LYK cascade;
--删除表空间，及对应的表空间文件也删除掉
drop tablespace LYK including contents and datafiles cascade constraint;
```

#### 创建db_link
```sql
两种创建db_link的sql
create database link HCQS
   connect to name identified by HCQS  using '
       (DESCRIPTION = 
           (ADDRESS_LIST = 
               (ADDRESS = 
                  (PROTOCOL = TCP)
                  (HOST = 10.96.2.32)
                  (PORT = 1521)) ) 
               (CONNECT_DATA = 
                   (SERVICE_NAME = oraqsdb)oraqsdb
          ) )';

create database link HCQS
  connect to HCQS identified by HCQS
  using '10.96.2.32:1521/oraqsdb';
```

#### 创建DBMS_JOB

```sql
--无参
declare
  jobno number;
begin
  dbms_job.submit(jobno,
                  'begin p_stat_query_count; end;',
                  sysdate,
                  'TRUNC(SYSDATE + 1) + (0*60+10)/(24*60)');
  commit;
end;

--有参
declare
    jobno number;
begin
    dbms_job.submit(jobno,
                    'DECLARE
    r_codes varchar2(4000);
    r_msg varchar2(4000);
    r_data varchar2(4000);
    r_cur_data sys_refcursor;
BEGIN
    p_train_day_create(r_codes,r_msg,r_data,r_cur_data,''11111'','''');
    commit;
EXCEPTION
    WHEN OTHERS THEN
        RAISE_APPLICATION_ERROR(-20101,SQLERRM);
END;',
                    sysdate,
                    'sysdate+5/(24*60)');
    commit;
end;

```

## 存储过程执行

```sql
--方法一
DECLARE
    r_codes varchar2(4000);
    r_msg varchar2(4000);
    r_data varchar2(4000);
BEGIN
    p_video_recg_pro_viola_count(r_codes,r_msg,r_data,'11111','2021-11-18');
EXCEPTION
    WHEN OTHERS THEN
        RAISE_APPLICATION_ERROR(-20101,SQLERRM);
END;
/

--方法二
--sql命令行执行
var r_codes varchar2;
var r_msg varchar2;
var r_data varchar2;
call p_video_recg_pro_viola_count(:r_codes,:r_msg,:r_data,'11111','2021-11-18');

--call可使用exec代替
```

#### oracle查询数据总量

```sql
select t.table_name,t.num_rows from user_tables t ORDER BY NUM_ROWS DESC; -- 查询当前用户的数据总量
select t.table_name,t.num_rows from user_tables@dblink t ORDER BY NUM_ROWS DESC; --查询dblink的数据总量
exec dbms_stats.gather_schema_stats('用户名'); -- 用户下所有表生成统计数据
exec dbms_stats.gather_table_stats(user,'表名'); -- 生成指定表统计数据
```

#### 查询表创建时间

```sql
SELECT t.object_name   as 表名,
       t.created       as 创建时间,
       t.last_ddl_time as DDL操作时间
FROM user_objects t
where t.object_type = 'TABLE'
```

#### 删除数据回滚

1、需要看 undo_retention 的设置，默认为 900s，也就是 15 分钟。

2、查看 UNDO 中 5 分钟前数据是否还在

```sql
select * from 用户.表 as of timestamp to_timestamp('2021-09-12 10:30:00', 'yyyy-mm-dd hh24:mi:ss');
```

3、防止 UNDO 中数据被覆盖，先创建一张备份表将5分钟前数据备份

```sql
create table 用户.表_20201217
as
select * from 用户.表 as of timestamp to_timestamp('2021-09-12 10:30:00', 'yyyy-mm-dd hh24:mi:ss');
```

4、用备份表和原表比对数据，将误删的数据插入原表中

> 注意：此方法仅适用于 delete 等 DML 误删误操作恢复，DDL 不支持！

#### 死锁查询

```n sql
--共享锁：Share；排他锁：Exclusive；行共享锁：Row-S；行排他锁：Row-X
select   V$SESSION.sid,v$session.SERIAL#,v$process.spid,   
  rtrim(object_type)   object_type,rtrim(owner)   ||   '.'   ||   object_name   object_name,   
  decode(lmode,       0,   'None',   
  1,   'Null',   
  2,   'Row-S',   
  3,   'Row-X',   
  4,   'Share',   
  5,   'S/Row-X',   
  6,   'Exclusive', 'Unknown')   LockMode,   
  decode(request,   0,   'None',   
  1,   'Null',   
  2,   'Row-S',   
  3,   'Row-X',   
  4,   'Share',   
  5,   'S/Row-X',   
  6,   'Exclusive',   'Unknown')   RequestMode   
  ,ctime,   block   b,   
  v$session.username,MACHINE,MODULE,ACTION,   
  decode(A.type,   
  'MR',   'Media   Recovery',   
  'RT','Redo   Thread',   
  'UN','User   Name',   
  'TX',   'Transaction',   
  'TM',   'DML',   
  'UL',   'PL/SQL   User   Lock',   
  'DX',   'Distributed   Xaction',   
  'CF',   'Control   File',   
  'IS',   'Instance   State',   
  'FS',   'File   Set',   
  'IR',   'Instance   Recovery',   
  'ST',   'Disk   Space   Transaction',   
  'TS',   'Temp   Segment',   
  'IV',   'Library   Cache   Invalida-tion',   
  'LS',   'Log   Start   or   Switch',   
  'RW',   'Row   Wait',   
  'SQ',   'Sequence   Number',   
  'TE',   'Extend   Table',   
  'TT',   'Temp   Table',   
  'Unknown')   LockType   
  from   (SELECT   *   FROM   V$LOCK)   A,   all_objects, V$SESSION, v$process   
  where   A.sid   >   6   
  and   object_name<>'OBJ$'   
  and   A.id1   =   all_objects.object_id   
  and   A.sid=v$session.sid   
  and   v$process.addr=v$session.paddr; 

--查看锁表进程SQL语句1： 
select sess.sid, sess.serial#, lo.oracle_username, lo.os_user_name, ao.object_name, lo.locked_mode 
from v$locked_object lo, dba_objects ao, v$session sess 
where ao.object_id = lo.object_id and lo.session_id = sess.sid; 

--查看锁表进程的具体sql
select l.session_id sid,
       s.serial#,
       l.locked_mode,
       l.oracle_username,
       s.user#,
       l.os_user_name,
       s.machine,
       s.terminal,
       a.sql_text,
       a.action
  from v$sqlarea a, v$session s, v$locked_object l
 where l.session_id = s.sid
   and s.prev_sql_addr = a.address
 order by sid, s.serial#;


--杀掉锁表进程： 
--如有記錄則表示有lock，記錄下SID, serial# ，將記錄的ID替換下面的1143,24155，即可解除LOCK 
alter system kill session '1143,24155'; 
SELECT Saddr, Sid, Serial#, Paddr, Username, Status FROM V$session t WHERE t.SID = '1143';

--查看机器锁定代码对象
select b.sid,b.serial#,b.machine,b.terminal,b.program,b.process,b.status from v$lock a , v$session b
where a.SID = b.SID and b.MACHINE='guoshaoyue-5040';

--用这个可以查（可以查看哪台机器哪个用户锁了记录, 其中command是用来杀掉锁住记录的session ）：
select s.sid,s.machine,o.object_name,l.oracle_username,l.locked_mode, 'ALTER  SYSTEM  KILL  SESSION  '''||s.sid||',  '||s.serial#||''';' Command
from  v$locked_object  l,v$session  s,all_objects  o  where  l.session_id=s.sid  and  l.object_id=o.object_id; 

--同样也是通过写sql从数据字典里查出来。 
-- SELECT-SQL1 功能:检查被加锁的对象 
select obj.OWNER||'.'||obj.OBJECT_NAME as OBJ_NAME, --// 对象名称(已经被锁住) 
   obj.SUBOBJECT_NAME as SUBOBJ_NAME,     --// 子对象名称(已经被锁住) 
   obj.OBJECT_ID as OBJ_ID,        --// 对象ID 
   obj.OBJECT_TYPE as OBJ_TYPE,       --// 对象类型 
   lock_obj.SESSION_ID as SESSION_ID,     --// 会话SESSION_ID 
   lock_obj.ORACLE_USERNAME as ORA_USERNAME,  --// ORACLE系统用户名称 
   lock_obj.OS_USER_NAME as OS_USERNAME,    --// 操作系统用户名称 
   lock_obj.PROCESS as PROCESS       --// 进程编号 
from 
  ( select * from all_objects where object_id in (select object_id from v$locked_object)) obj, 
  v$locked_object lock_obj 
where obj.object_id=lock_obj.object_id; 

-- SELECT-SQL2 功能:检查被加锁的对象以及加锁的会话信息 
-- ALTER-SQL1 功能:杀死会话(SESSION_ID,SERIAL#),可以手工解除锁 
-- 请手工修改SESSION_ID,SERIAL#为相应值 
-- 注意:本功能慎重使用,有一定的破坏性,该SQL可以断开客户机和服务器的连接 
-- ALTER SYSTEM KILL SESSION 'SESSION_ID,SERIAL#';
select LOCK_INFO.OWNER||'.'||LOCK_INFO.OBJ_NAME as OBJ_NAME, --// 对象名称(已经被锁住) 
   LOCK_INFO.SUBOBJ_NAME as SUBOBJ_NAME,       --// 子对象名称(已经被锁住) 
   SESS_INFO.MACHINE as MACHINE,         --// 机器名称 
   LOCK_INFO.SESSION_ID as SESSION_ID,       --// 会话SESSION_ID 
   SESS_INFO.SERIAL# as SERIAL#,         --// 会话SERIAL# 
   LOCK_INFO.ORA_USERNAME as ORA_USERNAME,      --// ORACLE系统用户名称 
   LOCK_INFO.OS_USERNAME as OS_USERNAME,       --// 操作系统用户名称 
   LOCK_INFO.PROCESS as PROCESS,         --// 进程编号 
   LOCK_INFO.OBJ_ID as OBJ_ID,          --// 对象ID 
   LOCK_INFO.OBJ_TYPE as OBJ_TYPE,         --// 对象类型 
   SESS_INFO.LOGON_TIME as LOGON_TIME,       --// 登录时间 
   SESS_INFO.PROGRAM  as PROGRAM,         --// 程序名称 
   SESS_INFO.STATUS as STATUS,          --// 会话状态 
   SESS_INFO.LOCKWAIT as LOCKWAIT,         --// 等待锁 
   SESS_INFO.ACTION as ACTION,          --// 动作 
   SESS_INFO.CLIENT_INFO as CLIENT_INFO       --// 客户信息 
 
from 
 ( 
  select obj.OWNER as OWNER, 
     obj.OBJECT_NAME as OBJ_NAME, 
     obj.SUBOBJECT_NAME as SUBOBJ_NAME, 
     obj.OBJECT_ID as OBJ_ID, 
     obj.OBJECT_TYPE as OBJ_TYPE, 
     lock_obj.SESSION_ID as SESSION_ID, 
     lock_obj.ORACLE_USERNAME as ORA_USERNAME, 
     lock_obj.OS_USER_NAME as OS_USERNAME, 
     lock_obj.PROCESS as PROCESS 
  from 
   ( select * from all_objects where object_id in (select object_id from v$locked_object)) obj, 
   v$locked_object lock_obj 
  where obj.object_id=lock_obj.object_id 
 )  LOCK_INFO, 
 ( 
  select SID, 
     SERIAL#, 
     LOCKWAIT, 
     STATUS, 
     PROGRAM, 
     ACTION, 
     CLIENT_INFO, 
     LOGON_TIME, 
     MACHINE 
  from v$session 
 ) SESS_INFO 
where LOCK_INFO.SESSION_ID=SESS_INFO.SID ;
```
