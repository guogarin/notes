[toc]





# 1. 连接表达式
## 0. 数据
在本小结关于连接的SQL的运行结果都是基于如下数据：
### 0.1 建表语句以及数据
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

### 0.2 表里的数据
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
| id | question_id | result | 
| ---: | ---: | --- | 
| 38 | 111 | wrong | 
| 14 | 112 | wrong | 
| 43 | 111 | right | 
| 15 | 115 | right | 
| 31 | 114 | right | 
| 32 | 113 | wrong | 
| 9 | 112 | right | 


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



## 等值连接(Equijoin)
&emsp;&emsp; 等值连接在连接条件中使用 **等于号`=`** 运算符比较被连接列的列值，其查询结果中列出被连接表中的所有列，包括其中的重复列。

## 非等值连接(NON EQUI JOIN)
&emsp;&emsp; 在连接条件使用除等于运算符以外的比较运算符比较被连接的列的列值。这些运算符包括`>、>=、<=、<、!>、!<`和`<>`。
```sql
SELECT p.player_name, p.height, h.height_level
FROM player AS p INNER JOIN height_grades AS h
on p.height BETWEEN h.height_lowest AND h.height_highest
```

## 1.2 自然连接(natural join)
### 1.2.1 什么是自然连接？它和笛卡尔积有何区别？
&emsp;&emsp; 自然连接运算作用于两个表，并产生一个表作为结果。与两个表的笛卡尔积不同的是，自然连接只考虑在两个表的模式中都出现的那些属性上取值相同的元组对，而笛卡尔积将第一个表的每个元组与第二个表的每个元组进行串接。
&emsp;&emsp; 自然连接 要求两个关系表中进行比较的必须是相同的属性列，无须添加连接条件，并且会在结果中消除重复的属性列。

### 自然连接和等值连接有何关系？
&emsp;&emsp; 自然连接，也是EQUI JOIN的一种，其结构使得具有相同名称的关联表的列将只出现一次。
&emsp;&emsp; 在连接条件中使用**等于号=**运算符比较被连接列的列值，但它使用选择列表指出查询结果集合中所包括的列，并删除连接表中的重复列：
> &emsp;&emsp; 所谓自然连接就是在等值连接的情况下，当连接属性X与Y具有相同属性组时，把在连接结果中重复的属性列去掉。
> &emsp;&emsp; 自然连接是在广义笛卡尔积R×S中选出同名属性上符合相等条件元组，再进行投影，去掉重复的同名属性，组成新的关系。
> 

### 1.2.2 如何得到 自然连接？
对于`Sales`表 和 `product`表，可通过如下sql获取自然连接：
```sql
select * from Sales natural join product;
```
执行结果如下：
```
| product_id | sale_id | year | quantity | price | product_name |
| ---------- | ------- | ---- | -------- | ----- | ------------ |
| 100        | 2       | 2009 | 12       | 5000  | Nokia        |
| 100        | 1       | 2008 | 10       | 5000  | Nokia        |
| 200        | 7       | 2011 | 15       | 9000  | Apple        |
```
可以看到的是

### 1.2.3 如果自然连接的两个表有多个属性名相同，会发生什么？
&emsp;&emsp; 如果自然连接的两个表有多个属性名相同，则只有在这些属性名的取值都相同时，自然连接才会将其进行串接。

### 1.2.4 join using
&emsp;&emsp; 为了发扬自然连接的优点，同时避免不正确的相等属性所带来的危险，SQL提供了一种自然连接的构造方式：`join ... using`子句，它允许你来制定究竟需要哪些列相等。
```sql
SELECT player_id, team_id, player_name, height, team_name 
    FROM player JOIN team USING(team_id)
```

## 连接条件
### `join on`里的`on`条件明明可以用 `where`子句来代替，那为什么还要引入`on`条件呢？



## 内连接
### 内连接 和 自然连接 有何异同？
&emsp;&emsp; 内连接基本与自然连接相同，它们的不同之处在于：
&emsp;&emsp; 第一，自然连接是对连接的表的同名属性列进行比较；而内连接则不要求两属性列同名，可以用`using`或`on`来指定连接条件。
&emsp;&emsp; 另外，对于两张表中列名相同的属性，自然连接只会返一次，而内连接会返回两次。例证如下：
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


## 外连接(outer join)
### 什么是外连接？



[SQL JOIN 语句详解](https://www.lixueduan.com/posts/mysql/02-join-detail/)




### 自然连接
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

### 若内连接（或外连接）不带连接条件，输出的结果将是？
无论是内连接还是外连接，如果不带连接条件，得到的结果都是笛卡尔积。下面用内连接举例：
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

### 外连接是不是必须带连接条件？
是的，外连接必须使用 `using`或`on`指定连接条件。不带条件、使用`where`都会报错：
```sql
SELECT * FROM user_profile  left JOIN question_practice_detail;
```
执行结果：
```
/* SQL错误（1064）：You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '' at line 1 */
```
使用`where`条件也是一样的：
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


### 内连接
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


### 外连接
#### 左连接
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


#### 右连接
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

#### 全连接
MySQL没有全连接，我们可以使用`UNION`来实现：
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



```sql

```
执行结果：