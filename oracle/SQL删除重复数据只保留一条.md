# SQL删除重复数据只保留一条

在几千条记录里,存在着些相同的记录,如何能用SQL语句,删除掉重复的呢
### 1、查找表中多余的重复记录，重复记录是根据单个字段（peopleId）来判断 

```sql
select * from people 
	where t_train_plan_total in (select peopleId from people group by peopleId having count(peopleId) > 1) 
```

### 2、删除表中多余的重复记录，重复记录是根据单个字段（peopleId）来判断，只留有rowid最小的记录 

```sql
delete from people
	where peopleId in (select peopleId from people group by peopleId having count(peopleId) > 1) 
	and rowid not in (select min(rowid) from vitae group by peopleId having count(*)>1)
```

### 3、查找表中多余的重复记录（多个字段） 

```sql
select * from vitae a 
	where (a.peopleId,a.seq) in (select peopleId,seq from vitae group by peopleId,seq having count(*) > 1) 
```

### 4、删除表中多余的重复记录（多个字段），只留有rowid最小的记录 

```sql
delete from vitae a 
	where (a.peopleId,a.seq) in (select peopleId,seq from vitae group by peopleId,seq having count(*) > 1) 
	and rowid not in (select min(rowid) from vitae group by peopleId,seq having count(*)>1) 
```

### 5、查找表中多余的重复记录（多个字段），不包含rowid最小的记录

``` sql
select * from vitae a 
	where (a.peopleId,a.seq) in (select peopleId,seq from vitae group by peopleId,seq having count(*) > 1) 
	and rowid not in (select min(rowid) from vitae group by peopleId,seq having count(*)>1)
```

```sq
select *
from (select t.*, row_number() over (partition by traincode order by rownum) rn
      from t_plan_train t
      where t.is_pass = '1'
        and t.excel_type = '12'
        and t.id_plan_cycle = 'fad585d652f04458befd996a5a069411')
where rn = 1
```



### 6.消除一个字段的左边的第一位：

```sql
update tableName set [Title]=Right([Title],(len([Title])-1)) where Title like '村%'
````

### 7.消除一个字段的右边的第一位：

```sql
update tableName set [Title]=left([Title],(len([Title])-1)) where Title like '%村'
```

### 8.假删除表中多余的重复记录（多个字段），不包含rowid最小的记录

```sql
update vitae set ispass=-1
where peopleId in (select peopleId from vitae group by peopleId
```

备注摘自：https://blog.csdn.net/anya/article/details/6407280/