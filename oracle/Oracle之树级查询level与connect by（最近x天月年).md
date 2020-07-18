# Oracle之树级查询level与connect by（最近x天/月/年）

## 一、level

level关键字，代表树形结构中的**层级编号**；**第一层是数字1**，第二层数字2，**依次递增**

## 二、connect by

### 1.语法

```sql
    select * from table [start with condition1]
        connect by [prior] id=parentid
```

一般用来查找存在父子关系的数据，也就是树形结构的数据；其返还的数据也能够明确的区分出每一层的数据，start with condition1 是用来限制第一层的数据，或者叫根节点数据；以这部分数据为基础来查找第二层数据，然后以第二层数据查找第三层数据以此类推。

connect by [prior] id=parentid 这部分是用来指明Oracle在查找数据时以怎样的一种关系去查找；比如说查找第二层的数据时用第一层数据的id去跟表里面记录的parentid字段进行匹配，如果这个条件成立那么查找出来的数据就是第二层数据，同理查找第三层第四层…等等都是按这样去匹配。

### 2.实例

```sql
    --从根节点查找叶子节点
    select t.*, level, CONNECT_BY_ROOT(id)
        from tab_test t
         start with t.id = 0
          connect by prior t.id = t.fid;
```

## 三、常用实例

### 1.使用level实现1到10的序列

```sql
select level from dual 
        connect by level<=10;
```

### 2.最近7天每天日期

```sql
select to_char(sysdate-level+1,'yyyy-mm-dd') d
     from dual
         connect by level<=7 
            order by d
```

### 3.最近12个月份

```sql
select  to_char(ADD_MONTHs(sysdate,-level+1),'yyyy-mm') d
     from dual 
        connect by level<=12 
            order by d
```

### 4.查询某个时间段所有日期

```sql
SELECT distinct
  to_char(to_date('2018-11-13','YYYY-MM-DD')+level-1,'yyyy-mm-dd') as d
FROM 
  dual 
CONNECT BY 
  level <= to_date('2018-12-25','YYYY-MM-DD')-to_date('2018-11-13','YYYY-MM-DD')+1
order by d;
```

### 5.最近一个月每天用量统计(此处t2.date为varchar类型）

WITH AS短语，也叫做子查询部分（subquery factoring），可以定义一个SQL片断，该SQL片断会被整个SQL语句用到

```sql
with t1 as (select to_char(sysdate-level+1,'yyyy-mm-dd') d from dual connect by level<=30 order by d )
  select t1.d time, NVL(sum(t2.VAL),0) value  
    from t1 left join t2
      on t1.d=t2.date
        group by t1.d order by t1.d 
```

### 6.最近12月每个月用量统计 (此处t2.date为varchar类型）

```sql
with t1 as (select  to_char(ADD_MONTHs(sysdate,-level+1),'yyyy-mm') d from dual connect by level<=12 order by d )
        select t1.d time, NVL(sum(t2.VAL),0) value  
                from t1 left join  t2
                        on t1.d=substr(t2.date,0,7) 
                                group by t1.d  order by t1.d 
```

