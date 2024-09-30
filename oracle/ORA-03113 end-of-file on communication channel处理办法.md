**Oracle错误ORA-03113: end-of-file on communication channel处理办法**

机器遭遇断电，之后oracle就不能启动了，报错ORA-03113: end-of-file on communication channel （通信通道的文件结尾）

```shell
[oracle@lhrora11204 ~]$ sqlplus / as sysdba;

SQL*Plus: Release 11.2.0.4.0 Production on Mon Jun 3 15:53:04 2024

Copyright (c) 1982, 2013, Oracle.  All rights reserved.

Connected to an idle instance.

SYS@LHR11G> startup
ORACLE instance started.

Total System Global Area  325685248 bytes
Fixed Size		    2252944 bytes
Variable Size		  188747632 bytes
Database Buffers	  130023424 bytes
Redo Buffers		    4661248 bytes
Database mounted.
ORA-03113: end-of-file on communication channel
Process ID: 2367
Session ID: 96 Serial number: 3


SYS@LHR11G> exit


[oracle@lhrora11204 ~]$ sqlplus /nolog

SQL*Plus: Release 11.2.0.4.0 Production on Mon Jun 3 15:57:00 2024

Copyright (c) 1982, 2013, Oracle.  All rights reserved.


@> conn / as sysdba;
Connected to an idle instance.


SYS@LHR11G> startup mount;
ORACLE instance started.

Total System Global Area  325685248 bytes
Fixed Size		    2252944 bytes
Variable Size		  188747632 bytes
Database Buffers	  130023424 bytes
Redo Buffers		    4661248 bytes
Database mounted.


SYS@LHR11G> select * from v$log;

    GROUP#    THREAD#  SEQUENCE#      BYTES  BLOCKSIZE	  MEMBERS ARC STATUS	       FIRST_CHANGE# FIRST_TIME 	 NEXT_CHANGE# NEXT_TIME
---------- ---------- ---------- ---------- ---------- ---------- --- ---------------- ------------- ------------------- ------------ -------------------
	 1	    1	       1   52428800	   512		1 NO  INACTIVE		     5375889 2024-05-31 11:09:15      5428328 2024-05-31 15:40:16
	 3	    1	       3   52428800	   512		1 NO  CURRENT		     5480850 2024-05-31 20:44:49   2.8147E+14
	 2	    1	       2   52428800	   512		1 NO  INACTIVE		     5428328 2024-05-31 15:40:16      5480850 2024-05-31 20:44:49



SYS@LHR11G> alter database open resetlogs;
alter database open resetlogs
*
ERROR at line 1:
ORA-01139: RESETLOGS option only valid after an incomplete database recovery


SYS@LHR11G> recover database until time '2024-06-05';
Media recovery complete.



SYS@LHR11G> alter database open resetlogs;

Database altered.

```