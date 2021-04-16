# Oracle 查询执行过的sql

```sql
---正在执行的sql
select a.username, a.sid,b.SQL_TEXT, b.SQL_FULLTEXT
  from vsession a, vsessiona,vsqlarea b 
where a.sql_address = b.address

---执行过的sql
select b.SQL_TEXT,b.SQL_FULLTEXT,b.FIRST_LOAD_TIME,
  from v$sqlarea b
where b.FIRST_LOAD_TIME between '2009-10-15/09:24:47' and
       '2009-10-15/09:24:47' order by b.FIRST_LOAD_TIME;
       
查找前十条性能差的sql. 
SELECT * FROM (select PARSING_USER_ID,EXECUTIONS,SORTS, 
COMMAND_TYPE,DISK_READS,sql_text FROM v$sqlarea 
order BY disk_reads DESC )where ROWNUM<10 ;

查询当前数据库连接数，进程数，启动数据库

查询数据库当前进程的连接数：

　　select count(*) from v$process;

　　查看数据库当前会话的连接数：

　　elect count(*) from v$session;

　　查看数据库的并发连接数：

　　select count(*) from v$session where status='ACTIVE';

　　查看当前数据库建立的会话情况：

　　select sid,serial#,username,program,machine,status from v$session;

　 查询数据库允许的最大连接数：

　　select value from v$parameter where name = 'processes';

或者：show parameter processes;

查询所有数据库的连接数

selectschemaname,count(*)fromv$sessiongroupbyschemaname;

查询终端用户使用数据库的连接情况。

selectosuser,schemaname,count(*)fromv$sessiongroupbyschemaname,osuser;

#查看当前不为空的连接
select * from v$session where username is not null

#查看不同用户的连接数

select username,count(username) from v$session where username is not null group by username

#连接数

select count(*) from v$session

#并发连接数

Select count(*) from v$session where status='ACTIVE'　

#最大连接

show parameter processes

#修改连接

alter system set processes = value scope = spfile

　　修改数据库允许的最大连接数：

　　alter system set processes = 300 scope = spfile;

　　(需要重启数据库才能实现连接数的修改)

重启数据库：

　　shutdown immediate;

　　startup;

　　查看当前有哪些用户正在使用数据：

　　select osuser,a.username,cpu_time/executions/1000000||'s',sql_fulltext,machine

　　from v$session a,v$sqlarea b

　　where a.sql_address = b.address

　　order by cpu_time/executions desc;

备注：UNIX 1个用户session对应一个操作系统process，而Windows体现在线程。

oracle中查询被锁的表并释放session

SELECT A.OWNER,A.OBJECT_NAME,B.XIDUSN,B.XIDSLOT,B.XIDSQN,B.SESSION_ID,B.ORACLE_USERNAME, B.OS_USER_NAME,B.PROCESS, B.LOCKED_MODE, C.MACHINE,C.STATUS,C.SERVER,C.SID,C.SERIAL#,C.PROGRAM

FROM ALL_OBJECTS A,V$LOCKED_OBJECT B,SYS.GV_$SESSION C

WHERE ( A.OBJECT_ID = B.OBJECT_ID ) AND (B.PROCESS = C.PROCESS ) ORDER BY 1,2

释放session Sql:

alter system kill session 'sid, serial#'

alter system kill session '379, 21132'

alter system kill session '374, 6938'

查看占用系统io较大的session

SELECT se.sid,se.serial#,pr.SPID,se.username,se.status,se.terminal,se.program,se.MODULE,se.sql_address,st.event,st.p1text,si.physical_reads,si.block_changes

FROM v$session se,　v$session_wait st,v$sess_io si,v$process pr

WHERE st.sid=se.sid　AND st.sid=si.sid AND se.PADDR=pr.ADDR AND se.sid>6　AND st.wait_time=0 AND st.event NOT LIKE '%SQL%' ORDER BY physical_reads DESC

找出耗cpu较多的session

select a.sid,spid,status,substr(a.program,1,40) prog,a.terminal,osuser,value/60/100 value

from v$session a,v$process b,v$sesstat c

where c.statistic#=12 and c.sid=a.sid and a.paddr=b.addr order by value desc

查询session被锁的sql可以用一下语句

select sys.v_$session.osuser,sys.v_$session.machine,v$lock.sid,

　　sys.v_$session.serial#,

　　decode(v$lock.type,

　　'MR', 'Media Recovery',

　　'RT','Redo Thread',

　　'UN','User Name',

　　'TX', 'Transaction',

　　'TM', 'DML',

　　'UL', 'PL/SQL User Lock',

　　'DX', 'Distributed Xaction',

　　'CF', 'Control File',

　　'IS', 'Instance State',

　　'FS', 'File Set',

　　'IR', 'Instance Recovery',

　　'ST', 'Disk Space Transaction',

　　'TS', 'Temp Segment',

　　'IV', 'Library Cache Invalida-tion',

　　'LS', 'Log Start or Switch',

　　'RW', 'Row Wait',

　　'SQ', 'Sequence Number',

　　'TE', 'Extend Table',

　　'TT', 'Temp Table',

　　'Unknown') LockType,

　　rtrim(object_type) || ' ' || rtrim(owner) || '.' || object_name object_name,

　　decode(lmode, 0, 'None',

　　1, 'Null',

　　2, 'Row-S',

　　3, 'Row-X',

　　4, 'Share',

　　5, 'S/Row-X',

　　6, 'Exclusive', 'Unknown') LockMode,

　　decode(request, 0, 'None',

　　1, 'Null',

　　2, 'Row-S',

　　3, 'Row-X',

　　4, 'Share',

　　5, 'S/Row-X',

　　6, 'Exclusive', 'Unknown') RequestMode,

　　ctime, block b

　　from v$lock, all_objects, sys.v_$session

　　where v$Lock.sid > 6

　　and sys.v_$session.sid = v$lock.sid

　　and v$lock.id1 = all_objects.object_id;

　 　启动oracle

　 　su - oracle

　　 sqlplus system/pwd as sysdba //进入sql

　　 startup //启动数据库

　　 lsnrctl start //启动监听

　　 sqlplus "/as sysdba"

　　 shutdown immediate;

　　 startup mount;

　　 alter database open;
```

