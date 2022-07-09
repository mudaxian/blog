---
title: mysql
date: 2022-07-07 14:29:17
tags:
- Mysql
- 数据库
categories:
- 数据库
- 数据库基本概念
---

# mysql

## 一、数据库概述

### 1.数据库模型

数据库按照数据结构来组织、存储和管理数据，实际上，数据库一共有三种模型：

- 层次模型
- 网状模型
- 关系模型

层次模型就是以“上下级”的层次关系来组织数据的一种方式，层次模型的数据结构看起来就像一颗树：

![image-20220707144155974](\img\image-20220707144155974.png) 

网状模型把每个数据节点和其他很多节点都连接起来，它的数据结构看起来就像很多城市之间的路网：

![image-20220707144741968](\img\image-20220707144741968.png) 

关系模型把数据看作是一个二维表格，任何数据都可以通过行号+列号来唯一确定，它的数据模型看起来就是一个Excel表：

![image-20220707144801825](\img\image-20220707144801825.png) 



### 2.主流关系数据库

目前，主流的关系数据库主要分为以下几类：

1. 商用数据库，例如：[Oracle](https://www.oracle.com/)，[SQL Server](https://www.microsoft.com/sql-server/)，[DB2](https://www.ibm.com/db2/)等；
2. 开源数据库，例如：[MySQL](https://www.mysql.com/)，[PostgreSQL](https://www.postgresql.org/)等；
3. 桌面数据库，以微软[Access](https://products.office.com/access)为代表，适合桌面应用程序使用；
4. 嵌入式数据库，以[Sqlite](https://sqlite.org/)为代表，适合手机应用和桌面程序。



总的来说，SQL语言定义了这么几种操作数据库的能力：

#### DDL：Data Definition Language

DDL语句：数据定义语言，这些语句定义了不同的数据段，数据库，表，列，索引等数据库对象。常用的语句关键字主要包括create,drop,alter等。

#### DML：Data Manipulation Language

DML语句：数据操纵语句，用于添加，删除，更新和查询数据库记录，并检查数据完整性。常用的语句关键字主要包括insert,delete,update等。

#### DQL：Data Query Language

DQL语句：`数据查询语言`，主要是对数据进行查询操作。常用关键字有 select、from、where等。

#### DCL：Data  Control  Language

DCL语句：数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库，表，字段，用户的访问权限和安全级别。主要的语句关键字包括grant,revoke等。



### 3.安装数据库

https://dev.mysql.com/downloads/mysql/



## 二、查询数据

### 1.基本查询

```sql
SELECT * FROM <表名>
```



### 2.条件查询

SELECT语句可以通过`WHERE`条件来设定查询条件，查询结果是满足查询条件的记录。

```sql
SELECT * FROM students WHERE score >= 80;
```

常用的条件表达式

| 条件                 | 表达式举例1     | 表达式举例2      | 说明                                              |
| :------------------- | :-------------- | :--------------- | :------------------------------------------------ |
| 使用=判断相等        | score = 80      | name = 'abc'     | 字符串需要用单引号括起来                          |
| 使用>判断大于        | score > 80      | name > 'abc'     | 字符串比较根据ASCII码，中文字符比较根据数据库设置 |
| 使用>=判断大于或相等 | score >= 80     | name >= 'abc'    |                                                   |
| 使用<判断小于        | score < 80      | name <= 'abc'    |                                                   |
| 使用<=判断小于或相等 | score <= 80     | name <= 'abc'    |                                                   |
| 使用<>判断不相等     | score <> 80     | name <> 'abc'    |                                                   |
| 使用LIKE判断相似     | name LIKE 'ab%' | name LIKE '%bc%' | %表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd' |



### 3.投影查询

如果我们只希望返回某些列的数据，而不是所有列的数据，我们可以用`SELECT 列1, 列2, 列3 FROM ...`，让结果集仅包含指定列。

```sql
SELECT id, score, name FROM students;
```



### 4.排序

如果我们要根据其他条件排序可以加上`ORDER BY`子句，默认从低到高

```sql
SELECT id, name, gender, score FROM students ORDER BY score;
```

如果要反过来，按照成绩从高到底排序，我们可以加上`DESC`表示“倒序”

```sql
SELECT id, name, gender, score FROM students ORDER BY score DESC;
```



### 5.分页查询

使用SELECT查询时，如果结果集数据量很大，比如几万行数据，放在一个页面显示的话数据量太大，不如分页显示，每次显示100条。

```sql
SELECT id, name, gender, score FROM students ORDER BY score DESC LIMIT 3 OFFSET 0;
```

上述查询`LIMIT 3 OFFSET 0`表示，对结果集从0号记录开始，最多取3条。注意SQL记录集的索引从0开始。

如果要查询第2页，那么我们只需要“跳过”头3条记录，也就是对结果集从3号记录开始查询，把`OFFSET`设定为3：

```sql
SELECT id, name, gender, score FROM students ORDER BY score DESC LIMIT 3 OFFSET 3;
```

分页查询的关键在于，首先要确定每页需要显示的结果数量`pageSize`（这里是3），然后根据当前页的索引`pageIndex`（从1开始），确定`LIMIT`和`OFFSET`应该设定的值：

- `LIMIT`总是设定为`pageSize`；
- `OFFSET`计算公式为`pageSize * (pageIndex - 1)`。



### 6.聚合查询

对于统计总数、平均数这类计算，SQL提供了专门的聚合函数，使用聚合函数进行查询，就是聚合查询，它可以快速获得结果。

```sql
SELECT COUNT(*) FROM students;
```

除了`COUNT()`函数外，SQL还提供了如下聚合函数：

| 函数 | 说明                                   |
| :--- | :------------------------------------- |
| SUM  | 计算某一列的合计值，该列必须为数值类型 |
| AVG  | 计算某一列的平均值，该列必须为数值类型 |
| MAX  | 计算某一列的最大值                     |
| MIN  | 计算某一列的最小值                     |

对于聚合查询，SQL还提供了“分组聚合”的功能。我们观察下面的聚合查询：

```sql
SELECT COUNT(*) num FROM students GROUP BY class_id;
```

执行这个查询，`COUNT()`的结果不再是一个，而是3个，这是因为，`GROUP BY`子句指定了按`class_id`分组，因此，执行该`SELECT`语句时，会把`class_id`相同的列先分组，再分别计算，因此，得到了3行结果。



### 7.多表连接查询

#### 7.1.交叉连接：笛卡尔积

SELECT查询不但可以从一张表查询数据，还可以从多张表同时查询数据。查询多张表的语法是：`SELECT * FROM <表1> <表2>`。

例如，同时从`students`表和`classes`表的“乘积”，即查询数据，可以这么写：

```sql
SELECT * FROM students, classes;
```

这种一次查询两个表的数据，查询的结果也是一个二维表，它是`students`表和`classes`表的“乘积”，即`students`表的每一行与`classes`表的每一行都两两拼在一起返回。结果集的列数是`students`表和`classes`表的列数之和，行数是`students`表和`classes`表的行数之积。

这种多表查询又称笛卡尔查询，使用笛卡尔查询时要非常小心，由于结果集是目标表的行数乘积，对两个各自有100行记录的表进行笛卡尔查询将返回1万条记录，对两个各自有1万行记录的表进行笛卡尔查询将返回1亿条记录。

注意，多表查询时，要使用`表名.列名`这样的方式来引用列和设置别名，这样就避免了结果集的列名重复问题。但是，用`表名.列名`这种方式列举两个表的所有列实在是很麻烦，所以SQL还允许给表设置一个别名，让我们在投影查询中引用起来稍微简洁一点：

```sql
SELECT s.id sid, s.name, s.gender, s.score, c.id cid, c.name cname FROM students s, classes c;
```

**重点记：**

   笛卡尔积：用的比较少，因为存在重复数据
   笛卡尔积：一个表的每条数据都和另一个表的所有数据匹配一次
   结    果： 一表 9 条 乘以 另一表 6 条 = 54 条

#### 7.2.内连接

内连接，取的就是两张表的交集

##### 隐式（无join）连接

```sql
select 字段 from 表A, 表B where 消除笛卡尔积的连接条件
```



##### 显式（有join）连接

```sql
select 字段* from 表A 别名 INNER(可以省略) JOIN 表B 别名 ON 消除笛卡尔积的连接条件
```



##### 等值连接

等值连接的最大的特点就是：条件是等量关系。

```sql
select 字段* from 表1 INNER(可以省略) JOIN 表2 ON 消除笛卡尔积的连接条件A=B
```



##### 非等值连接

非等值连接的最大的特点就是：条件不是是等量关系。

```sql
select 字段* from 表1 INNER(可以省略) JOIN 表2 ON 消除笛卡尔积的连接条件
```



##### 自连接

自连接的最大的特点：就是一张表看做两张表，自己连接自己

```sql
select s.*,st.teacher_id from student s,student st where s.id = st.teacher_id;
```



#### 7.3.外连接

外连接又分为左外连接、右外连接、全连接

左外连接（LEFT OUTER JOIN），简称左连接（LEFT JOIN）

右外连接（RIGHT OUTER JOIN），简称右连接（RIGHT JOIN）

全外连接（FULL OUTER JOIN），简称全连接（FULL JOIN）



##### 左外连接

左外连接：left join 或 left outrer join （outer可以省略）

左外连接：左边的是主表，左表数据全部显示，右表显示符合ON后的条件的数据，不符合的用NULL代替。

```sql
select * from student st left join teacher th on st.teacher_id = th.id;#（outer可以省略）
```



##### 右外连接

右外连接：right join 或 right outrer join （outer可以省略）

右外连接：右边边的是主表，右边表数据全部显示，左边表显示符合ON后的条件的数据，不符合的用NULL代替。

```sql
select * from student st right join teacher th on st.teacher_id = th.id;#（outer可以省略）
```



##### 全外连接

全外连接：full join 或 full outer join（outer可以省略），但Mysql不支持，可以使用union组合并去重实现。 

全外接查询：就是左表独有的数据加上右表独有的数据

```sql
select * from student st left join teacher th on st.teacher_id = th.id where th.id is null union;
select * from student st right join teacher th on st.teacher_id = th.id where st.teacher_id is null;
```



##### 全连接

全连接：full join 或 full outer join（outer可以省略），但Mysql不支持，可以使用union组合并去重实现。

全连接查询的是左表所有的数据加上右表所有的数据并去重。 

```sql
select * from student st left join teacher th on st.teacher_id = th.id union;
select * from student st right join teacher th on st.teacher_id = th.id;

```



总结：mysql存在七种连接，分别是内连接、左外连接、左外连接特殊情况、右外连接、右外连接特殊情况、全连接、全外连接。总结在一起就是内连接、左外连接、右外连接、全外连接。



## 三、修改数据

### 1.INSERT

当我们需要向数据库表中插入一条新记录时，就必须使用`INSERT`语句。

```sql
INSERT INTO students (class_id, name, gender, score) VALUES (2, '大牛', 'M', 80);
#查询并观察结果:
SELECT * FROM students;
```

在students表中插入students_two表的数据，要求两张表结构相同

```sql
INSERT INTO students SELECT * FROM students_two;
```



### 2.UPDATE

如果要更新数据库表中的记录，我们就必须使用`UPDATE`语句。

```sql
UPDATE students SET name='大牛', score=66 WHERE id=1;
#查询并观察结果:
SELECT * FROM students WHERE id=1;
```

要特别小心的是，`UPDATE`语句可以没有`WHERE`条件，例如：

```sql
UPDATE students SET score=60;
```

这时，整个表的所有记录都会被更新。所以，在执行`UPDATE`语句时要非常小心，最好先用`SELECT`语句来测试`WHERE`条件是否筛选出了期望的记录集，然后再用`UPDATE`更新。



### 3.DELETE

如果要删除数据库表中的记录，我们可以使用`DELETE`语句。

```sql
DELETE FROM students WHERE id=1;
#查询并观察结果:
SELECT * FROM students;
```

要特别小心的是，和`UPDATE`类似，不带`WHERE`条件的`DELETE`语句会删除整个表的数据：

```sql
DELETE FROM students;
```

这时，整个表的所有记录都会被删除。所以，在执行`DELETE`语句时也要非常小心，最好先用`SELECT`语句来测试`WHERE`条件是否筛选出了期望的记录集，然后再用`DELETE`删除。



## 四、管理Mysql

要管理MySQL，可以使用Navicat

下载及安装连接：

https://blog-chi-ebon.vercel.app/2022/07/08/navicat-install/