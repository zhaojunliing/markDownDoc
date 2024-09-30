环境：Linux redhat6.3 下安装的oracle11g

oracle 11g推出了审计功能，但这个功能会针对很多操作都产生审计文件.aud，日积月累下来这些文件也很多，
默认情况下，系统为了节省资源，减少I/0操作，其审计功能是关闭的



# 一、审计功能关闭

## 1、查看审计功能是否开启？

```sql
SQL> show parameter audit_trail

NAME                 TYPE    VALUE
------------------------------------
audit_trail          string   DB
```


说明：VALUE值为DB，表面审计功能为开启的状态

## 2、关闭oracle的审计功能
```sql
SQL> alter system set audit_trail=FALSE scope=spfile;
System altered. 
```

## 3、重启数据库
```sql
SQL> shutdown immediate;
SQL> startup;
```

## 4、验证审计是否已经被关闭
```sql
SQL> show parameter audit_trail

NAME                 TYPE    VALUE
------------------------------------
audit_trail          string   FALSE
```

说明：VALUE值为FALSE，表面审计功能为关闭的状态

# 二、删除审计日志

当已形成很对日志时，可删除里面的记录，目前是直接删除，未对数据库造成影响。

查询目前的日志信息：select * FROM SYS.AUD$;

删除已有的审计信息：DELETE FROM SYS.AUD$;

或者快速删除表信息：truncate table SYS.AUD$;