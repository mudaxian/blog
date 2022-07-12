---
title: Mysql
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

下载及安装链接：

https://blog-chi-ebon.vercel.app/2022/07/08/navicat-install/



## 五、事务

### 1.概念及作用

MySQL 事务主要用于处理操作量大，复杂度高的数据。比如说，在人员管理系统中，你删除一个人员，你既需要删除人员的基本资料，也要删除和该人员相关的信息，如信箱，文章等等，这样，这些数据库操作语句就构成一个事务！

- 在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。
- 事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
- 事务用来管理 insert,update,delete 语句



### 2.条件

一般来说，事务是必须满足4个条件（ACID）：原子性（Atomicity，或称不可分割性）、一致性（Consistency）、隔离性（Isolation，又称独立性）、持久性（Durability）。

- 原子性：一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
- 一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
- 隔离性：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
- 持久性：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

注意：在 MySQL 命令行的默认设置下，事务都是自动提交的，即执行 SQL 语句后就会马上执行 COMMIT 操作。因此要显式地开启一个事务务须使用命令 BEGIN 或 START TRANSACTION，或者执行命令 SET AUTOCOMMIT=0，用来禁止使用当前会话的自动提交。



### 3.事务控制语句

- BEGIN 或 START TRANSACTION 显式地开启一个事务；
- COMMIT 也可以使用 COMMIT WORK，不过二者是等价的。COMMIT 会提交事务，并使已对数据库进行的所有修改成为永久性的；
- ROLLBACK 也可以使用 ROLLBACK WORK，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
- SAVEPOINT identifier，SAVEPOINT 允许在事务中创建一个保存点，一个事务中可以有多个 SAVEPOINT；
- RELEASE SAVEPOINT identifier 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
- ROLLBACK TO identifier 把事务回滚到标记点；
- SET TRANSACTION 用来设置事务的隔离级别。InnoDB 存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 和 SERIALIZABLE。



### 4.事务处理方法

MYSQL 事务处理主要有两种方法：

#### 用 BEGIN, ROLLBACK, COMMIT来实现

- BEGIN 开始一个事务
- ROLLBACK 事务回滚
- COMMIT 事务确认

#### 直接用 SET 来改变 MySQL 的自动提交模式:

- SET AUTOCOMMIT=0 禁止自动提交

- SET AUTOCOMMIT=1 开启自动提交

  

### 5.隔离级别

#### ReadUncommitted

读未提交是隔离级别最低的一种事务级别。在这种隔离级别下，一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读（Dirty Read）。

查看隔离级别

```sql
SELECT @@TX_ISOLATION;
```

设置该级别：

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```



#### ReadCommitted

在读已提交隔离级别下，一个事务可能会遇到不可重复读（Non Repeatable Read）的问题。

不可重复读是指，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致。

设置该级别：

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```



#### RepeatableRead

该级别为Mysql InnoDB默认隔离级别

在可重复读隔离级别下，一个事务可能会遇到幻读（Phantom Read）的问题。

幻读是指，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了。

设置该级别：

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```



#### Serializeble

序列化是最严格的隔离级别。在Serializable隔离级别下，所有事务按照次序依次执行，因此，脏读、不可重复读、幻读都不会出现。

Serializable隔离级别：一个个事务排成序列的形式。事务一个挨一个执行，等待前一个事务执行完，后面的事务才可以顺序执行。在Serializable级别下，不会使用MySQL的MVCC机制，而是在每一个SELECT请求下获得读锁，在每一个UPDATE操作下尝试获得写锁。如果没有特别重要的情景，一般都不会使用Serializable隔离级别。

设置该级别：

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZEBLE;
```

