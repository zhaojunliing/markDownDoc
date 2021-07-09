# ORACLE11g 重装系统后根据dbf恢复数据库

### 1、环境恢复

安装一个和原系统一致的oracle 环境，主要包括版本、数据名sid、实例名、路径和数据库编码一致

注意预留磁盘分区需要够，虚拟机中安装的时候持批判性能要好。虚拟机多做快照，防止出错的时候进行回滚操作。

步骤2和步骤3未使用。

### 2、修改listener.ora的参数

```
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PLSExtProc)
      (ORACLE_HOME = D:\oracle\product\10.1.0\db_1)
      (PROGRAM = extproc)
    )
    (SID_DESC =
      (GLOBAL_DBNAME = orcl) //如果已经有这个orcl了，这个文件就不用修改
      (ORACLE_HOME = D:\oracle\product\10.1.0\db_1) //这里要根据你的实际路径来
      (SID_NAME = orcl)
    )
  )
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS_LIST =
        (ADDRESS = (PROTOCOL = TCP)(HOST = lypch )(PORT = 1521))
      )
      (ADDRESS_LIST =
        (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC))
      )
    )
  )
```

### 3、修改tnsnames.ora的参数

```
AMMICLY =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = lypch )(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
      (UR=A) //参数允许只进行管理性的连接，数据库nomount,mount或者restricted时，动态监听显示状态为BLOCKED时，客户端配置UR=A进行连接。
    )
  )
EXTPROC_CONNECTION_DATA =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC))
    )
    (CONNECT_DATA =
      (SID = PLSExtProc)
      (PRESENTATION = RO)
    )
  )
```

### 4、停止服务

```sql
SQL>shutdown immediate
```

### 5、复制dbf文件

将新安装的oracle的dbf备份后删除，将原dbf拷贝到新安装的目录下/u01/app/oradata/orcl

### 6、复制fast_recovery_area文件

原oracle库主目录下的fast_recovery_area目录全部覆盖到新安装的oracle对应目录下

### 7、启动

```shell
SQL>startup
```

