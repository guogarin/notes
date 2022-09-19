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

## `MAX()` 函数
### 作用
&emsp;&emsp; `MAX()`函数返回指定列的最大值。

### `MAX()` 语法
```sql
SELECT MAX(column_name) FROM table_name;
```

### 实例
#### 数据和要求
某`user_profile`表如下:
| id  | device_id | gender | age  | university | gpa |
| --- | --------- | ------ | ---- | ---------- | --- |
| 1   | 2234      | male   | 21   | 北京大学   | 3.2 |
| 2   | 2235      | male   | NULL | 复旦大学   | 3.8 |
| 3   | 2236      | female | 20   | 复旦大学   | 3.5 |
| 4   | 2237      | female | 23   | 浙江大学   | 3.3 |
| 5   | 2238      | male   | 25   | 复旦大学   | 3.1 |
| 6   | 2239      | male   | 25   | 北京大学   | 3.6 |
| 7   | 2240      | male   | NULL | 清华大学   | 3.3 |
| 8   | 2241      | female | NULL | 北京大学   | 3.7 |
运营想要知道复旦大学学生gpa最高值是多少，请你取出相应数据，根据输入，你的查询应返回以下结果，结果保留到小数点后面1位(1位之后的四舍五入):
| gpa |
| --- |
| 3.8 |

#### 解答
```sql
select max(gpa)  from user_profile where university = '复旦大学';
```
另外，这个用`limit`子句也能做到。

## `avg()` 函数
### 作用
&emsp;&emsp; `AVG()` 函数返回数值列的平均值。

### AVG() 语法
```sql
SELECT AVG(column_name) FROM table_name;
```

### 实例
#### 数据和要求
&emsp;&emsp; `user_profile`表同`MAX()`函数给出的数据。
&emsp;&emsp; 现在运营想要看一下男性用户有多少人以及他们的平均gpa是多少，用以辅助设计相关活动，请你取出相应数据。
```sql
select count(*) as male_num, 
        avg(gpa) as avg_gpa 
            from user_profile 
                where gender='male';
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

## 实例
### 题目
描述
> &emsp;&emsp; 题目：现在运营只需要查看前2个用户明细设备ID数据，请你从用户信息表 user_profile 中取出相应结果。
> 
示例：
| id  | device_id | gender | age | university | province |
| --- | --------- | ------ | --- | ---------- | -------- |
| 1   | 2138      | male   | 21  | 北京大学   | Beijing  |
| 2   | 3214      | male   |     | 复旦大学   | Shanghai |
| 3   | 6543      | female | 20  | 北京大学   | Beijing  |
| 4   | 2315      | female | 23  | 浙江大学   | ZheJiang |
| 5   | 5432      | male   | 25  | 山东大学   | Shandong |
根据输入，你的查询应返回以下结果：

| device_id |
| --------- |
| 2138      |
| 3214      |

### 解答
```sql
select device_id
    from user_profile 
        order by id 
            limit 2 offset 0;
```






&emsp;
&emsp;
&emsp;
# 过滤空值
### 题目
描述
> &emsp;&emsp; 题目：现在运营想要对用户的年龄分布开展分析，在分析时想要剔除没有获取到年龄的用户，请你取出所有年龄值不为空的用户的设备ID，性别，年龄，学校的信息。
> 
示例：
| id  | device_id | gender | age | university | province |
| --- | --------- | ------ | --- | ---------- | -------- |
| 1   | 2138      | male   | 21  | 北京大学   | Beijing  |
| 2   | 3214      | male   |     | 复旦大学   | Shanghai |
| 3   | 6543      | female | 20  | 北京大学   | Beijing  |
| 4   | 2315      | female | 23  | 浙江大学   | ZheJiang |
| 5   | 5432      | male   | 25  | 山东大学   | Shandong |
根据输入，你的查询应返回以下结果：
| device_id | gender | age | university |
| --------- | ------ | --- | ---------- |
| 2138      | male   | 21  | 北京大学   |
| 6543      | female | 20  | 北京大学   |
| 2315      | female | 23  | 浙江大学   |
| 5432      | male   | 25  | 山东大学   |
### 解答
&emsp;&emsp; 注意，在查询`NULL`时，不能使用比较运算符(`=`或者`< >`)，需要使用`IS NULL`运算符或者`IS NOT NULL`运算符：
```sql
select device_id, gender,age,university 
    from user_profile 
        where age is not null; -- 划重点！
```




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
&emsp;&emsp; 默认是`INNER JOIN`





&emsp;
&emsp;
&emsp;
# `UNION` 操作符
## `union`的作用
&emsp;&emsp; `union` 操作符合并两个或多个 `SELECT` 语句的结果。
&emsp;&emsp; 请需要注意的是，`UNION` 内部的每个 `SELECT` 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每个 `SELECT` 语句中的列的顺序必须相同。

## `union`的语法
```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

## `UNION ALL`的作用和语法
&emsp;&emsp; 默认地，`UNION` 操作符选取不同的值；`UNION ALL`允许重复的值
```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

## 使用 `union`的注意事项
&emsp;&emsp; 使用`UNION`命令时需要注意，只能在最后使用一个`ORDER BY`命令，是将两个查询结果合在一起之后，再进行排序！绝对不能写两个`ORDER BY`命令。

## 使用实例
### 表数据
下面是选自 `Websites` 表的数据：
```sql
mysql> SELECT * FROM Websites;

+----+---------------+---------------------------+-------+---------+
| id | name          | url                       | alexa | country |
+----+---------------+---------------------------+-------+---------+
| 1  | Google        | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook      | https://www.facebook.com/ | 3     | USA     |
| 7  | stackoverflow | http://stackoverflow.com/ |   0 | IND     |
+----+---------------+---------------------------+-------+---------+
```
下面是 `apps` APP 的数据：
```sql
mysql> SELECT * FROM apps;

+----+------------+-------------------------+---------+
| id | app_name   | url                     | country |
+----+------------+-------------------------+---------+
|  1 | QQ APP     | http://im.qq.com/       | CN      |
|  2 | 微博 APP   | http://weibo.com/       | CN      |
|  3 | 淘宝 APP   | https://www.taobao.com/ | CN      |
+----+------------+-------------------------+---------+
3 rows in set (0.00 sec)
```

### `UNION` 实例
```sql
SELECT country FROM Websites
UNION
SELECT country FROM apps
ORDER BY country;
```
运行结果：
```
+---------+
| country |
+---------+
| CN      |
| IND     |
| USA     |
+---------+
```

### `UNION ALL` 实例
```sql
SELECT country FROM Websites
UNION ALL
SELECT country FROM apps
ORDER BY country;
```
运行结果：
```
+---------+
| country |
+---------+
| CN      |
| CN      |
| CN      |
| CN      |
| CN      |
| IND     |
| USA     |
| USA     |
| USA     |
+---------+
```
### 对比
&emsp;&emsp; 显然，`UNION`得到的结果没有重复，而 `UNION ALL` 得到的结果是包含重复的内容的。