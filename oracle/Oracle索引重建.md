## 一、查询索引失效的sql

```sql
select t.*, i.index_type, i.tablespace_name, i.table_type, i.status
  from user_ind_columns t, user_indexes i
 where t.index_name = i.index_name
   and t.table_name = i.table_name
   and i.status != 'VALID'
  -- and t.table_name = 'T_S_TRAIN_LKJ'
```

## 二、针对非分区表

1、在 oracle中，重建普通表上的索引很简单。要重建特定索引，只需执行如下sql命令：

```sql
ALTER INDEX INDEX_NAME Rebuild;
```

2、如果重建某个表上的全部索引，执行如下 PL/SQL 代码:

```sql
begin
    for c1 in (select t.index_name, t.partitioned from user_indexes t where table_name = 'TABLE_NAME')
        loop
            if c1.partitioned = 'NO' then
                execute immediate 'ALTER INDEX ' || c1.index_name || ' REBUILD';
            end if;
        end loop;
end;
/
```

## 三、针对分区表

1、如果这个索引不是分区的，那么重建的方法 和 重建普通表上的索引 相同。

2、如果这个索引是分区的，重建方法是执行如下sql代码：

```sql
begin
    for c2 in (select index_name, partition_name
               from user_ind_partitions
               where index_name = 'INDEX_NAME' and status = 'UNUSABLE')
        loop
            execute immediate 'alter index ' || c2.index_name || ' rebuild partition ' || c2.partition_name;
        end loop;
end;
/
```

重建一张表上的所有分区索引的方法是执行如下sql代码：

```sql
begin
    for c1 in (select t.index_name, t.partitioned from user_indexes t where table_name = 'TABLE_NAME')
        loop
            if c1.partitioned = 'YES' then
                -- rebuild every unusable partition for partitioned index
                for c2 in (select partition_name
                           from user_ind_partitions
                           where index_name = c1.index_name
                             and status = 'UNUSABLE')
                    loop
                        execute immediate 'alter index ' || c1.index_name || ' rebuild partition ' || c2.partition_name;
                    end loop;
            end if;
        end loop;
end;
/
```

而重建这张表上的全部索引的sql 代码如下：

```sql
begin
    for c1 in (select t.index_name, t.partitioned from user_indexes t where table_name = 'TABLE_NAME')
        loop
            if c1.partitioned = 'NO' then
                -- rebuild global index directly
                execute immediate 'alter index ' || c1.index_name || ' rebuild';
            else
                -- rebuild every unusable partition for partitioned index
                for c2 in (select partition_name
                           from user_ind_partitions
                           where index_name = c1.index_name
                             and status = 'UNUSABLE')
                    loop
                        execute immediate 'alter index ' || c1.index_name || ' rebuild partition ' || c2.partition_name;
                    end loop;
            end if;
        end loop;
end ;
/
```

## 附：

> INDEX_NAME 代表索引的名字

> TABLE_NAME 代表索引的名字