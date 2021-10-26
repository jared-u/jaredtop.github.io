---
title: MySQL 常用函数
tags:
  - MySQL 
categories:
  - [MySQL]
date: 2021-10-19 10:07:00
description: MySQL 常用函数
---
# 字符串函数

## LOWER(column|str)

将字符串参数值转换为全小写字母后返回

```mysql
select LOWER("SQL LOWER")
-> sql lower
```

## UPPER(column|str)

将字符串参数值转换为全大写字母后返回

```mysql
select UPPER("sql upper")
-> SQL UPPER
```

## CONCAT(column|str1, column|str2,...)

将多个字符串参数首尾相连后返回

```mysql
SELECT CONCAT("My","S","Q","L")
-> MySQL
```

**如果有任何参数为null，则函数返回null**

```mysql
SELECT CONCAT("My",null,"Q","L");
-> NULL
```

**如果参数是数字，则自动转换为字符串**

```mysql
SELECT CONCAT("My",123,"Q","L");
-> My123QL
```

## CONCAT_WS(separator,str1,str2,...)

将多个字符串参数以给定的分隔符separator首尾相连后返回

```mysql
SELECT CONCAT_WS("&","You","Me","other");
-> You&Me&other
```

**！！也就是函数圆括号里的第一个项目用来指定分隔符**

> 注意：如果有任何参数为null，则函数不返回null，而是直接忽略它

```mysql
SELECT CONCAT_WS("&","You",null,"other");
-> You&other
```

**打开和关闭管道符号“|”的连接功能**

​	PIPES_AS_CONCAT：将“||”视为字符串的连接操作符而非或运算符

​	|| 管道连接符：

​	基本格式：

```mysql
mysql> select 列名1 || 列名2 || 列名3  from  表名;
```

在mysql中，进行上式连接查询之后，会将查询结果集在一列中显示(字符串连接)，列名是‘列名1 || 列名2 || 列名3’；

```mysql
mysql> select s_no || s_name || s_age
    -> from student;
+-------------------------+
| s_no || s_name || s_age |
+-------------------------+
| 1001张三23              |
| 1002李四19              |
| 1003马五20              |
| 1004甲六17              |
| 1005乙七22              |
+-------------------------+
```

> 注意：
>
> ①如果不显示结果，是因为sql_mode参数中没有PIPES_AS_CONCAT，只要给sql_mode参数加入PIPES_AS_CONCAT，就可以实现像CONCAT一样的功能；
>
> ②如果不给sql_mode参数加入PIPES_AS_CONCAT的话，|| 默认是or的意思，查询结果是一列显示是1。

## SUBSTR(str,pos[,len])

从源字符串str中的指定位置pos开始取一个字串并返回

> 注意：
>
> ①len指定子串的长度，如果省略则一直取到字符串的末尾；len为负值表示从源字符串的尾部开始取起。
>
> ②函数SUBSTR()是函数SUBSTRING()的同义词。

```mysql
select substring('hello boy',5);
-> o boy

select substr('hello boy',5,3);
-> o b

select substr('hello boy',-2);
-> oy
```

## LENGTH(str)：

返回字符串的存储长度

```mysql
select length('text'),length('你好');
-> 4	6
```

> 注意：编码方式不同字符串的存储长度就不一样(‘你好’:utf8是6，gbk是4)

## CHAR_LENGTH(str)：

返回字符串中的字符个数

```mysql
select char_length('text'),char_length('你好');
-> 4	2
```

## INSTR(str, substr)

从源字符串str中返回子串substr第一次出现的位置

```mysql
select instr('foobarbar','bar');
-> 4
```

## LPAD(str, len, padstr)

在源字符串的左边填充给定的字符padstr到指定的长度len，返回填充后的字符串

```mysql
select lpad('hi',5,'??');
-> ???hi
```

## RPAD(str, len, padstr)

在源字符串的右边填充给定的字符padstr到指定的长度len，返回填充后的字符串

```mysql
select rpad('hi',6,'??');
-> hi????
```

## TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str), TRIM([remstr FROM] str)

从源字符串str中去掉两端、前缀或后缀字符remstr并返回；

如果不指定remstr，则去掉str两端的空格；

不指定BOTH、LEADING、TRAILING ，则默认为 BOTH。

```mysql
select trim('  bar  ');
->bar
select trim(leading 'x' from 'xxxbarxxx');
->barxxx
select trim(both 'x' from 'xxxbarxxx');
->bar
select trim(trailing 'xyz' from 'xyzbarxxyz');
->xyzbarx
```

## REPLACE(str, from_str, to_str)

在源字符串str中查找所有的子串form_str（大小写敏感），找到后使用替代字符串to_str替换它。返回替换后的字符串

```mysql
select replace('www.mysql.com','w','Ww');
->WwWwWw.mysql.com
```

## LTRIM(str)，RTRIM(str)：

去掉字符串的左边或右边的空格(左对齐、右对齐)

```mysql
SELECT  ltrim('   barbar   ') rs1, rtrim('   barbar   ') rs2;
->barbar   
->   barbar

```

## REPEAT(str, count)

将字符串str重复count次后返回

```mysql
select repeat('MySQL',3);
->MySQLMySQLMySQL
```

## REVERSE(str)：

将字符串str反转后返回

```mysql
select reverse('abcdef');
->fedcba
```

## CHAR(N,... [USING charset_name])

将每个参数N解释为整数（字符的编码），并返回每个整数对应的字符所构成的字符串(NULL值被忽略)。

```mysql
select char(77,121,83,81,'76'),char(77,77.3,'77.3');
MySQL	MMM
```

默认情况下，函数返回二进制字符串，若想返回针对特定字符集的字符串，使用using选项

```mysql
SELECT charset(char(0x65)), charset(char(0x65 USING utf8));
->binary	utf8
```

## FORMAT(X,D[,locale])：

以格式‘#,###,###.##’格式化数字X

D指定小数位数

locale指定国家语言(默认的locale为en_US)

```mysql
SELECT format(12332.123456, 4),format(12332.2,0);
->12,332.1235	12,332

SELECT format(12332.2,2,'de_DE');
->12.332,20
```

## SPACE(N)

返回由N个空格构成的字符串

```mysql
select space(3);
->   
```

## LEFT(str, len)

返回最左边的len长度的子串

```mysql
select left('chinaitsoft',5);
->china
```

## RIGHT(str, len)

返回最右边的len长度的子串

```mysql
select right('chinaitsoft',5);
->tsoft
```

## STRCMP(expr1,expr2)

如果两个字符串是一样的则返回0；如果第一个小于第二个则返回-1；否则返回1

```mysql
select strcmp('text','text');
->0

SELECT strcmp('text', 'text2'),strcmp('text2', 'text');
-> -1  1
```

# 数学函数

## ROUND(X,Y) 

对X值进行四舍五入 保留小数点后面的Y位

```mysql
select ROUND(-1.55);
select ROUND(1.567,2);
-> -2 1.57
```

## ABS(x)

返回 x 的绝对值

```mysql
SELECT ABS(-1);
->1
```

## AVG(expression)

返回一个表达式的平均值，expression 是一个字段

```mysql
SELECT AVG(age) FROM user
-> 22.5000
```

## CEIL(x) CEILING(x)

返回大于或等于 x 的最小整数　

```mysql
select CEIL(1.5)
-> 2
select CEILING(1.5)
-> 2
```

## FLOOR(x)

返回小于或等于 x 的最大整数

```mysql
select floor(1.5)
-> 1
```

## MAX(expression)

 返回字段 expression 中的最大值

```mysql
select max(age) from user
-> 23
```

## MIN(expression)

返回字段 expression 中的最小值

```mysql
select min(age) from user
->22
```

## SUM(expression)

返回指定字段的总和

```mysql
select sum(age) from user
->45
```

# 日期函数

## ADDDATE(d,n)

计算起始日期 d 加上 n 天的日期

```mysql
SELECT ADDDATE("2017-06-15", INTERVAL 10 DAY);
->2017-06-25
```

## ADDTIME(t,n)

n 是一个时间表达式，时间 t 加上时间表达式 n

```mysql
加 5 秒：
SELECT ADDTIME('2011-11-11 11:11:11', 5);
->2011-11-11 11:11:16 (秒)

添加 2 小时, 10 分钟, 5 秒:
SELECT ADDTIME("2020-06-15 09:34:21", "2:10:5"); 
-> 2020-06-15 11:44:26
```

## DATEDIFF(d1,d2)

计算日期 d1->d2 之间相隔的天数

```mysql
SELECT DATEDIFF('2001-01-01','2001-02-02')
-> -32
```

## DATE_ADD(d，INTERVAL expr type)

计算起始日期 d 加上一个时间段后的日期，type 值可以是：

- MICROSECOND
- SECOND
- MINUTE
- HOUR
- DAY
- WEEK
- MONTH
- QUARTER
- YEAR
- SECOND_MICROSECOND
- MINUTE_MICROSECOND
- MINUTE_SECOND
- HOUR_MICROSECOND
- HOUR_SECOND
- HOUR_MINUTE
- DAY_MICROSECOND
- DAY_SECOND
- DAY_MINUTE
- DAY_HOUR
- YEAR_MONTH

```mysql
SELECT DATE_ADD("2017-06-15", INTERVAL 10 DAY);    
-> 2017-06-25

SELECT DATE_ADD("2017-06-15 09:34:21", INTERVAL 15 MINUTE);
-> 2017-06-15 09:49:21

SELECT DATE_ADD("2017-06-15 09:34:21", INTERVAL -3 HOUR);
->2017-06-15 06:34:21
```

## DATE_FORMAT(d,f)

按表达式 f的要求显示日期 d

```mysql
SELECT DATE_FORMAT('2011-11-11 11:11:11','%Y-%m-%d %r')
-> 2011-11-11 11:11:11 AM
```

## DATE_SUB(date,INTERVAL expr type)

函数从日期减去指定的时间间隔。

```mysql
SELECT OrderId,DATE_SUB(OrderDate,INTERVAL 2 DAY) AS OrderPayDate
FROM Orders
```

## DAY(d)

返回日期值 d 的日期部分

```mysql
	
SELECT DAY("2017-06-15");  
-> 15
```

## DAYNAME(d)

返回日期 d 是星期几，如 Monday,Tuesday

```mysql
SELECT DAYNAME('2011-11-11 11:11:11')
->Friday
```

## DAYOFMONTH(d)

计算日期 d 是本月的第几天

```mysql
SELECT DAYOFMONTH('2011-11-11 11:11:11')
->11
```

## DAYOFWEEK(d)

日期 d 今天是星期几，1 星期日，2 星期一，以此类推

```mysql
SELECT DAYOFWEEK('2011-11-11 11:11:11')
->6
```

## DAYOFYEAR(d)

 计算日期 d 是本年的第几天

```mysql
SELECT DAYOFYEAR('2011-11-11 11:11:11')
->315
```

## EXTRACT(type FROM d)

从日期 d 中获取指定的值，type 指定返回的值。
type可取值为：

- MICROSECOND
- SECOND
- MINUTE
- HOUR
- DAY
- WEEK
- MONTH
- QUARTER
- YEAR
- SECOND_MICROSECOND
- MINUTE_MICROSECOND
- MINUTE_SECOND
- HOUR_MICROSECOND
- HOUR_SECOND
- HOUR_MINUTE
- DAY_MICROSECOND
- DAY_SECOND
- DAY_MINUTE
- DAY_HOUR
- YEAR_MONTH

```mysql
SELECT EXTRACT(MINUTE FROM '2011-11-11 11:11:11') 
-> 11
```

## HOUR(t)

返回 t 中的小时值

```mysql
SELECT HOUR('1:2:3')
-> 1
```

## LAST_DAY(d)

返回给给定日期的那一月份的最后一天

```mysql
SELECT LAST_DAY("2017-06-20");
-> 2017-06-30
```

# 高级函数

## BIN(x)

 返回 x 的二进制编码

```mysql
SELECT BIN(15); 
-> 1111
```

## BINARY(s)

将字符串 s 转换为二进制字符串

```mysql
SELECT BINARY "RUNOOB";
-> RUNOOB
```

## CONV(x,f1,f2)

 返回 f1 进制数变成 f2 进制数

```mysql
	
SELECT CONV(15, 10, 2);
-> 1111
```

