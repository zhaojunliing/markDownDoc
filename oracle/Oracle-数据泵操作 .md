# Oracle-数据泵expdp/impdp操作

>原用户：test01  					目标用户：test02 
>
>原表空间：test_space_01 	目标表空间：test_space_02

## 一、查看目录

```sql
SELECT privilege, directory_name, DIRECTORY_PATH
  FROM user_tab_privs t, all_directories d
 WHERE t.table_name(+) = d.directory_name
 ORDER BY 2, 1;
 
 select * from dba_directories ;
```

## 二、物理机创建目录

使用oracle用户，然后执行mkdir命令创建

```shell
# 创建目录
mkdir -p /u02/dir_dp

# 修改权限
chown oracle:oinstall /u02/dir_dp
```

## 三、创建逻辑目录

在PL/SQL中执行命令

```sql
create directory dir_dp as '/u02/dir_dp';
```

## 四、导出全库

```shell
expdp system/oracle directory=dir_dp dumpfile=expdp_db01_full.dmp logfile=expdp_db01_full.log full=y

impdp system/oracle directory=dir_dp dumpfile=expdp_db01_full.dmp logfile=expdp_db01_full.log full=y
```

## 五、导出指定表空间

```shell
expdp system/oracle directory=dir_dp dumpfile=expdp_db01_tspache.dmp logfile=expdp_db01_tspache.log tablespaces=test,test

impdp system/oracle directory=dir_dp dumpfile=expdp_db01_tspache.dmp logfile=impdp_db01_tspache.log tablespaces=test1,test2
```

## 六、导出导入指定用户数据


```shell
expdp wf056_zz/wf056_zz@192.168.20.113:1521/orcl schemas=wf056_zz directory=dir_dp dumpfile =wf056_zz.dmp logfile=wf056_zz.log;

impdp wf056_zz/wf056_zz@192.168.20.113:1521/orcl schemas=wf056_zz remap_schema=wf056_zz:wf056_zz directory=dir_dp dumpfile =wf056_zz.dmp logfile=impdp_wf056_zz.log;
```

## 七、导出指定表数据

```shell
1、导出指定表
expdp test01/test01 directory=dir_dp dumpfile=expdp_db01_tb.dmp logfile=expdp_db01_tb.log tables=test1,test2

2、导入指定表
impdp test02/test02 directory=dir_dp dumpfile=expdp_db01_tb.dmp logfile=impdp_db01_tb.log tables=test01.test1,test01.test2
```

## 八、按照查询条件导出

```shell
expdp system/oracle directory=dir_dp dumpfile=expdp_db01_q.dmp logfile=expdp_db01_q.log tables=test query='where id >5 and id <10'

impdp system/oracle directory=dir_dp dumpfile=expdp_db01_q.dmp logfile=impdp_db01_q.log tables=test1 query='where id >5 and id <10'
```

## 其他

```sql
--修改原始表空间
remap_tablespace=test_space_01:test_space_02
--修改原始用户
remap_schema=test01:test02
--表数据追加
table_exists_action=append;
--查看目录
select * from dba_directories;
--查看权限
select * from dba_sys_privs where grantee='SYSTEM';
--删除不需要的目录
drop directory dumpdir ;
--获取创建表空间语句
select dbms_metadata.get_ddl('TABLESPACE','TEST') from dual;
--查看jobs
select * from dba_datapump_jobs;
```

