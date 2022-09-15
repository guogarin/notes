- [第二高的薪水](#第二高的薪水)
  - [1.1 题目](#11-题目-1)
  - [1.2 解答](#12-解答-1)
    - [我的解答](#我的解答-1)
    - [他人的解答](#他人的解答-1)







&emsp;
&emsp;
&emsp;
## 1. 组合两个表
### 1.1 题目
表: `Person`
```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
personId 是该表的主键列。
该表包含一些人的 ID 和他们的姓和名的信息。
```
表: `Address`
```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
addressId 是该表的主键列。
该表的每一行都包含一个 ID = PersonId 的人的城市和州的信息。
```
&emsp;&emsp; 编写一个SQL查询来报告 `Person` 表中每个人的姓、名、城市和州。如果 `personId` `的地址不在 Address 表中，则报告为空`  `null` 。以任意顺序 返回结果表。查询结果格式如下所示。
示例 1:
```
输入: 
Person表:
+----------+----------+-----------+
| personId | lastName | firstName |
+----------+----------+-----------+
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |
+----------+----------+-----------+
Address表:
+-----------+----------+---------------+------------+
| addressId | personId | city          | state      |
+-----------+----------+---------------+------------+
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |
+-----------+----------+---------------+------------+
输出: 
+-----------+----------+---------------+----------+
| firstName | lastName | city          | state    |
+-----------+----------+---------------+----------+
| Allen     | Wang     | Null          | Null     |
| Bob       | Alice    | New York City | New York |
+-----------+----------+---------------+----------+
解释: 
地址表中没有 personId = 1 的地址，所以它们的城市和州返回 null。
addressId = 1 包含了 personId = 2 的地址信息。
```
## 1.2 解答
### 我的解答
```sql
select A.firstName, A.lastName, B.city, B.state from Person A left join Address B on A.PersonId = B.PersonId;
```
### 他人的解答
在进行连接的时候不需要通过表名来指定字段，因为数据库会为我们生成一个临时表：
```sql
select FirstName, LastName, City, State
    from Person left join Address
        on Person.PersonId = Address.PersonId;
```







&emsp;
&emsp;
&emsp;
# 第二高的薪水
## 1.1 题目
`Employee` 表：
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
id 是这个表的主键。
表的每一行包含员工的工资信息。
```
&emsp;&emsp; 编写一个 SQL 查询，获取并返回 `Employee` 表中第二高的薪水 。如果不存在第二高的薪水，查询应该返回 `null` 。查询结果如下例所示。
示例 1：
```
输入：
`Employee` 表：
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
输出：
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```
示例 2：
```
输入：
Employee 表：
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+
输出：
+---------------------+
| SecondHighestSalary |
+---------------------+
| null                |
+---------------------+
```
## 1.2 解答
### 我的解答
**第一次提交：**
```sql
SELECT DISTINCT salary 
    from Employee
        LIMIT 1, 1;
```
**第二次提交：**
```sql
SELECT DISTINCT salary AS SecondHighestSalary
    from Employee ORDER BY salary  DESC
        LIMIT 1, 1;
```
错误原因： 没有考虑到第二高的薪水不存在的情况。
**第三次提交：**
```sql
SELECT IFNULL((
    SELECT DISTINCT salary 
        from Employee ORDER BY salary  DESC
            LIMIT 1, 1), NULL
) AS SecondHighestSalary;
```
调试的时候遇到的问题：通过`IFNULL()`函数得到的结果需要 用`SELECT`来获取。

### 他人的解答
**方法一：使用子查询和 `LIMIT` 子句**
```sql

```

**方法二：使用 `IFNULL` 和 `LIMIT` 子句**
```sql

```


