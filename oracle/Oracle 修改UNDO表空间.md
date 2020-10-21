1 确认当前UNDO表空间

```
SQL> show parameter undo
NAME                                 TYPE        VALUE
------------------------------------ ----------- -----------
undo_management                      string      AUTO
undo_retention                       integer     900
undo_tablespace                      string      UNDOTBS1
```

2 创建新UNDO表空间

```
create undo tablespace undotbs2
datafile '/home/oracle/oradata/undotbs2.dbf'
size 1G  --表空间文件初始大小
autoextend on  --表空间自动增长
next 1G maxsize unlimited  --自动增长文件大小及最大空间
extent management local; 
```

3 修改默认UNDO表空间

```
SQL> alter system set undo_tablespace = undotbs2;
System altered.
SQL> show parameter undo
NAME                                 TYPE        VALUE
------------------------------------ ----------- -----------
undo_management                      string      AUTO
undo_retention                       integer     900
undo_tablespace                      string      UNDOTBS2
```

4 删除原来的UNDO表空间

4.1 若成功删除

```
SQL> drop tablespace undotbs1 including contents and datafiles cascade constraints;
Tablespace dropped.
```

4.2 未成功删除

有时会出现ORA-30013错误, 检查是否还有在UNDOTBS1表空间上online状态的回滚段:

```
SQL> select segment_name,owner,tablespace_name,status from dba_rollback_segs where tablespace_name='UNDOTBS1' and status = 'ONLINE';

SEGMENT_NAME                   OWNER           TABLESPACE_NAME                STATUS
------------------------------ --------------- ------------------------------ ----------
_SYSSMU13$                     PUBLIC          UNDOTBS1                       ONLINE
_SYSSMU15$                     PUBLIC          UNDOTBS1                       ONLINE
```

 

这是因为原undo表空间还有活动事务, 还在迁移中. 观察alert.log有

```
Tue Jun 21 14:21:52 2011
Undo Tablespace 1 moved to Pending Switch-Out state.
```

等到alert.log出现如下提示后, 表示undo表空间迁移完毕:

```
Tue Jun 21 14:21:54 2011
Successfully onlined Undo Tablespace 5.
Undo Tablespace 1 successfully switched out.
```

此时再看是否还有在UNDOTBS1表空间上online状态的回滚段:

```
SQL> select segment_name,owner,tablespace_name,status from dba_rollback_segs where tablespace_name='UNDOTBS1' and status = 'ONLINE';

no rows selected
```

```
SQL> DROP TABLESPACE UNDOTBS1 INCLUDING CONTENTS AND DATAFILES CASCADE CONSTRAINTS;

Tablespace dropped.
```

