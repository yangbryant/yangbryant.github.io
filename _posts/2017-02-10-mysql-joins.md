---
layout: post
title: MySQL Joins的用法
date: 2017-02-10 13:12:00 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 数据库
---

***

* 本文介绍了如何使用 **MySQL JOINS** 语法.

* MySQL JOINS 用于从多个表中检索记录. 当在SQL语句中连接两个或者多个表时,就执行 MySQL JOIN.

* 不同类型的 MySQL 连接:
1. MySQL INNER JOIN (简单连接)
2. MySQL LEFT OUTER JOIN (LEFT 外连接)
3. MySQL RIGHT OUTER JOIN (RIGHT 外连接)
4. MySQL FULL OUTER JOIN (FULL 外连接)
5. MySQL CROSS JOIN (笛卡尔积)

***

* 注意: 本文的实例都依赖于如下的两个表:

```mysql
Table: Person
+----+-----------+----------+
| Id | FirstName | LastName |
+----+-----------+----------+
|  1 | Zhang     | San      |
|  2 | Li        | Si       |
|  3 | Wang      | Wu       |
+----+-----------+----------+

Table: Address
+--------+------+----------+-----------+
| AddrId | Id   | City     | Province  |
+--------+------+----------+-----------+
|      1 |    1 | Beijing  | Beijing   |
|      2 |    2 | Shanghai | Shanghai  |
|      5 |    5 | Shenzhen | Guangdong |
+--------+------+----------+-----------+
```

***

### INNER JOIN (简单连接)

* MySQL INNER JOINS 返回满足连接条件的多个表中的所有行.
* 句法:

```mysql
SELECT columns
FROM table1 
INNER JOIN table2
ON table1.column = table2.column;
```

* MySQL INNER JOINS 返回 table1 和 table2 阴影区域的记录.

![inner_join][inner_join]

* 实例分析

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
INNER JOIN Address
ON Person.Id = Address.Id;

// 执行结果如下:
+----+-----------+----------+----------+----------+
| Id | FirstName | LastName | City     | Province |
+----+-----------+----------+----------+----------+
|  1 | Zhang     | San      | Beijing  | Beijing  |
|  2 | Li        | Si       | Shanghai | Shanghai |
+----+-----------+----------+----------+----------+
```

* MySQL INNER JOIN 实例 返回 Person 和 Address 的所有行,其中在 Person 和 Address 中都有一个匹配的 Id 值.

* 旧语法: MySQL INNER JOIN 可以使用旧的隐式语法重写.

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person, Address
WHERE Person.Id = Address.Id;
```

***

### LEFT OUTER JOIN (LEFT 外连接)

* MySQL LEFT OUTER JOINS 返回在 ON 条件中指定的 LEFT-hand 表中所有行, 并且 **只返回** 满足连接条件的其他表中的行.
* 句法:

```mysql
SELECT columns
FROM table1
LEFT [OUTER] JOIN table2
ON table1.column = table2.column;
```

* MySQL LEFT OUTER JOINS 返回 table1 中的所有记录, 并且只返回与 table1 相交的 table2 中的记录.

![left_outer_join][left_outer_join]

* 实例分析

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
LEFT OUTER JOIN Address
ON Person.Id = Address.Id;

// 执行结果如下:
+----+-----------+----------+----------+----------+
| Id | FirstName | LastName | City     | Province |
+----+-----------+----------+----------+----------+
|  1 | Zhang     | San      | Beijing  | Beijing  |
|  2 | Li        | Si       | Shanghai | Shanghai |
|  3 | Wang      | Wu       | NULL     | NULL     |
+----+-----------+----------+----------+----------+
```

* MySQL LEFT OUTER JOIN 实例 返回 Person 表中的所有行, 并且仅返回 Address 表中联接字段相等的行.
* 如果 Person 表中的 Id 值不存在于 Address 表中,则 Address 表中的所有字段在结果集中显示为 <NULL> .

***

### RIGHT OUTER JOIN (RIGHT 外连接)

* MySQL RIGHT OUTER JOINS 返回在 ON 条件中指定的 RIGHT-hand 表中所有行, 并且 **只返回** 满足连接条件的其他表中的行.
* 句法:

```mysql
SELECT columns
FROM table1
RIGHT [OUTER] JOIN table2
ON table1.column = table2.column;
```

* MySQL RIGHT OUTER JOINS 返回 table2 中的所有记录, 并且只返回与 table2 相交的 table1 中的记录.

![right_outer_join][right_outer_join]

* 实例分析

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
RIGHT OUTER JOIN Address
ON Person.Id = Address.Id;

// 执行结果如下:
+------+-----------+----------+----------+-----------+
| Id   | FirstName | LastName | City     | Province  |
+------+-----------+----------+----------+-----------+
|    1 | Zhang     | San      | Beijing  | Beijing   |
|    2 | Li        | Si       | Shanghai | Shanghai  |
| NULL | NULL      | NULL     | Shenzhen | Guangdong |
+------+-----------+----------+----------+-----------+
```

* MySQL RIGHT OUTER JOIN 实例 返回 Address 表中的所有行, 并且仅返回 Person 表中联接字段相等的行.
* 如果 Address 表中的 Id 值不存在于 Person 表中,则 Person 表中的所有字段在结果集中显示为 <NULL> .

***

### FULL OUTER JOIN (FULL 外连接)

* MySQL FULL OUTER JOINS 得到 两个表 的所有记录. 如果在其他表中没有匹配,在没有匹配的字段结果显示为 <NULL> .
* 但是 **MySQL 不支持这种用法** , 我们只能用 **UNION** 来处理.
* 句法:

```mysql
SELECT columns
FROM table1
LEFT OUTER JOIN table2
ON table1.columns = table2.columns
UNION
SELECT columns
FROM table1
RIGHT OUTER JOIN table2
ON table1.columns = table2.columns;
```

*  MySQL FULL OUTER JOINS 返回 table1 和 table2 中的所有记录, 找不到的子段结果显示为 <NULL> .

![full_outer_join][full_outer_join]

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
LEFT OUTER JOIN Address
ON Person.Id = Address.Id
UNION
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
RIGHT OUTER JOIN Address
ON Person.Id = Address.Id;

// 执行结果如下:
+------+-----------+----------+----------+-----------+
| Id   | FirstName | LastName | City     | Province  |
+------+-----------+----------+----------+-----------+
|    1 | Zhang     | San      | Beijing  | Beijing   |
|    2 | Li        | Si       | Shanghai | Shanghai  |
|    3 | Wang      | Wu       | NULL     | NULL      |
| NULL | NULL      | NULL     | Shenzhen | Guangdong |
+------+-----------+----------+----------+-----------+
```

* 另一种情况: 如果你想重复出现结果,可以使用 `UNION ALL`.

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
LEFT OUTER JOIN Address
ON Person.Id = Address.Id
UNION ALL
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
RIGHT OUTER JOIN Address
ON Person.Id = Address.Id;

// 执行结果如下:
+------+-----------+----------+----------+-----------+
| Id   | FirstName | LastName | City     | Province  |
+------+-----------+----------+----------+-----------+
|    1 | Zhang     | San      | Beijing  | Beijing   |
|    2 | Li        | Si       | Shanghai | Shanghai  |
|    3 | Wang      | Wu       | NULL     | NULL      |
|    1 | Zhang     | San      | Beijing  | Beijing   |
|    2 | Li        | Si       | Shanghai | Shanghai  |
| NULL | NULL      | NULL     | Shenzhen | Guangdong |
+------+-----------+----------+----------+-----------+
```

***

### CROSS JOIN (笛卡尔积)

* CROSS JOINS 得到 两个表 的所有记录 做 **N\*M** 的组合.
* CROSS JOINS 不支持 **ON**语法. 但在 MySQL 中, CROSS JOINS 与 ON 语法使用,有容错处理, 相当于 **INTER JOIN**.
* 句法:

```mysql
SELECT columns
FROM table1
CROSS JOIN table2;
```

* 实例分析

```mysql
SELECT Person.Id, Person.FirstName, Person.LastName, Address.City, Address.Province
FROM Person
CROSS JOIN Address;

// 执行结果如下:
+----+-----------+----------+----------+-----------+
| Id | FirstName | LastName | City     | Province  |
+----+-----------+----------+----------+-----------+
|  1 | Zhang     | San      | Beijing  | Beijing   |
|  1 | Zhang     | San      | Shanghai | Shanghai  |
|  1 | Zhang     | San      | Shenzhen | Guangdong |
|  2 | Li        | Si       | Beijing  | Beijing   |
|  2 | Li        | Si       | Shanghai | Shanghai  |
|  2 | Li        | Si       | Shenzhen | Guangdong |
|  3 | Wang      | Wu       | Beijing  | Beijing   |
|  3 | Wang      | Wu       | Shanghai | Shanghai  |
|  3 | Wang      | Wu       | Shenzhen | Guangdong |
+----+-----------+----------+----------+-----------+
```

[inner_join]: /assets/images/mysql_join/inner_join.gif 'inner_join'
[left_outer_join]: /assets/images/mysql_join/left_outer_join.gif 'left_outer_join'
[right_outer_join]: /assets/images/mysql_join/right_outer_join.gif 'right_outer_join'
[full_outer_join]: /assets/images/mysql_join/full_outer_join.jpg 'full_outer_join'