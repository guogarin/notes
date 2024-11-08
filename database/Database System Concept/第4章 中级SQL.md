[toc]





# 1. 连接表达式
## 1.0 数据
在本小结关于连接的SQL的运行结果都是基于如下数据：
### 1.0.1 建表语句以及数据
```sql
-- 用户信息表
CREATE TABLE `user_profile` (
	`id` INT(11) NOT NULL,
	`name` VARCHAR(14) NOT NULL COLLATE 'utf8_general_ci',
	`age` INT(11) NULL DEFAULT NULL
)
COLLATE='utf8_general_ci'
ENGINE=InnoDB
;

-- 答题情况表
CREATE TABLE `question_practice_detail` (
	`id` INT(11) NOT NULL,
	`question_id` INT(11) NOT NULL,
	`result` VARCHAR(32) NOT NULL COLLATE 'utf8_general_ci'
)
COLLATE='utf8_general_ci'
ENGINE=InnoDB
;

-- 数据
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (38, '张三', 21);
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (14, '李四', 19);
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (43, '王五', 20);
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (15, '赵六', 23);
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (32, '孙七', 25);
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (31, '周八', 28);
INSERT INTO `user_profile` (`id`, `name`, `age`) VALUES (21, '吴九', 28);

INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (38, 111, 'wrong');
INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (14, 112, 'wrong');
INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (43, 111, 'right');
INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (15, 115, 'right');
INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (31, 114, 'right');
INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (32, 113, 'wrong');
INSERT INTO `question_practice_detail` (`id`, `question_id`, `result`) VALUES (9, 112, 'right');
```

### 1.0.2 表里的数据
`user_profile`:
---
| id | name | age | 
| ---: | --- | ---: | 
| 38 | 张三 | 21 | 
| 14 | 李四 | 19 | 
| 43 | 王五 | 20 | 
| 15 | 赵六 | 23 | 
| 32 | 孙七 | 25 | 
| 31 | 周八 | 28 | 
| 21 | 吴九 | 28 | 

`question_practice_detail`:
---
| id | question_id | result | 
| ---: | ---: | --- | 
| 38 | 111 | wrong | 
| 14 | 112 | wrong | 
| 43 | 111 | right | 
| 15 | 115 | right | 
| 31 | 114 | right | 
| 32 | 113 | wrong | 
| 9 | 112 | right | 



&emsp;
## 1.1 笛卡尔积(Cartesian product)
### 1.1.1 什么是笛卡尔积？
&emsp;&emsp; 笛卡尔积原本是代数的概念，他的意思是对于两个不同的集合A，B。对于A中的每一个元素，都有对于在B中的所有元素做连接运算 。可以见得对于两个元组分别为m，n的表。笛卡尔积后得到的元组个数为m x n个元组。
&emsp;&emsp; 例如现在我们有两个集合 `A = {0,1}` , `B = {2,3,4}`,那么，集合 `A * B` 得到的结果就是
> A * B = {(0,2)、(1,2)、(0,3)、(1,3)、(0,4)、(1,4)};
> 
`B * A` 得到的结果就是
> B * A = {(2,0)、{2,1}、{3,0}、{3,1}、{4,0}、(4,1)};
> 
上面 `A * B` 和 `B * A` 的结果就可以称为两个集合相乘的 笛卡尔积


### 1.1.2 如何得到笛卡尔积？
&emsp;&emsp; 笛卡尔积是指将两个或多个表中的每一行组合在一起，形成所有可能的组合。我们可以通过三种方法得到笛卡尔积：
> ① 两个表在`FROM`后面用逗号隔开，且不添加任何选择条件；
> ② 使用`CROSS JOIN`；
> ③ 当在MySQL中使用`(INNER) JOIN`语句时，如果**没有明确指定连接条件** 或 **使用`WHERE`子句**，那么将产生笛卡尔积（Cartesian product）。
> 
```sql
-- 笛卡尔积 1
SELECT * FROM user_profile, question_practice_detail;

-- 笛卡尔积 2
SELECT * FROM user_profile  cross JOIN question_practice_detail;

-- 笛卡尔积 3
SELECT * FROM user_profile  inner JOIN question_practice_detail;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 38 | 111 | wrong | 
| 43 | 王五 | 20 | 38 | 111 | wrong | 
| 15 | 赵六 | 23 | 38 | 111 | wrong | 
| 32 | 孙七 | 25 | 38 | 111 | wrong | 
| 31 | 周八 | 28 | 38 | 111 | wrong | 
| 21 | 吴九 | 28 | 38 | 111 | wrong | 
| 38 | 张三 | 21 | 14 | 112 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 14 | 112 | wrong | 
| 15 | 赵六 | 23 | 14 | 112 | wrong | 
| 32 | 孙七 | 25 | 14 | 112 | wrong | 
| 31 | 周八 | 28 | 14 | 112 | wrong | 
| 21 | 吴九 | 28 | 14 | 112 | wrong | 
| 38 | 张三 | 21 | 43 | 111 | right | 
| 14 | 李四 | 19 | 43 | 111 | right | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 43 | 111 | right | 
| 32 | 孙七 | 25 | 43 | 111 | right | 
| 31 | 周八 | 28 | 43 | 111 | right | 
| 21 | 吴九 | 28 | 43 | 111 | right | 
| 38 | 张三 | 21 | 15 | 115 | right | 
| 14 | 李四 | 19 | 15 | 115 | right | 
| 43 | 王五 | 20 | 15 | 115 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 32 | 孙七 | 25 | 15 | 115 | right | 
| 31 | 周八 | 28 | 15 | 115 | right | 
| 21 | 吴九 | 28 | 15 | 115 | right | 
| 38 | 张三 | 21 | 31 | 114 | right | 
| 14 | 李四 | 19 | 31 | 114 | right | 
| 43 | 王五 | 20 | 31 | 114 | right | 
| 15 | 赵六 | 23 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 31 | 114 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 21 | 吴九 | 28 | 31 | 114 | right | 
| 38 | 张三 | 21 | 32 | 113 | wrong | 
| 14 | 李四 | 19 | 32 | 113 | wrong | 
| 43 | 王五 | 20 | 32 | 113 | wrong | 
| 15 | 赵六 | 23 | 32 | 113 | wrong | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 
| 31 | 周八 | 28 | 32 | 113 | wrong | 
| 21 | 吴九 | 28 | 32 | 113 | wrong | 
| 38 | 张三 | 21 | 9 | 112 | right | 
| 14 | 李四 | 19 | 9 | 112 | right | 
| 43 | 王五 | 20 | 9 | 112 | right | 
| 15 | 赵六 | 23 | 9 | 112 | right | 
| 32 | 孙七 | 25 | 9 | 112 | right | 
| 31 | 周八 | 28 | 9 | 112 | right | 
| 21 | 吴九 | 28 | 9 | 112 | right | 



&emsp;
## 1.2 等值连接(Equijoin) 和 非等值连接(NON EQUI JOIN)
### 1.2.1 等值连接(Equijoin)
&emsp;&emsp; 等值连接在连接条件中使用 **等于号`=`** 运算符比较被连接列的列值，其查询结果中列出被连接表中的所有列，包括其中的重复列。

### 1.2.2 非等值连接(NON EQUI JOIN)
&emsp;&emsp; 在连接条件使用除等于运算符以外的比较运算符比较被连接的列的列值。这些运算符包括`>、>=、<=、<、!>、!<`和`<>`。
```sql
SELECT p.player_name, p.height, h.height_level
FROM player AS p INNER JOIN height_grades AS h
on p.height BETWEEN h.height_lowest AND h.height_highest
```


&emsp;
## 1.3 自然连接(natural join)
### 1.3.1 什么是自然连接？它和笛卡尔积有何区别？
&emsp;&emsp; 自然连接运算作用于两个表，并产生一个表作为结果。与两个表的笛卡尔积不同的是，自然连接只考虑 在两个表的模式中都出现的那些属性上 取值相同 的元组对，而笛卡尔积将第一个表的每个元组与第二个表的每个元组进行串接。
&emsp;&emsp; 自然连接 要求两个关系表中进行比较的必须是相同的属性列，无须添加连接条件，并且会在结果中消除重复的属性列。

### 1.3.2 自然连接和等值连接有何关系？
&emsp;&emsp; 自然连接，也是EQUI JOIN的一种，其结构使得具有相同名称的关联表的列将只出现一次。
&emsp;&emsp; 在连接条件中使用**等于号=**运算符比较被连接列的列值，但它使用选择列表指出查询结果集合中所包括的列，并删除连接表中的重复列：
> &emsp;&emsp; 所谓自然连接就是在等值连接的情况下，当连接属性X与Y具有相同属性组时，把在连接结果中重复的属性列去掉。
> &emsp;&emsp; 自然连接是在广义笛卡尔积R×S中选出同名属性上符合相等条件元组，再进行投影，去掉重复的同名属性，组成新的关系。
> 

### 1.3.3 如何得到 自然连接？
```sql
-- 自然连接
SELECT * FROM user_profile  natural JOIN question_practice_detail;
```
执行结果：
| id | name | age | question_id | result | 
| ---: | --- | ---: | ---: | --- | 
| 38 | 张三 | 21 | 111 | wrong | 
| 14 | 李四 | 19 | 112 | wrong | 
| 43 | 王五 | 20 | 111 | right | 
| 15 | 赵六 | 23 | 115 | right | 
| 31 | 周八 | 28 | 114 | right | 
| 32 | 孙七 | 25 | 113 | wrong | 

### 1.3.4 如果自然连接的两个表有多个属性名相同，会发生什么？
&emsp;&emsp; 如果自然连接的两个表有多个属性名相同，则只有在这些属性名的取值都相同时，自然连接才会将其进行串接。

### 1.3.5 join using
&emsp;&emsp; 为了发扬自然连接的优点，同时避免不正确的相等属性所带来的危险，SQL提供了一种自然连接的构造方式：`join ... using`子句，它允许你来制定究竟需要哪些列相等。
```sql
SELECT player_id, team_id, player_name, height, team_name 
    FROM player JOIN team USING(team_id)
```

### 1.3.6 自然连接的使用场景
&emsp;&emsp; 自然连接在日常中使用的很少，完全可以用内连接来替代，而且内连接还比自然连接直观。


&emsp;
## 1.4 内连接
### 1.4.1 内连接 和 自然连接 有何异同？

&emsp;&emsp; 内连接基本与自然连接相同，它们的不同之处在于：
&emsp;&emsp; 第一，自然连接是对连接的表的同名属性列进行比较；而内连接则不要求两属性列同名，可以用`using`或`on`来指定连接条件。
&emsp;&emsp; 第二，对于两张表中列名相同的属性，自然连接只会返一次，而内连接会返回两次。例证如下：
```sql
select * from Sales natural join product;

select * from Sales  join Product on Sales.product_id=Product.product_id;
```
执行结果如下：
```
| product_id | sale_id | year | quantity | price | product_name |
| ---------- | ------- | ---- | -------- | ----- | ------------ |
| 100        | 2       | 2009 | 12       | 5000  | Nokia        |
| 100        | 1       | 2008 | 10       | 5000  | Nokia        |
| 200        | 7       | 2011 | 15       | 9000  | Apple        |

| sale_id | product_id | year | quantity | price | product_id | product_name |
| ------- | ---------- | ---- | -------- | ----- | ---------- | ------------ |
| 2       | 100        | 2009 | 12       | 5000  | 100        | Nokia        |
| 1       | 100        | 2008 | 10       | 5000  | 100        | Nokia        |
| 7       | 200        | 2011 | 15       | 9000  | 200        | Apple        |
```
可以看到的是，`join on`比  `natural join`多了一个`product_id`属性，那是因为 `select *`将`Sales`表 和 `product`表的 `product_id`属性 都筛选了出来。
[Difference between natural join and inner join](https://stackoverflow.com/questions/8696383/difference-between-natural-join-and-inner-join)

### 1.4.2 若内连接和外连接不带连接条件，输出的结果将是？
&emsp;&emsp; 内连接 如果不带连接条件，得到的结果是笛卡尔积。
&emsp;&emsp; 但对于外连接，如果不带连接条件，则会报错，因为外连接必须使用 `using`或`on`指定连接条件，若果不带条件、或者使用`where` 都会报错。
```sql
-- 外连接 不带on条件
SELECT * FROM user_profile  left JOIN question_practice_detail;
```
执行结果：
```
/* SQL错误（1064）：You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '' at line 1 */
```

```sql
-- 内连接 不带on条件
SELECT * FROM user_profile  inner JOIN question_practice_detail;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 38 | 111 | wrong | 
| 43 | 王五 | 20 | 38 | 111 | wrong | 
| 15 | 赵六 | 23 | 38 | 111 | wrong | 
| 32 | 孙七 | 25 | 38 | 111 | wrong | 
| 31 | 周八 | 28 | 38 | 111 | wrong | 
| 21 | 吴九 | 28 | 38 | 111 | wrong | 
| 38 | 张三 | 21 | 14 | 112 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 14 | 112 | wrong | 
| 15 | 赵六 | 23 | 14 | 112 | wrong | 
| 32 | 孙七 | 25 | 14 | 112 | wrong | 
| 31 | 周八 | 28 | 14 | 112 | wrong | 
| 21 | 吴九 | 28 | 14 | 112 | wrong | 
| 38 | 张三 | 21 | 43 | 111 | right | 
| 14 | 李四 | 19 | 43 | 111 | right | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 43 | 111 | right | 
| 32 | 孙七 | 25 | 43 | 111 | right | 
| 31 | 周八 | 28 | 43 | 111 | right | 
| 21 | 吴九 | 28 | 43 | 111 | right | 
| 38 | 张三 | 21 | 15 | 115 | right | 
| 14 | 李四 | 19 | 15 | 115 | right | 
| 43 | 王五 | 20 | 15 | 115 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 32 | 孙七 | 25 | 15 | 115 | right | 
| 31 | 周八 | 28 | 15 | 115 | right | 
| 21 | 吴九 | 28 | 15 | 115 | right | 
| 38 | 张三 | 21 | 31 | 114 | right | 
| 14 | 李四 | 19 | 31 | 114 | right | 
| 43 | 王五 | 20 | 31 | 114 | right | 
| 15 | 赵六 | 23 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 31 | 114 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 21 | 吴九 | 28 | 31 | 114 | right | 
| 38 | 张三 | 21 | 32 | 113 | wrong | 
| 14 | 李四 | 19 | 32 | 113 | wrong | 
| 43 | 王五 | 20 | 32 | 113 | wrong | 
| 15 | 赵六 | 23 | 32 | 113 | wrong | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 
| 31 | 周八 | 28 | 32 | 113 | wrong | 
| 21 | 吴九 | 28 | 32 | 113 | wrong | 
| 38 | 张三 | 21 | 9 | 112 | right | 
| 14 | 李四 | 19 | 9 | 112 | right | 
| 43 | 王五 | 20 | 9 | 112 | right | 
| 15 | 赵六 | 23 | 9 | 112 | right | 
| 32 | 孙七 | 25 | 9 | 112 | right | 
| 31 | 周八 | 28 | 9 | 112 | right | 
| 21 | 吴九 | 28 | 9 | 112 | right | 

**显然，内连接不带连接条件的话，得到的都是笛卡尔积。**

### 1.4.3 如何使用内连接？
<div align="center"> <img src="./pic/inner_join.gif"> </div>

&emsp;&emsp; 和自然连接不一样的是，内连接一般会用`on`或`using`指定连接条件（如果内连接不带连接条件，则得到的是两个表的笛卡尔积）：
```sql
SELECT * FROM user_profile  INNER JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 


&emsp;
## 1.5 外连接(outer join)
### 1.5.1 为什么需要外连接
&emsp;&emsp; 当我们想知道参赛者的答题情况时，可以通过对`user_profile`和`question_practice_detail`进行内连接：
```sql
SELECT * FROM user_profile  INNER JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 

但我们发现，用户`吴九`并没有出现在查询结果中，那是因为他压根就没答题，因此在内连接时，在`question_practice_detail`表中找不到对应的数据，所以不会出现在内连接的结果中。
&emsp;&emsp; 如果我们想让所有用户都出现在查询结果中（无论该用户是否答题），该怎么做呢？这是外连接就发挥作用了：
```sql
SELECT *
FROM user_profile
LEFT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 
| 21 | 吴九 | 28 | null | null | null | 

可以看到的是，用户`吴九`出现在了左连接的查询结果中。

### 1.5.2 有哪些外连接？
&emsp;&emsp; 外连不但返回符合连接和查询条件的数据行，还返回不符合条件的一些行。
&emsp;&emsp; 外连接就好像是为非基准表添加了一行全为空值的万能行，用来与基准表中找不到匹配的行进行匹配。假设两个没有空值的表进行左连接，左表是基准表，左表的所有行都出现在结果中，右表则可能因为无法与基准表匹配而出现是空值的字段。
> ① left join （左连接）：返回包括左表中的所有记录和右表中连接字段相等的记录。
> ② right join（右连接）：返回包括右表中的所有记录和左表中连接字段相等的记录。
> ③ full join （全外连接）：返回左右表中所有的记录和左右表中连接字段相等的记录。(需要注意的是，关键字outer是可选择的，取决于具体语言，在实现上它们都是遵循标准的，因此FULL JOIN和FULL OUTER JOIN是一样的。)
> 

### 1.5.3 外连接是不是必须带连接条件？
是的，外连接必须使用 `using`或`on`指定连接条件。不带条件 或 使用`where`都会报错：
```sql
SELECT * FROM user_profile  left JOIN question_practice_detail;
```
执行结果：
```
/* SQL错误（1064）：You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '' at line 1 */
```
使用`where`条件也是一样报错：
```sql
SELECT *
FROM user_profile
LEFT JOIN question_practice_detail
WHERE question_practice_detail.id=user_profile.id;
```
执行结果：
```
/* SQL错误（1064）：You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '' at line 1 */
```

### 1.5.4 左连接
<div align="center"> <img src="./pic/left_join.gif"> </div>

```sql
SELECT *
FROM user_profile
LEFT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 
| 21 | 吴九 | 28 | null | null | null | 


### 1.5.5 右连接
<div align="center"> <img src="./pic/right_join.gif"> </div>

```sql
SELECT *
FROM user_profile
RIGHT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| null | null | null | 9 | 112 | right | 

### 1.5.6 全连接
MySQL没有全连接，我们可以使用`UNION`来实现全连接：
```sql
SELECT *
FROM user_profile
LEFT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id 

UNION

SELECT *
FROM user_profile
RIGHT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
| id | name | age | id | question_id | result | 
| ---: | --- | ---: | ---: | ---: | --- | 
| 38 | 张三 | 21 | 38 | 111 | wrong | 
| 14 | 李四 | 19 | 14 | 112 | wrong | 
| 43 | 王五 | 20 | 43 | 111 | right | 
| 15 | 赵六 | 23 | 15 | 115 | right | 
| 31 | 周八 | 28 | 31 | 114 | right | 
| 32 | 孙七 | 25 | 32 | 113 | wrong | 
| 21 | 吴九 | 28 | null | null | null | 
| null | null | null | 9 | 112 | right | 

###  1.5.7 `left outer join`和`left join`有何区别？
&emsp;&emsp; left join`是`left outer join`的缩写，关键字`OUTER`是可选择的，取决于具体语言，在实现上它们都是遵循标准的。

### 1.5.8 如何理解外连接中的 左 和 右？
&emsp;&emsp; 左连接与右连接的 **左右** 指的是以两张表中的哪一张为基准表，它们都是外连接。


&emsp;
## 1.6 关于连接的一些问题
### 当`join`没有任何前缀时，使用的表连接方式是什么？（表的默认连接是什么？）
&emsp;&emsp; `join`是内连接，也就是说`INNER JOIN`中的`INNER`是可以省略的。

### 
<div align="center"> <img src="./pic/joins.png"> </div>
<center> <font color=black> <b> 4种连接的7种用法 </b> </font> </center>

### 在对多表进行连接时，后台发生了什么？
&emsp;&emsp; 在连接两张(或多张)来时，数据库会生成一张中间的临时表，然后再将这张临时表返回给用户。
&emsp;&emsp; 连接的结果可以在逻辑上看作是由`SELECT`语句指定的列组成的新表。

### 在`left join`(`right join`)时，`on`和`where`各自起什么作用？
在使用 `join` 时，`on` 和 `where` 条件的区别如下：
> &emsp;&emsp; `on` 条件是在生成临时表时使用的条件，它不管 `on` 中的条件是否为真，都会返回左边表(若是`right join`则返回的是右边)中的记录。
> &emsp;&emsp; `where` 条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有 left join 的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。
> 

### `join on`里的`on`条件明明可以用 `where`子句来代替，那为什么还要引入`on`条件呢？
&emsp;&emsp; **ON条件** 是过滤两个链接表笛卡尔积形成中间表的约束条件。
&emsp;&emsp; **WHERE条件** 在有ON条件的SELECT语句中是过滤中间表的约束条件。在没有ON的单表查询中，是限制物理表或者中间查询结果返回记录的约束。在两表或多表连接中是限制连接形成最终中间表的返回结果的约束。
&emsp;&emsp;  从这里可以看出，将WHERE条件移入ON后面是不恰当的。推荐的做法是：
> ON只进行连接操作，WHERE只过滤中间表的记录。
> 
&emsp;&emsp; 另外，在ON子句中指定连接条件，并在WHERE子句中出现其余的条件，这样的SQL可读性更强。









[SQL JOIN 语句详解](https://www.lixueduan.com/posts/mysql/02-join-detail/)
```sql

```
执行结果：