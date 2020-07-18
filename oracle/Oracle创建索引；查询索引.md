## Oracle创建索引；查询索引

### 1、创建索引

```sql
create index 索引名 on 表名(列名);
```

### 2、删除索引
```sql
drop index 索引名;
```

### 3、创建组合索引
```sql
create index 索引名 on 表名(列名1,,列名2);
```
### 4、查看目标表中已添加的索引
```sql
--查看该表的所有索引
select * from all_indexes where table_name = 'tablename';

--查看该表的所有索引列
select* from all_ind_columns where table_name = 'tablename';
```