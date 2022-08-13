---
title: MyBatis
date: 2022-07-12 14:55:40
tags:
- 框架
- 简化jdbc
categories:
---

# MyBatis

## 一、简介

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



## 二、入门

### 1.新建数据库

```sql
CREATE DATABASE `mybatis`;
use `mybatis`;
CREATE TABLE `user`(
	`id` INT(10) NOT NULL PRIMARY KEY,
    `name` VARCHAR(30) DEFAULT NULL,
    `pwd` VARCHAR(30) DEFAULT NULL
)ENGINE = INNODB DEFAULT CHARSET=utf8;
insert into user(`id`,`name`,`pwd`) values
(1,'mudaxian','123456'),
(2,'heierguo','111111'),
(3,'liusansi','555555');
```



### 2.创建maven项目导入依赖：

```xml
<!--mysql驱动-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>
<!--mybatis-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
<!--junit-->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
<!-- lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.24</version>
    <scope>provided</scope>
</dependency>

```



### 3.在resources文件下创建mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEnding=UTF-8&amp;serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/mudaxian/dao/UserMapping.xml"/>
    </mappers>
</configuration>
```

注意：每一个Mapping.xml都需要在该核心配置文件中注册



### 4.创建mybatis工具类

```java
package com.mudaxian.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    
    private static SqlSessionFactory sqlSessionFactory;
    
    static{
        try {
            String xml = "mybatis-config.xml";
            InputStream resourceAsStream = Resources.getResourceAsStream(xml);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}

```



### 5.在数据访问层接口下创建相应的xml

#### 创建pojo实体类，并使用@Data注解

```java
package com.mudaxian.pojo;
import lombok.Data;

@Data
public class User {
    private int id;
    private String name;
    private String pwd;
}
```



#### 创建数据访问层接口

```java
package com.mudaxian.dao;

import com.mudaxian.pojo.User;
import java.util.List;

public interface UserDao {
    List<User> getUserList();
}
```



#### 在接口同目录下创建UserMapping.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mudaxian.dao.UserDao">
    <select id="getUserList" resultType="com.mudaxian.pojo.User">
        select * from mybatis.user;
    </select>
</mapper>
```



### 6.使用junit测试

```java
package com.mudaxian.dao;

import com.mudaxian.pojo.User;
import com.mudaxian.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void test(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();
        for (User temp:userList){
            System.out.println(temp);
        }
        //关闭SqlSession
        sqlSession.close();
    }
}
```

最后输出结果

```java
User(id=1, name=mudaxian, pwd=123456)
User(id=2, name=heierguo, pwd=111111)
User(id=3, name=liusansi, pwd=555555)
```

入门测试成功

