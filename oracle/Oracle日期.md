# Oracle 时间相减得出毫秒、秒、分、时、天

### 1、概念说明

oracle 两个时间相减默认的是天数

oracle 两个时间相减默认的是天数*24 为相差的小时数

oracle 两个时间相减默认的是天数*24*60 为相差的分钟数

oracle 两个时间相减默认的是天数*24*60*60 为相差的秒数

###  2、计算相差月份(MONTHS_BETWEEN)

--MONTHS_BETWEEN(date2,date1) 

给出date2-date1的月份 

```sql
> select months_between('19-12月-1999','19-3月-1999') mon_between from dual;

MON_BETWEEN 
----------- 
9 

>select months_between(to_date('2000.05.20','yyyy.mm.dd'),to_date('2005.05.20','yyyy.dd')) mon_betw from dual; 

MON_BETW 
--------- 
-60 
```

### 3、计算两个时间相差表达式

1. ##### **获取两时间的相差豪秒数** 
   
   ```sql
   >select ceil((To_date('2008-05-02 00:00:00' , 'yyyy-mm-dd hh24-mi-ss') - To_date('2008-04-30 23:59:59' , 'yyyy-mm-dd hh24-mi-ss')) * 24 * 60 * 60 * 1000) 相差豪秒数 FROM DUAL;
   
   相差豪秒数 
   ---------- 
   86401000 
   ```

2. ##### 获取两时间的相差秒数 

   ```sql
    >select ceil((To_date('2008-05-02 00:00:00' , 'yyyy-mm-dd hh24-mi-ss') - To_date('2008-04-30 23:59:59' , 'yyyy-mm-dd hh24-mi-ss')) * 24 * 60 * 60) 相差秒数 FROM DUAL; 
       
   相差秒数 
   ---------- 
   86401 
   ```

3. ##### 获取两时间的相差分钟数 

   ```sql
   >select ceil(((To_date('2008-05-02 00:00:00' , 'yyyy-mm-dd hh24-mi-ss') - To_date('2008-04-30 23:59:59' , 'yyyy-mm-dd hh24-mi-ss'))) * 24 * 60) 相差分钟数 FROM DUAL; 
   
   相差分钟数 
   ----------
   1441 
   
   ```

4. ##### 获取两时间的相差小时数 

   ```sql
   >select ceil((To_date('2008-05-02 00:00:00' , 'yyyy-mm-dd hh24-mi-ss') - To_date('2008-04-30 23:59:59' , 'yyyy-mm-dd hh24-mi-ss')) * 24) 相差小时数 FROM DUAL; 
    
   相差小时数 
   ---------- 
   25 
	```

5. ##### 获取两时间的相差天数 

   ```sql
   >select ceil((To_date('2008-05-02 00:00:00' , 'yyyy-mm-dd hh24-mi-ss') - To_date('2008-04-30 23:59:59' , 'yyyy-mm-dd hh24-mi-ss'))) 相差天数 FROM DUAL;
   
   相差天数 
   ---------- 
   2 
   ```
   
   注：天数可以2个日期直接减，这样更加方便 

6. ##### 获取两时间月份差 

   ```sql
   >select (EXTRACT(year FROM to_date('2009-05-01','yyyy-mm-dd')) - EXTRACT(year FROM to_date('2008-04-30','yyyy-mm-dd'))) * 12 + EXTRACT(month FROM to_date('2008-05-01','yyyy-mm-dd')) - EXTRACT(month FROM to_date('2008-04-30','yyyy-mm-dd')) months from dual;
   
   MONTHS 
   ---------- 
   13 
   ```
   
   
   注：可以使用months_between函数，更加方便 
   
7. ##### 获取两时间年份差 

   ```sql
   select EXTRACT(year FROM to_date('2009-05-01','yyyy-mm-dd')) - EXTRACT(year FROM to_date('2008-04-30','yyyy-mm-dd')) years from dual; 
   
   YEARS 
   ---------- 
   1 
   
   ```

### 4、原有日期增加操作

```sql
select sysdate,add_months(sysdate,12) from dual; --加1年 
select sysdate,add_months(sysdate,1) from dual;  --加1月 
select sysdate,TO_CHAR(sysdate+7,'yyyy-mm-dd HH24:MI:SS') from dual; --加1星期 
select sysdate,TO_CHAR(sysdate+1,'yyyy-mm-dd HH24:MI:SS') from dual; --加1天 
select sysdate,TO_CHAR(sysdate+1/24,'yyyy-mm-dd HH24:MI:SS') from dual; --加1小时 
select sysdate,TO_CHAR(sysdate+1/24/60,'yyyy-mm-dd HH23:MI:SS') from dual; --加1分钟 
select sysdate,TO_CHAR(sysdate+1/24/60/60,'yyyy-mm-dd HH23:MI:SS') from dual; --加1秒 
select  sysdate+7  from  dual;           --加7天
```
