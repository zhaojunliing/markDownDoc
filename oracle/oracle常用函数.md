# oracle常用函数
---

##一.单行函数

###1、字符函数

####（1）concat(str1,str2)字符串拼接函数
```sql
select concat('Hello ','World') from dual;
```
–等价于
```SQL
select 'Hello '||'World' from dual;
```

####（2）initcap(str)将每个单词首字母大写，其他字母小写

```sql
select initcap('hello world!') from dual; --返回结果为'Hello World!'
select initcap('HELLO WORLD!') from dual; --返回结果为'Hello World!'
```

####（3）instr(x,find_string[,start][,occurrence])返回指定字符串在某字符串中的位置，可以指定搜索的开始位置和返回第几次搜索出来的结果

———-搜索时下标从1开始计算
```sql
select instr('Hello World!','o') from dual;--从1位置开始搜索，返回第一次出现的o的位置，结果为5
select instr('Hello World!','o',6) from dual;--从6位置开始搜索，返回第一次出现的o的位置，结果为8
select instr('Hello World!','o',1,2) from dual;--从1位置开始搜索，返回第二次出现o的位置，结果为8
```
####（4）length(str)返回表达式中的字符数
```sql
select length('Hello World!') from dual;--返回结果为12
select length('张三') from dual;--返回结果为2
```

####（5）lengthb(str)返回表达式中的字节数
```sql
select lengthb('Hello World!') from dual;--返回结果为12
select lengthb('张三') from dual;--返回结果为6
```

####（6）lower(str)将字符串转换为小写
```sql
select lower('Hello World!') from dual;
```

####（7）upper(str)将字符串转换为大写
```sql
select upper('Hello World!') from dual;
```

####（8）lpad(str,width[,pad_string])当字符串长度不够时，左填充补齐，可以指定补齐时用什么字符补齐,若不指定，则以空格补齐
```sql
select lpad('Hello World!',20) from dual;--返回结果为'        Hello World!'
select lpad('Hello World!',20,'*') from dual;--返回结果为'********Hello World!'
```
####（9）rpad(str,width[,pad_string])当字符串长度不够时，右填充补齐，原理同左填充
```sql
select rpad('Hello World!',20) from dual;--返回结果为'Hello World!'
select rpad('Hello World!',20,'*+') from dual;--返回结果为'Hello World!*+*+*+*+'
```

####（10）ltrim(x[,trim_string])从字符串左侧去除指定的所有字符串，若没有指定去除的字符串，则默认去除左侧空白符
```sql
select ltrim('   Hello World!    ') from dual;--返回结果为'Hello World!    '
select ltrim('***+*Hello World!***+*','*+') from dual;--返回结果为'Hello World!***+*'
```

####（11）rtrim(x[,trim_string])从字符串右侧去除指定的所有字符串，原理同ltrim()
```sql
select rtrim('   Hello World!    ') from dual;--返回结果为'    Hello World!'
select rtrim('***+*Hello World!***+*','*+') from dual;--返回结果为'***+*Hello World!'
```

####（12）trim(trim_string from x)从字符串两侧去除指定的所有字符串
```sql
select trim('*+' from '***+*Hello World!***+*') from dual;
```
注意，ltrim()和rtrim()的截取集可以使多个字符，但trim的截取集只能有一个字符
```sql
select trim('*+' from '***+*Hello World!***+*') from dual;--错误，截取集只能有一个字符
```
####（13）nvl(x,value)将一个NULL转换为另外一个值，如果x为NULL,则返回value,否则返回x值本身
```sql
insert into student values(7,'猪猪',default,NULL);
select nvl(address,'北京市') from student;
```
####（14）nvl2(x,value1,value2),如果x不为NULL，返回value1，否则，返回value2
```sql
select nvl2(address,'有地址','无地址') from student;
```
####（15）replace(x,search_string,replace_string),从字符串x中搜索search_string字符串，并使用replace_string字符串替换。并不会修改数据库中原始值
```sql
select replace('Hello World!','o','HA') from dual;
```
####（16）substr(x,start[,length])返回字符串中的指定的字符，这些字符从字符串的第start个位置开始，长度为length个字符；如果start是负数，则从x字符串的末尾开始算起；如果　　　　　　 length省略，则将返回一直到字符串末尾的所有字符
```sql
select substr('Hello World',3) from dual; --返回结果为'llo World'
select substr('Hello World',-3) from dual;--返回结果为'rld'
select substr('Hello World',3,2) from dual;--返回结果为'll'
select substr('Hello World',-7,4) from dual;--返回结果为'o Wo'
```
###2、数值函数

####（1）abs(value)返回value的绝对值
```sql
select abs(-10) from dual;--返回结果为10
```
####（2）ceil(value)返回大于等于value的最小整数
```sql
select ceil(2.3) from dual; --返回结果为3
```

####（3）floor(value)返回小于等于value的最大整数
```sql
select floor(2.3) from dual; --返回结果为2
```

####（4）trunc(value,n)对value进行截断，如果n>0，保留n位小数；n<0，则保留-n位整数位；n=0，则去掉小数部分
```sql
select trunc(555.666) from dual; --返回结果为555，不加n时默认去掉小数部分
select trunc(555.666,2) from dual;--返回结果为555.66
select trunc(555.666,-2) from dual;--返回结果为500
```
####（5）round(value,n)对value进行四舍五入，保存小数点右侧的n位。如果n省略的话，相当于n=0的情况
```sql
select round(555.666) from dual;--返回结果为556，不加n时默认去掉小数部分
select round(555.666,2) from dual;--返回结果为555.67
select round(555.666,-2) from dual;--返回结果为600
```
注意:1.trunc和round用法类似，只不过trunc是硬生生截取，并不进行四舍五入，而round进行截取时四舍五入
　　　　　　 2.都还可以对日期的截取，可以参考写的日期函数笔记
```sql
select round(sysdate,'year') from dual;
select trunc(sysdate,'year') from dual;
```

###3、转换函数

>将值从一种类型转换成另外一种类型，或者从一种格式转换为另外一种格式

####（1）to_char(x[,format]):将x转化为字符串。 format为转换的格式，可以为数字格式或日期格式
```sql
select to_char('12345.67') from dual; --返回结果为12345.67
select to_char('12345.67','99,999.99') from dual; --返回结果为12,345.67
```
####（2）to_number(x [, format]):将x转换为数字。可以指定format格式
```sql
select to_number('970.13') + 25.5 from dual;
select to_number('-$12,345.67', '$99,999.99') from dual;
```
####（3）cast(x as type):将x转换为指定的兼容的数据库类型
```sql
select cast(12345.67 as varchar2(10)),cast('05-7月-07' as date), cast(12345.678 as number(10,2)) from dual;
```
####（4）to_date(x [,format]):将x字符串转换为日期

```sql
select to_date('2012-3-15','YYYY-MM-DD') from dual
```

##二、聚集函数

###1、常用函数

####（1）avg(x)：返回x的平均值
```sql
select avg(grade) from sc;
```
####（2）count(x)：返回统计的行数
```sql
select count(name) from sc;
```
####（3）max(x)：返回x的最大值
```sql
select max(grade) from sc;
```
####（4）min(x)：返回x的最小值
```sql
select min(grade) from sc;
```
####（5）sum(x)：返回x的总计值
```sql
select sum(grade) from sc;
```

###2、对分组行使用聚集函数

>对分组后的行使用聚集函数，聚集函数会统计每组中的值，对于每组分别统计后返回一个值。

示例
```sql
--按照职位分组，求出每个职位的最高和最低工资
select job ,max(sal),min(sal) from emp 
group by job 
order by job;
```
注意：
1.分组时select子句后边的列名必须与group by子句后的列名一致，除非是聚合函数

```sql
select deptno,avg(sal) from EMP; --错误，因为deptno不是聚集函数，也不是group by后面跟的列名
```

2.不能使用聚集函数作为WHERE子句的筛选条件

```sql
select deptno from emp where avg(sal)>1000;--错误
```

3.分组后，需要使用条件进行筛选，则使用having过滤分组后的行，不能使用where，where只能放在group by前面。

```sql
select deptno, avg(sal) from emp where deptno<>10 
	group by deptno  
having avg(sal) > 900;
```



