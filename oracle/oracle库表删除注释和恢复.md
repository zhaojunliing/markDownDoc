 # oracle库表删除注释和恢复的步骤教程

> 项目上线后，由于一些原因，我们要删除开发时中的注释字段。这里简单起见,只需要执行简单的两三句。因为oracle是有张表来管理注释。
>

 ## 一、先备份字段注释.以便以后dmp数据有注释

 plsql执行: 

```sql
select 'comment on column '||t.table_name||'.'||t.column_name||' is '''||t.comments||''';' from user_col_comments t; 
```

点获取所有行，复制所有查询出的行。

保存到新建的txt.可用于恢复。

## 二、删除所有注释

plsql执行: 

```sql
select 'comment on column '||t.table_name||'.'||t.column_name||' is '''';' from user_col_comments t; 
```

点获取所有行,复制所有查询出的行，执行复制出的所有语句。

测试在数据过大的情况plsql下容易编译卡死.在sqldeveloper无此情况。

可以使用sql命令行界面，复制进去进行提交操作。