# oracle里如何将两个日期的时间差返回**时**分的格式

```sql
SELECT 
   EXTRACT(DAY FROM (sysdate-to_date('2012-03-29 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' days '
   || EXTRACT(HOUR FROM (sysdate-to_date('2012-03-29 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' hours'
   || EXTRACT(MINUTE FROM (sysdate-to_date('2012-03-29 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' minute'
   || EXTRACT(SECOND FROM (sysdate-to_date('2012-03-29 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' second' "Interval"
FROM DUAL;
```

 

```sql
SELECT 
   EXTRACT(DAY FROM (sysdate-to_date('2013-09-01 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )*24+
   EXTRACT(HOUR FROM (sysdate-to_date('2013-09-01 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' hours'
   || EXTRACT(MINUTE FROM (sysdate-to_date('2013-09-01 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' minute'
   || EXTRACT(SECOND FROM (sysdate-to_date('2013-09-01 00:00:00','YYYY-MM-DD HH24:MI:ss')) DAY TO SECOND )
   || ' second' "Interval"
FROM DUAL;
```