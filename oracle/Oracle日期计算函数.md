# Oracle计算时间函数(numtodsinterval、numtoyminterval)

**numtodsinterval(<x>,<c>)** ,x是一个数字,c是一个字符串,
表明x的单位,这个函数把x转为interval day to second数据类型

常用的单位有 ('day','hour','minute','second')

example

SQL> select sysdate,sysdate+numtodsinterval(3,'hour') as res from dual;

SYSDATE       RES ------------------- -------------------

2007-09-05 01:45:34  2007-09-05 04:45:34

 

**numtoyminterval** 与numtodsinterval函数类似,将x转为interval year to month数据类型

常用的单位有'year','month'

example

SQL> select sysdate,sysdate+numtoyminterval(3,'year') as res from dual;

SYSDATE       RES ------------------- -------------------

2007-09-05 01:54:53  2010-09-05 01:54:53

\-----------------------------------------------------------------------------------------------------------------------

格式：NumToYMInterval(n, interval_unit);

n: 数值类型

interval_unit: 'YEAR', 'MONTH' ，或其他可以转换成这两个值之一的表达式

 

NumToYMInterval(1, 'YEAR') ：一年后的间隔

NumToYMInterval(-1, 'MONTH'): 一个月前

 

小数会被计算成整数后，再做计算：

 

select sysdate + numtoyminterval(0.1, 'MONTH') as future from dual;\

 

FUTURE

\------------------

11-OCT-13

 

该函数的结果是：”INTERVAL YEAR TO MONTH literal“。不能与数值做运算。

select 1 + NumToYMInterval(1, 'MONTH') from dual

Oracle会返回一个错误。

 

做日期运算时，这个函数非常有用。例如：取一个月后的日期：

select sysdate + NumToYMInterval(1, 'MONTH') from dual;