### Oracle 常用操作
#### 创建表空间及用户
```
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
工具--导入表

![导入表结构](https://raw.githubusercontent.com/zhaojunliing/markDownDoc/master/youDaoYun/Oracle/2018-07-17_132220.png)

#### 导入用户表数据
```
-- 只导入表数据
imp YYY/YYY@localhost:1521/orcl file=e:/dbbak/file_name_YYYYMMdd00.dmp fromuser='XXX' touser='YYY' data_only=y

-- 导入指定表数据
imp YYY/YYY@localhost:1521/orcl file=e:/dbbak/file_name_YYYYMMdd00.dmp data_only=y tables=(table_name1,table_name2)
```

#### 数据库表空间删除
```
drop tablespace ZDCLBX including contents and datafiles;
```

#### 数据库表空间大小和数据文件位置查询
```
select tablespace_name,file_id,bytes/1024/1024/1024,file_name from dba_data_files order by file_id;
```

#### 查询当前用户有多少表
```
select count(*) from user_tables；

select wm_concat(object_name) from user_objects where lower(object_type)='table';  --查询当前用户有多上张表
```
#### 查看连接用户
```
select count(*) from v$process; --当前的数据库连接数  
  
select value from v$parameter where name ='processes'; --数据库允许的最大连接数  
  
alter system set processes = 150 scope = spfile; --修改最大连接数:  
  
shutdown immediate;  
startup;--重启数据库  
  
SELECT osuser, a.username,cpu_time/executions/1000000||'s', b.sql_text,machine from v$session a, v$sqlarea b where a.sql_address =b.address;  --查看当前有哪些用户正在使用数据  
  
select count(*) from v$session; --当前的session连接数  
  
select count(*) from v$session where status='ACTIVE'; --并发连接数  
  
show parameter processes; --最大连接 
```
