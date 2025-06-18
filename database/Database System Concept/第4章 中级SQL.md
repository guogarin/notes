[toc]




&emsp;
&emsp;
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
|   id | name |  age |
| ---: | ---- | ---: |
|   38 | 张三 |   21 |
|   14 | 李四 |   19 |
|   43 | 王五 |   20 |
|   15 | 赵六 |   23 |
|   32 | 孙七 |   25 |
|   31 | 周八 |   28 |
|   21 | 吴九 |   28 |

`question_practice_detail`:
---
|   id | question_id | result |
| ---: | ----------: | ------ |
|   38 |         111 | wrong  |
|   14 |         112 | wrong  |
|   43 |         111 | right  |
|   15 |         115 | right  |
|   31 |         114 | right  |
|   32 |         113 | wrong  |
|    9 |         112 | right  |



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
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   38 |         111 | wrong  |
|   43 | 王五 |   20 |   38 |         111 | wrong  |
|   15 | 赵六 |   23 |   38 |         111 | wrong  |
|   32 | 孙七 |   25 |   38 |         111 | wrong  |
|   31 | 周八 |   28 |   38 |         111 | wrong  |
|   21 | 吴九 |   28 |   38 |         111 | wrong  |
|   38 | 张三 |   21 |   14 |         112 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   14 |         112 | wrong  |
|   15 | 赵六 |   23 |   14 |         112 | wrong  |
|   32 | 孙七 |   25 |   14 |         112 | wrong  |
|   31 | 周八 |   28 |   14 |         112 | wrong  |
|   21 | 吴九 |   28 |   14 |         112 | wrong  |
|   38 | 张三 |   21 |   43 |         111 | right  |
|   14 | 李四 |   19 |   43 |         111 | right  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   43 |         111 | right  |
|   32 | 孙七 |   25 |   43 |         111 | right  |
|   31 | 周八 |   28 |   43 |         111 | right  |
|   21 | 吴九 |   28 |   43 |         111 | right  |
|   38 | 张三 |   21 |   15 |         115 | right  |
|   14 | 李四 |   19 |   15 |         115 | right  |
|   43 | 王五 |   20 |   15 |         115 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   32 | 孙七 |   25 |   15 |         115 | right  |
|   31 | 周八 |   28 |   15 |         115 | right  |
|   21 | 吴九 |   28 |   15 |         115 | right  |
|   38 | 张三 |   21 |   31 |         114 | right  |
|   14 | 李四 |   19 |   31 |         114 | right  |
|   43 | 王五 |   20 |   31 |         114 | right  |
|   15 | 赵六 |   23 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   31 |         114 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   21 | 吴九 |   28 |   31 |         114 | right  |
|   38 | 张三 |   21 |   32 |         113 | wrong  |
|   14 | 李四 |   19 |   32 |         113 | wrong  |
|   43 | 王五 |   20 |   32 |         113 | wrong  |
|   15 | 赵六 |   23 |   32 |         113 | wrong  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |
|   31 | 周八 |   28 |   32 |         113 | wrong  |
|   21 | 吴九 |   28 |   32 |         113 | wrong  |
|   38 | 张三 |   21 |    9 |         112 | right  |
|   14 | 李四 |   19 |    9 |         112 | right  |
|   43 | 王五 |   20 |    9 |         112 | right  |
|   15 | 赵六 |   23 |    9 |         112 | right  |
|   32 | 孙七 |   25 |    9 |         112 | right  |
|   31 | 周八 |   28 |    9 |         112 | right  |
|   21 | 吴九 |   28 |    9 |         112 | right  |



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
|   id | name |  age | question_id | result |
| ---: | ---- | ---: | ----------: | ------ |
|   38 | 张三 |   21 |         111 | wrong  |
|   14 | 李四 |   19 |         112 | wrong  |
|   43 | 王五 |   20 |         111 | right  |
|   15 | 赵六 |   23 |         115 | right  |
|   31 | 周八 |   28 |         114 | right  |
|   32 | 孙七 |   25 |         113 | wrong  |

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
&emsp;&emsp; 第一，**自然连接**是对连接的表的同名属性列进行比较；而**内连接**则不要求两属性列同名，可以用`using`或`on`来指定连接条件。
&emsp;&emsp; 第二，对于两张表中列名相同的属性，**自然连接**只会返一次，而内连接会返回两次。例证如下：
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
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   38 |         111 | wrong  |
|   43 | 王五 |   20 |   38 |         111 | wrong  |
|   15 | 赵六 |   23 |   38 |         111 | wrong  |
|   32 | 孙七 |   25 |   38 |         111 | wrong  |
|   31 | 周八 |   28 |   38 |         111 | wrong  |
|   21 | 吴九 |   28 |   38 |         111 | wrong  |
|   38 | 张三 |   21 |   14 |         112 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   14 |         112 | wrong  |
|   15 | 赵六 |   23 |   14 |         112 | wrong  |
|   32 | 孙七 |   25 |   14 |         112 | wrong  |
|   31 | 周八 |   28 |   14 |         112 | wrong  |
|   21 | 吴九 |   28 |   14 |         112 | wrong  |
|   38 | 张三 |   21 |   43 |         111 | right  |
|   14 | 李四 |   19 |   43 |         111 | right  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   43 |         111 | right  |
|   32 | 孙七 |   25 |   43 |         111 | right  |
|   31 | 周八 |   28 |   43 |         111 | right  |
|   21 | 吴九 |   28 |   43 |         111 | right  |
|   38 | 张三 |   21 |   15 |         115 | right  |
|   14 | 李四 |   19 |   15 |         115 | right  |
|   43 | 王五 |   20 |   15 |         115 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   32 | 孙七 |   25 |   15 |         115 | right  |
|   31 | 周八 |   28 |   15 |         115 | right  |
|   21 | 吴九 |   28 |   15 |         115 | right  |
|   38 | 张三 |   21 |   31 |         114 | right  |
|   14 | 李四 |   19 |   31 |         114 | right  |
|   43 | 王五 |   20 |   31 |         114 | right  |
|   15 | 赵六 |   23 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   31 |         114 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   21 | 吴九 |   28 |   31 |         114 | right  |
|   38 | 张三 |   21 |   32 |         113 | wrong  |
|   14 | 李四 |   19 |   32 |         113 | wrong  |
|   43 | 王五 |   20 |   32 |         113 | wrong  |
|   15 | 赵六 |   23 |   32 |         113 | wrong  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |
|   31 | 周八 |   28 |   32 |         113 | wrong  |
|   21 | 吴九 |   28 |   32 |         113 | wrong  |
|   38 | 张三 |   21 |    9 |         112 | right  |
|   14 | 李四 |   19 |    9 |         112 | right  |
|   43 | 王五 |   20 |    9 |         112 | right  |
|   15 | 赵六 |   23 |    9 |         112 | right  |
|   32 | 孙七 |   25 |    9 |         112 | right  |
|   31 | 周八 |   28 |    9 |         112 | right  |
|   21 | 吴九 |   28 |    9 |         112 | right  |

**显然，内连接不带连接条件的话，得到的都是笛卡尔积。**

### 1.4.3 如何使用内连接？
<div align="center"> <img src="./pic/inner_join.gif"> </div>

&emsp;&emsp; 和自然连接不一样的是，内连接一般会用`on`或`using`指定连接条件（如果内连接不带连接条件，则得到的是两个表的笛卡尔积）：
```sql
SELECT * FROM user_profile  INNER JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |


&emsp;
## 1.5 外连接(outer join)
### 1.5.1 为什么需要外连接
&emsp;&emsp; 当我们想知道参赛者的答题情况时，可以通过对`user_profile`和`question_practice_detail`进行内连接：
```sql
SELECT * FROM user_profile  INNER JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |

但我们发现，用户`吴九`并没有出现在查询结果中，那是因为他压根就没答题，因此在内连接时，在`question_practice_detail`表中找不到对应的数据，所以不会出现在内连接的结果中。
&emsp;&emsp; 如果我们想让所有用户都出现在查询结果中（无论该用户是否答题），该怎么做呢？这是外连接就发挥作用了：
```sql
SELECT *
FROM user_profile
LEFT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |
|   21 | 吴九 |   28 | null |        null | null   |

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
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |
|   21 | 吴九 |   28 | null |        null | null   |


### 1.5.5 右连接
<div align="center"> <img src="./pic/right_join.gif"> </div>

```sql
SELECT *
FROM user_profile
RIGHT JOIN question_practice_detail ON question_practice_detail.id=user_profile.id;
```
执行结果：
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |
|   31 | 周八 |   28 |   31 |         114 | right  |
| null | null | null |    9 |         112 | right  |

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
|   id | name |  age |   id | question_id | result |
| ---: | ---- | ---: | ---: | ----------: | ------ |
|   38 | 张三 |   21 |   38 |         111 | wrong  |
|   14 | 李四 |   19 |   14 |         112 | wrong  |
|   43 | 王五 |   20 |   43 |         111 | right  |
|   15 | 赵六 |   23 |   15 |         115 | right  |
|   31 | 周八 |   28 |   31 |         114 | right  |
|   32 | 孙七 |   25 |   32 |         113 | wrong  |
|   21 | 吴九 |   28 | null |        null | null   |
| null | null | null |    9 |         112 | right  |

###  1.5.7 `left outer join`和`left join`有何区别？
&emsp;&emsp; left join`是`left outer join`的缩写，关键字`OUTER`是可选择的，取决于具体语言，在实现上它们都是遵循标准的。

### 1.5.8 如何理解外连接中的 左 和 右？
&emsp;&emsp; 左连接与右连接的 **左右** 指的是以两张表中的哪一张为基准表，它们都是外连接。


&emsp;
## 1.6 关于连接的一些问题
### 1.6.1 当`join`前面没有任何关键字修饰时，使用的表连接方式是什么？（表的默认连接是什么？）
&emsp;&emsp; `join`是内连接，也就是说`INNER JOIN`中的`INNER`是可以省略的。

### 
<div align="center"> <img src="./pic/joins.png"> </div>
<center> <font color=black> <b> 4种连接的7种用法 </b> </font> </center>

### 1.6.2 在对多表进行连接时，后台发生了什么？
&emsp;&emsp; 在连接两张(或多张)来时，数据库会生成一张中间的临时表，然后再将这张临时表返回给用户。
&emsp;&emsp; 连接的结果可以在逻辑上看作是由`SELECT`语句指定的列组成的新表。

### 1.6.3 在`left join`(`right join`)时，`on`和`where`各自起什么作用？
在使用 `join` 时，`on` 和 `where` 条件的区别如下：
> &emsp;&emsp; `on` 条件是在生成临时表时使用的条件，它不管 `on` 中的条件是否为真，都会返回左边表(若是`right join`则返回的是右边)中的记录。
> &emsp;&emsp; `where` 条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有 left join 的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。
> 
另外，`on`是外连接声明的一部分，但`where`却不是。

### 1.6.4 `join on`里的`on`条件明明可以用 `where`子句来代替，那为什么还要引入`on`条件呢？
&emsp;&emsp; **ON条件** 是过滤两个链接表笛卡尔积形成中间表的约束条件。
&emsp;&emsp; **WHERE条件** 在有ON条件的SELECT语句中是过滤中间表的约束条件。在没有ON的单表查询中，是限制物理表或者中间查询结果返回记录的约束。在两表或多表连接中是限制连接形成最终中间表的返回结果的约束。
&emsp;&emsp;  从这里可以看出，将WHERE条件移入ON后面是不恰当的。推荐的做法是：
> ON只进行连接操作，WHERE只过滤中间表的记录。
> 
&emsp;&emsp; 另外，在ON子句中指定连接条件，并在WHERE子句中出现其余的条件，这样的SQL可读性更强。





&emsp;
&emsp;
# 2. 视图
## 2.1 什么是视图？
&emsp;&emsp; 视图是指计算机数据库中的视图，是一个虚拟表，其内容由查询定义。同真实的表一样，视图包含一系列带有名称的列和行数据。但是，视图并不在数据库中以存储的数据值集形式存在。行和列数据来自由定义视图的查询所引用的表，并且在引用视图时动态生成。
&emsp;&emsp; 视图可以理解为一张表或多张表的预计算，这些表称为**基表**。

## 2.2 视图的应用场景
**(1) 安全考虑**
> ① 通过视图可以将实体表隐藏起来，让外部程序无法得知实体表的真实结构，降低数据库被攻击的风险。
> ② 在多数的情况下，视图表是只读的，外部程序无法直接透过视图表修改资料（当然，具备更新能力的视图表除外）。
> 
**(2) 简化查询**
> 通过视图可以提前将高度复杂的查询写好包装在视图表中，程序员只需要直接访问该视图表即可取出需要的数据，无需再自己写SQL。
> 

## 2.3 如何使用视图
```sql
CREATE VIEW v_myView
AS
   SELECT * FROM myTable
```
即可建立一个视图表，而外部程序可以用下列指令来访问视图表：
```sql
SELECT * FROM v_myView WHERE myID = 3982;
```
若要删除视图表，则可以用`DROP VIEW v_myView`来删除。

## 2.4 视图是如何保存的？它里面包含数据吗？
&emsp;&emsp; 视图不进行预计算和存储，相反，数据库系统存储的是与视图相关联的查询表达式。
&emsp;&emsp; 视图不存储数据。每当视图被访问时，其中的数据就通过计算查询结果而被创建出来。

## 2.5 视图可以`join`吗？
&emsp;&emsp; 在**查询**中，视图可以出现在实体表可以出现的任何位置，因此毫无疑问，视图可以`join`。

## 2.6 视图 和 `WITH`子句 有何不同？
&emsp;&emsp; 视图和`WITH`子句的不同之处在于：视图一旦创建，在它被显示删除之前就一直是可用的；但通过`WITH`子句定义的命名子查询只在定义它的查询中可用。

## 2.7 通过视图更新数据
### 2.7.1 可以视图更新数据吗？
&emsp;&emsp; 可以通过视图修改基表的数据，但是这些操作将直接反应在基表中。如果视图是基于一个基础表产生的，直接进行INSERT,UPDATE,DELETE操作即可；若视图是基于多张表的，则情况要复杂一些。

### 2.7.2 是否建议通过视图更新数据？
&emsp;&emsp; 不建议。

## 2.8 物化视图(materialized view)
### 2.8.1 何为物化视图？
&emsp;&emsp; 有些数据库系统允许存储视图关系，但是该数据库保证：如果用于定义视图的表发生改变，则视图也会跟着修改以保持最新。这样的视图被称为**物化视图(materialized view)**

### 2.8.2 普通视图和物化视图什么区别？
&emsp;&emsp; 对于普通视图，真实数据是保存在基表中，普通视图里不保存物理数据，他的数据都是实时查询计算得到的。而物化视图是保存有真实数据的。它们有如下区别：
> **① 存储方式** 物化视图会存储实际的数据，而普通视图只是保存SQL定义，不会存储实际的数据。
> 
> **② 更新机制** 物化视图在基表数据发生变化时需要更新以保持数据的一致性，而普通视图在查询时只是执行其定义的SQL语句来获取数据。
> 
> **③ 性能差异** 由于物化视图存储了实际的数据，因此在查询时可以直接返回结果而无需重新计算，从而提高了查询性能。而普通视图在查询时需要执行其定义的SQL语句来获取数据，性能相对较差。
> 
> **④ 能否更新基表** 物化视图不可以进行更新，删除，修改等操作，只能够查询，而普通视图可以更。
> 

### 物化视图 一般用在什么场合？
**① 数据仓库**
>在数据仓库中，经常需要对大量数据进行复杂的查询和分析。使用物化视图可以预先计算和存储这些查询的结果，从而提高查询性能。
> 
**② 实时数据分析**
>对于需要实时获取数据分析结果的应用场景，物化视图可以确保数据的实时性和准确性，同时提供快速的查询响应。
> 
**③ 大数据处理**
>在处理大数据时，物化视图可以作为一种缓存机制，将部分计算结果存储起来，以便在后续查询中重复使用，从而降低计算资源的消耗。
> 
**④ 复杂的表连接和聚合操作**
>对于涉及多个表连接和聚合操作的查询，物化视图可以将这些耗时的操作预先计算并存储起来，从而避免在每次查询时都重新执行这些操作。
> 

### 物化视图有什么特点？
**① 提高查询性能**
> 物化视图预先计算并存储查询结果，当查询请求到达时，可以直接返回结果，而无需重新计算，从而提高查询性能。
> 
**② 减少查询开销**
> 由于物化视图存储了查询结果，因此在查询时可以减少计算资源的消耗，降低查询开销。
> 
**③ 支持离线查询**
> 物化视图可以在离线模式下使用，即使数据库不可用，也可以使用物化视图作为备份进行查询。
> 

### 物化视图的类型
在Oracle中，有`ON DEMAND`、`ON COMMIT`两种类型，两者的区别在于刷新方法的不同：
> `ON DEMAND`顾名思义，仅在该物化视图“需要”被刷新了，才进行刷新(REFRESH)，即更新物化视图，以保证和基表数据的一致性；
> 而ON COMMIT`是说，一旦基表有了COMMIT，即事务提交，则立刻刷新，立刻更新物化视图，使得数据和基表一致。
> 




&emsp;
&emsp;
# 3. 事物(transaction)
## 3.1 什么是事物？
&emsp;&emsp; 事务由 查询 和(或) 更新语句 的序列组成。

## 3.2 事物什么时候开始？什么时候结束？
&emsp;&emsp; SQL标准规定，当一条SQL语句被执行时，就隐式的开始了一个事物。
&emsp;&emsp; 在 提交 或 回滚 当前事物时，该事务终止。

## 3.3 可以回滚已提交的事务吗？
&emsp;&emsp; 不能，一旦一个事务被提交，则不能通过回滚操作来撤销。

## 3.4 举个例子说明事务的使用场景
&emsp;&emsp; 考虑一个银行的转账业务，我们需要从一个银行账户转到该银行的另一个账户。为了完成这项任务，我们需要更新两个账户的余额，先从转出账户扣除转账金额，然后加到转入账户中。如果在从转出账户扣除转账金额后，在把金额加到转入账户前系统奔溃了。那么银行就会发生余额不一致的情况。利用事务则可以避免出现这样的问题。

## 3.5 事务是否可以分割？
&emsp;&emsp; 不可以，事务具有原子性。

## 3.6 如果在事务执行完毕后，程序员忘记commit了，会发生什么？
&emsp;&emsp; 更新要么被自动提交，要么被回滚，具体看数据库的实现。




&emsp;
&emsp;
# 4. 完整性约束(integrity constraint)
## 4.1 完整性约束的作用是？有哪些完整性约束？
&emsp;&emsp; 完整性约束保证授权用户对数据库所做的修改不会导致数据一致性的丢失。

## 4.2 非空约束
&emsp;&emsp; 非空约束禁止对该属性插入空值。

## 4.3 唯一性约束
&emsp;&emsp; 顾名思义，唯一性约束要求
### (1) 如何定义唯一约束？
**① 使用单列唯一键**
```sql
CREATE TABLE users (
    user_id NUMBER PRIMARY KEY,
    username VARCHAR2(50) NOT NULL,
    email VARCHAR2(100) UNIQUE,
    password VARCHAR2(50) NOT NULL
);
```
**② 使用多列唯一键**
```sql
CREATE TABLE orders (
    order_id NUMBER PRIMARY KEY,
    customer_id NUMBER,
    product_id NUMBER,
    order_date DATE,
    CONSTRAINT unique_order UNIQUE (customer_id, product_id)
);
```

### (2) 唯一性约束和主键有何区别？
&emsp;&emsp; 主键不可为空(`NULL`)，而唯一键列可以包含空值(但只能有一个空值)。
&emsp;&emsp; 在一个表中，主键只能有一个，唯一约束却可以有多个。
&emsp;&emsp; 主键可以做外键，唯一键不行。

## 4.4 check约束(check constraint)
### (1) check约束的作用是？
&emsp;&emsp; CHECK 约束用于限制列中的值的范围。

### (2) 如何定义check约束？
&emsp;&emsp; check约束可以单独出现，也可以作为字段声明的一部分。
**① check约束作为声明的一部分**
```sql
CREATE TABLE Persons
(
  P_Id int NOT NULL CHECK (P_Id>0),
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255)
);
```
**② check约束单独出现**
```sql
CREATE TABLE section
(
  course_id varchar(8),
  sec_id varchar(8),
  semester varchar(6),
  Year numeric (4,0),
  building varchar(15),
  room_number varchar(7),
  time_slot_id varchar(4),
  PRIMARY key(course_id, sec_id, semester, year),
  CHECK (semester in('Spring', 'Summer', 'Fall', 'Winter'))
);
```
上表模拟了一个枚举类型，指定`semester`必须是`'Spring', 'Summer', 'Fall', 'Winter')`中的一个。这样check约束就能对字段的取值加以控制，毕竟地球只有四个季节。

#### (3) 如果check约束的字段被插入空值会如何？
&emsp;&emsp; CHECK约束不接受计算结果为 `FALSE` 的值，如果插入的值不会导致check条件为假，则能成功插入。 而空值的计算结果为 `UNKNOWN`，所以NULL可以被成功插入。 例如：
> 假设对`age`字段要求插入的值必须大于零，如果我们插入NULL到`age`字段中，是可以成功插入到。
> 
如果我们需要保证某个字段不为空，则我们应该使用非空约束。


## 4.5 引用完整性(Referential Integrity)
&emsp;&emsp; 引用完整性又称参照完整性，引用完整性指 被引用表中的主键字 和 引用表中的外键字段之间的关系。

## 4.6 给约束命名
### (1) 给约束命名有何优点？
&emsp;&emsp; 当你需要修改或删除某个约束时，可以快速的对其进行修改。如果约束的名称确实，则我们需要想办法识别出待修改的约束的名字，比如Oracle的约束都保存在`  user_constraints`表中。





&emsp;
&emsp;
# 5. SQL的数据类型与模式
## 5.1 日期和时间类型
### (1) 有哪些时间日期类型？
① `date` 日期
② `time` 时间
③ `datetime` 时间日期
④ `timestamp` 时间戳

### (2) `datetime` 和 `timestamp` 有何异同？
#### 相同点
① 两个数据类型存储时间的格式一致。均为 `YYYY-MM-DD HH:MM:SS`
② 两个数据类型都包含「日期」和「时间」部分。
③ 两个数据类型都可以存储微秒的小数秒（秒后6位小数秒）

#### 区别
**① 占用空间**
TIMESTAMP ：占 `4` 个字节（小数秒+3 个字节）
DATETIME：在 `MySQL 5.6.4` 之前，占 `8` 个字节 ，之后版本，占 `5` 个字节。（小数秒+3 个字节）
| 类型      | 占据字节  | 表示形式            |
| --------- | --------- | ------------------- |
| timestamp | 4 字节    | yyyy-mm-dd hh:mm:ss |
| datetime  | 8(5) 字节 | yyyy-mm-dd hh:mm:ss |

**②  表示范围**
| 类型      | 表示范围                                                     |
| --------- | ------------------------------------------------------------ |
| datetime  | '1000-01-01 00:00:00.000000' to '9999-12-31 23:59:59.999999' |
| timestamp | '1970-01-01 00:00:01.000000' to '2038-01-19 03:14:07.999999' |
z§
&emsp;&emsp; timestamp翻译为汉语即"时间戳"，它是当前时间到 Unix元年(1970年1月1日0时0分0 秒)的秒数。对于某些时间的计算，如果是以 datetime 的形式会比较困难，假如我是 1994-1-20 06:06:06 出生，现在的时间是 2016-10-1 20:04:50 ，那么要计算我活了多少秒钟用 datetime 还需要函数进行转换，但是 timestamp 直接相减就行。

**③ 时区(存入时间是否会自动转换？)**
&emsp;&emsp; `timestamp` 只占 `4` 个字节，而且是以`utc`的格式储存， 它会自动检索当前时区并进行转换。
&emsp;&emsp; `datetime`以 `8` 个字节储存，不会进行时区的检索.
&emsp;&emsp; 也就是说，对于timestamp来说，如果储存时的时区和检索时的时区不一样，那么拿出来的数据也不一样。对于datetime来说，存什么拿到的就是什么。

**④使用 `now()` 存储当前时间时，保存的实际值，是否与当前计算机时间一致？**
`TIMESTAMP`：可能不一致。存储值会被转换成 UTC 时间值再存入数据库。
`DATETIME`：与当前时间是一致的。

**⑤ 如果存入的是 `NULL` 时，两个类型如何存储？**
`TIMESTAMP`：会自动存储当前时间（ `now()` ）。
`DATETIME`：不会自动存储当前时间，会直接存入 `NULL` 值。

### (3) SQL中时间日期相关的函数
#### ① EXTRACT()
&emsp;&emsp; EXTRACT() 函数用于返回日期/时间的单独部分，比如年、月、日、小时、分钟等等。语法如下：
```sql
EXTRACT(field FROM datetime)
```
其中`field`可以是`MICROSECOND, SECOND, MINUTE, HOUR, DAY, WEEK, MONTH`等等，例如有`"Orders"`表：

| OrderId | ProductName      | OrderDate               |
| ------- | ---------------- | ----------------------- |
| 1       | Jarlsberg Cheese | 2008-11-11 13:23:44.657 |

下面是 SELECT 语句：
```sql
SELECT  
    EXTRACT(YEAR FROM OrderDate) AS OrderYear,
    EXTRACT(MONTH FROM OrderDate) AS OrderMonth,
    EXTRACT(DAY FROM OrderDate) AS OrderDay
FROM 
    Orders
WHERE 
    OrderId=1
```
结果如下所示：
| OrderYear | OrderMonth | OrderDay |
| --------- | ---------- | -------- |
| 2008      | 11         | 11       |

#### ② 获取当前时间日期的函数
```sql
SELECT CURRENT_DATE(), CURRENT_TIME(), CURRENT_TIMESTAMP(), LOCALTIME(), LOCALTIMESTAMP();
```
运行结果：
| CURRENT_DATE() | CURRENT_TIME() | CURRENT_TIMESTAMP() | LOCALTIME()         | LOCALTIMESTAMP()    |
| -------------- | -------------- | ------------------- | ------------------- | ------------------- |
| 2024-12-18     | 14:39:59       | 2024-12-18 14:39:59 | 2024-12-18 14:39:59 | 2024-12-18 14:39:59 |


## 5.2 类型转换 和 格式化函数
&emsp;&emsp; 虽然系统会自动执行某些数据类型的转换，但其它的转换需要显示请求。

## 5.3 缺省值(DEFAULT 约束)
&emsp;&emsp; 缺省值 就是 DEFAULT 约束，DEFAULT 约束会在该字段没有提供值的时候给它插入一个默认值。
```sql
CREATE TABLE Persons
(
    P_Id int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255) DEFAULT 'Sandnes'
)
```

## 5.4 大对象类型
&emsp;&emsp; MySQL提供了`blob`(binary large object)类型来存储**二进制大对象**，`text`类型存储长文本字符串。

## 5.5 用户自定义类型

## 5.6 生成唯一码
### 5.6.1 什么是唯一码？为什么需要它？
&emsp;&emsp; 唯一码 是用来唯一标识一个信息的一个编码。
&emsp;&emsp; 假如有一个员工表`Employee`，每个员工都需要一个员工号来标识该用户，此时就可以用到唯一码。

### 5.6.2 如何获得唯一码？
&emsp;&emsp; 不同的数据库的的实现可能都不一样，MySQL用的是`auto_increment`，SQL Server用的是`identify`。

## 5.7 create table 的扩展
### 5.7.1 只复制表结果
&emsp;&emsp; 应用常常要求创建与现有的某个表的模式相同的表。SQL提供了一个`create table like`的扩展来支持这项任务:
以下语句使用的是MySQL8进行验证：
```sql
create table temp_versions like versions;
```
但需要注意的是，上面的建表语句 **不会复制数据！**。
### 5.7.2 同时复制数据和表结构
若想复制数据，则应该使用`CREATE TABLE 新表名 AS ...`：
```sql
CREATE TABLE temp_versions2  as
	(SELECT * FROM versions);
```
或者只复制部分数据：
```sql
CREATE TABLE temp_versions3  as
	(SELECT * FROM versions WHERE start_time > '2021-10-20 08:00:00');
```
**此功能在不同的数据库中语法可能不一样，有的可能需要在后面加上`WITH DATA`**

# 5 catalog 和 schema
## 5.1 什么是 catalog 和 schema
&emsp;&emsp; 在关系型数据库中，分三级：`database.schema.table`。即一个数据库下面可以包含多个`schema`，一个`schema`下可以包含多个数据库对象，比如表、存储过程、触发器等。但并非所有数据库都实现了`schema`这一层，比如: 
> `mysql`直接把`schema`和`database`等效了，也就是说在`mysql`中，`create database` 和 `create schema` 是等效的；
> 另外，`PostgreSQL`、`Oracle`、`SQL server`等的`schema`也含义不太相同。
> 
&emsp;&emsp; 所以说，关系型数据库中没有`catalog`的概念。但在一些其它地方（特别是大数据领域的一些组件）有`catalog`的概念，也是用来做层级划分的，一般是这样的层级关系：`catalog.database.table`。
&emsp;&emsp; 实际上，`schema`就是数据库对象的集合，这个集合包含了各种对象如：表、视图、存储过程、索引等。
&emsp;&emsp; 如果把`database`看作是一个仓库，仓库很多房间（`schema`），一个`schema`代表一个房间，`table`可以看作是每个房间中的储物柜，`user`是每个`schema`的主人，有操作数据库中每个房间的权利，就是说每个数据库映射`user`有每个`schema`（房间）的钥匙。



&emsp;
&emsp;
# 7. 授权
## 7.0 权限的分类
对**数据**的授权包括
> ① 读取数据的权限
> ② 插入数据的权限
> ③ 更新数据的权限
> ④ 删除数据的权限
> 
以上几种权限(privilege)可以部分授予，也可以完全不授予。
另外，用户还可以被授予数据库模式层面的权限：
> ① 创建表
> ② 修改表
> ③ 删除表
> 
最高权限属于**管理员用户**，也就是 管理员权限

## 7.1 权限的授予与回收
### 授予权限
```sql
grant <权限列表>
on <表名/视图名>
to <用户>;
```
常用权限：
> `all privileges`
> `create`、`drop`
> `select`、`insert`、`delete`、`update`
> 

### 回收权限
```sql
revoke <权限列表>
on <表名/视图名>
from <用户>;
```

[一文看尽MySQL用户权限管理](https://cloud.tencent.com/developer/article/1656008)

## 7.2 角色(role)
### 7.2.1 什么是角色？它的主要作用是？
&emsp;&emsp; 角色(role)是 一组权限(privilege) 的集合。
&emsp;&emsp; 角色 可以方便管理员对用户的权限进行统一管理，通过角色，可以将一组具有相同权限的用户组织在一起。
&emsp;&emsp; 例如在一个校园管理系统中，可以通过角色来进行权限管理，可以通过创建 “教师”、“学生”等角色，对用户进行统一赋权，简化权限管理。

### 7.2.2 如何使用角色？
**(1) 创建角色**
```sql
-- 创建角色 系主任、教师 和 学生
CREATE ROLE 'dean', 'teacher', 'student';
```
**(2) 给角色赋权**
```sql
-- 将 scores表的查询权限赋给 学生（角色）
grant select
on scores
to student;
```
**(3) 将角色授予用户**
```sql
-- 将学生角色的权限赋给 用户zhangsan
grant student
to zhangsan;
```
一个角色可以赋给另一个角色：
```sql
-- 将教师角色的权限赋给 系主任（因为系主任也是老师，而且比一般的老师权限高）
grant teacher
to dean;
```

## 行级授权



```sql

```
执行结果：