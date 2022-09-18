- [](#)
- [SQL 连接(JOIN)](#sql-连接join)
  - [有哪几种连接？这几种连接有哪些用法？](#有哪几种连接这几种连接有哪些用法)
  - [`left outer join`和`left join`有何区别？](#left-outer-join和left-join有何区别)
  - [在对多表进行连接时，后台发生了什么？](#在对多表进行连接时后台发生了什么)
  - [如何理解连接中的 左 和 右？](#如何理解连接中的-左-和-右)
  - [有哪些外连接？如何理解外连接？](#有哪些外连接如何理解外连接)
  - [在`left join`(`right join`)时，`on`和`where`各自起什么作用？](#在left-joinright-join时on和where各自起什么作用)
  - [`join`默认是左连接还是右连接？](#join默认是左连接还是右连接)








&emsp;
&emsp;
&emsp;
# 函数
## 空值替换函数
在MySQL中是`IFNULL`：
```sql
IFNULL(expression, alt_value)
```
如果第一个参数的表达式 `expression` 为 `NULL`，则返回第二个参数的备用值。
在Oracle中则为：`NVL`
```sql
NVL(expression, alt_value)
```





&emsp;
&emsp;
&emsp;
# `LIMIT`子句
## `LIMIT`子句的作用

## `LIMIT`子句的语法
```sql
SELECT select_list
    FROM table_name 
        LIMIT [offset,] row_count; 
```
参数解析：
> `offset` ： 可选参数，指定的第一行的偏移量。在`offse`t第一行的是`0`，而不是`1` 
> `row_count` : 指定的返回最大行数 
> 
举个例子：
```sql
-- 返回计算结果中的前x行数据。
LIMIT x

-- 返回计算结果中从y行开始的x行数据。
LIMIT y, x
```
<div align="center"> <img src="./pic/sql_grammar/LIMIT子句.png"> </div>
<center> <font color=black> <b> LIMIT子句 </b> </font> </center>







&emsp;
&emsp;
&emsp;
# SQL 连接(JOIN)
## 有哪几种连接？这几种连接有哪些用法？
&emsp;&emsp; 
> ① `inner join`（内连接）：只返回两个表中连接字段相等的行。
> ② `left join` （左连接）：返回包括左表中的所有记录和右表中连接字段相等的记录。
> ③ `right join`（右连接）：返回包括右表中的所有记录和左表中连接字段相等的记录。
> ④ `full join` （全外连接）：返回左右表中所有的记录和左右表中连接字段相等的记录。(需要注意的是，关键字`outer`是可选择的，取决于具体语言，在实现上它们都是遵循标准的，因此`FULL JOIN`和`FULL OUTER JOIN`是一样的。)
> 
它们有如下图7种用法：
<div align="center"> <img src="./pic/sql_grammar/joins.png"> </div>
<center> <font color=black> <b> 4种连接的7种用法 </b> </font> </center>

## `left outer join`和`left join`有何区别？
`left join`是`left outer join`的缩写，`join`一共有三种`OUTER JOIN`:
> ① `LEFT OUTER JOIN`
> ② `RIGHT OUTER JOIN`
> ③ `FULL OUTER JOIN`
> &emsp;&emsp; 其中关键字`OUTER`是可选择的，取决于具体语言，在实现上它们都是遵循标准的。
> 

## 在对多表进行连接时，后台发生了什么？
&emsp;&emsp; 在连接两张(或多张)来时，数据库会生成一张中间的临时表，然后再将这张临时表返回给用户。
&emsp;&emsp; 连接的结果可以在逻辑上看作是由`SELECT`语句指定的列组成的新表。

## 如何理解连接中的 左 和 右？
&emsp;&emsp; 左连接与右连接的 **左右** 指的是以两张表中的哪一张为基准，它们都是外连接。

## 有哪些外连接？如何理解外连接？
`join`一共有三种`OUTER JOIN`:
> ① `LEFT OUTER JOIN`
> ② `RIGHT OUTER JOIN`
> ③ `FULL OUTER JOIN`
> &emsp;&emsp; 其中关键字`OUTER`是可选择的，取决于具体语言，在实现上它们都是遵循标准的。
> 
&emsp;&emsp; 外连接就好像是为非基准表添加了一行全为空值的万能行，用来与基准表中找不到匹配的行进行匹配。假设两个没有空值的表进行左连接，左表是基准表，左表的所有行都出现在结果中，右表则可能因为无法与基准表匹配而出现是空值的字段。

## 在`left join`(`right join`)时，`on`和`where`各自起什么作用？
在使用 `join` 时，`on` 和 `where` 条件的区别如下：
> &emsp;&emsp; `on` 条件是在生成临时表时使用的条件，它不管 `on` 中的条件是否为真，都会返回左边表(若是`right join`则返回的是右边)中的记录。
> &emsp;&emsp; `where` 条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有 left join 的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。
> 

## `join`默认是左连接还是右连接？

