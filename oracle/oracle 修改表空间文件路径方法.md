##  oracle 修改表空间文件路径方法

> 背景：数据库表空间文件创建到机械盘上了，需要迁移到固态盘上。
>

1、先登录sqlplus：

```
sqlplus  system/password as sysdba
```

2、修改表空间为Offline： 

 ```
alter tablespace users offline; 
 ```

3、拷贝表空间文件 

```
拷贝  C:\oracle\product\10.2.0\oradata\orcl\USERS01.DBF 到 D:\oracledata\orcl\USERS01.DBF
```

4、修改oracle表空间指向地址 

```
alter database rename file ‘原路径\USERS01.DBF' to '文件新路径\USERS01.DBF'; 
alter database rename file 'C:\oracle\product\10.2.0\oradata\orcl\USERS01.DBF' to 'D:\oracledata\orcl\USERS01.DBF'
```

5、手动删除表空间物理文件

```
 删除c:下的USERS01.DBF文件，并且以后数据全部会放在D:\oracledata
```


 6、修改表空间为Online 

```
  SQL> alter tablespace users online; 
```

