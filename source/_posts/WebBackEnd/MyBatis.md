---
title: MyBatis
tags:
  - MyBatis
  - Java
categories:
  - [MyBatis]
date: 2020-07-10 13:07:31

---



参考《Java EE互联网轻量级框架整合开发》、MyBatis官方文档

---

## MyBatis概述

### 什么是MyBatis

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

MyBatis 的前身是 Apache 的开源项目iBatis，是一个基于 Java 的持久层框架。MyBatis 的优势在于灵活，它几乎可以代替 JDBC ，同时提供了接口编程。目前 MyBatis 的数据访问层 DAO (Data Access Objects）是不需要实现类的，它只需要一个接口和 XML（或者注解）。MyBatis 提供自动映射、动态 SQL、级联、缓存、注解、代码和 SQL 分离等特性，使用方便，同时也可以对 SQL 进行优化。因为其具有封装少、映射多样化、支持存储过程、可以进行 SQL 优化等特点，使得它取代了 Hibernate 成为了 Java 互联网中首选的持久框架。

Hibernate 作为一种十分流行的框架，它有其无可替代的优势，这里我们有必要讨论一下它和 MyBatis 的区别。由于 MyBatis 和 Hibernate 都是持久层框架，都会涉及数据库，所以首先定义一个数据库表一一角色表（t_role）

```
ID-->int(12)
roleName-->varchar(60)
note-->varchar(1024)
```

根据这个角色表，我们可以用一个 POJO (Plain Ordinary Java Object ）和这张表定义的字段对应起来

```java
package com.learn.chapterl.pojo;
public class Role implements java.io.Serializable{
    private Integer id;
    private String roleName;
    private String note;
    /** setter and getter **/
}
```

无论是 MyBatis 还是 Hibernate 都是依靠某种方法，将数据库的表和 POJO 映射起来的，这样程序员就可以操作 POJO 来完成相关的逻辑了。

### Hibernate 简介

要将POJO和数据库映射起来需要给这些框架提供映射规则 ，所以下一步要提供映射的规则。

在 MyBatis 或者 Hibernate 中可以通过 XML 或者注解提供映射规则， 这里讨论的是XML方式，因为在 MyBatis 中注解方式会受到一定的限制，所以 MyBatis 通常使用 XML方式实现映射关系。

我们把 POJO 对象和数据库表相互映射的框架称为对象关系映射（ Object Relational Mapping, ORM，或O/RM，或 O/R mapping ）框架。无论 MyBatis 或者 Hibernate 都可以称为 ORM 框架 ，只是 Hibernate 的设计理念是完全面向 POJO 的，而 MyBatis 则不是。Hibernate 基本不再需要编写 SQL 就可以通过映射关系来操作数据库，是一种全表映射的体现：而 MyBatis 则不同，它需要我们提供 SQL 去运行。

Hibernate 是将 POJO 和数据库表对应的映射文件

```xml
<hibernate-mapping> 
    <class name=”com.learn.chapterl.pojo.Role” table="t_role"> 
        <id name=”id” type=”java.lang.Integer”>
            <column name=”id”/>
            <generator class="identity" /> 
        </id> 
        <property name＝”roleName” type＝”string” >
            <column name="role_name" length="60" not-null="true"/>
        </property> 
        <property name="note" type="string">
            <column name="note" length="512"/>
        </property> 
    </class> 
</hibernate-mapping>
```

首先，对 POJO 和表 t_role 进行了映射配置，把两者映射起来了。然后，对 POJO 进行操作，从而影响t_role 表的数据，比如对其增、删、查、改

```java
//Hibernate 通过 Session 操作数据库数据
Session session = null;
Transaction tx = null;
try {
    //打开 Session
    session = HibernateUtil.getSessionFactory().openSession();
    //事务
    tx = session.beginTransaction();
    //POJO
    Role role = new Role();
    role.setId(1);
    rold.setRoleName("rolename1");
    role.setNote("note1");
    session.save(role);//保存
    Role role2 = (Role) session.get(Role.class, 1);//查询
    role2.setNote("修改备注");
    session.updata(role2);//更新
    System.err.println(role2.getRoleName());
    session.delete(role2);//删除
    tx.commit();//提交事务
} catch (Exception ex){
    if (tx != null && tx.isActive()){
        tx.rollback()//回滚事务
    }
    ex.printStackTrace();
} finally {
    if (session != null && session.isOpen()){
        session.close();
    }
}
```

这里我们没有看到 SQL ，那是因为 Hibernate 会根据映射关系来生成对应的 SQL，程序员不用精通 SQL，只要懂得操作 POJO 就能够操作对应数据库的表了。这在管理系统时代是十分有利的 。因为对于管理系统而言，首先在于实现业务逻辑，然后才是性能，所以 Hibernate 成为了那个时代的主流持久框架。

### MyBatis简介

在移动互联网时代，MyBatis 成为了目前互联网 Java 持久框架的首选，与 Hibernate 消除 SQL 不同，MyBatis 不屏蔽 SQL。不屏蔽 SQL 的优势在于，程序员可以自己制定 SQL规则，无须 Hibernate 自动生成规则，这样能够更加精确地定义 SQL ，从而优化性能 。它更符合移动互联网高并发、大数据、高性能 、 高响应的要求。

与 Hibernate 一样 ，MyBatis 也需要一个映射文件把 POJO 和数据库的表对应起来。

```xml
<mapper namespace="com.learn.chapterl.mapper.RoleMapper">
    <resultMap id="roleMap" type="com.learn.chapterl.pojo.Role">
        <id property="id" column="id"/>
        <result property="roleName" column="role_name"/>
        <result property="note" column="note"/>
    </resultMap> 
    
    <select id="getRole" resultMap="roleMap">
        select id, role_name, note from t_role where id = #{id}
    </select> 
    
    <delete id ="deleteRole" parameterType="int">
    	delete from t_role where id = #{id} 
    </delete> 
    
    <insert id ="insertRole" parameterType="com.learn.chapterl.pojo.Role">
    	insert into t_role(role_name, note) values(#{roleName}, #{note}) 
    </insert> 
    
    <update id="updateRole" parameterType="com.learn.chapterl.pojo.Role" >
        update t_role set 
        role name= #{roleName}, 
        note = #{note} 
        where id = #{id} 
    </update> 
</mapper>
```

这里的 `resultMap` 元素用于定义映射规则 ，而实际上 MyBatis 在满足一定的规则下，完成自动映射，而增、删、查、改对应着 insert 、delete 、select、update 四个元素，十分明了 。

> 注意，`mapper` 元素中的 `namespace` 属性，它要和一个接口的全限定名保持一致 ，而里面的 SQL 的 `id` 也需要和接口定义的方法完全保持一致

定义 MyBatis 映射文件

```java
package com.learn.chapterl.mapper;
import com.learn.chapterl.pojo.Role;
public interface RoleMapper{
    public Role getRole(Integer id);
    public int deletRole(Integer id);
    public int insertRole(Role role);
    public int updateRole(Role role);
}
```

对角色类的增、删、 查 、 改

```java
SqlSession sqlSession = null;
try { 
    sqlSession = MyBatisUtil.getSqlSession();
    RoleMapper roleMapper = sqlSession.getMapper(RoleMapper.class); 
    Role role = roleMapper.getRole(1);//查询
    System.err.println(role.getRoleName()); 
    role.setRoleName("update role name"); 
    roleMapper.updateRole(role);//更新
    Role role2 = new Role(); 
    role2.setNote("note2"); 
    role2.setRoleName("role2"); 
    roleMapper.insertRole(role); //插入
    roleMapper.deleteRole(S); //删除
    sqlSession.commit(); //提交事务
} catch (Exception ex) { 
	ex.printStackTrace();
    if (sqlSession != null) { 
    sqlSession.rollback();//回滚事务
    }
} finally{//关闭连接
    if (sqlSession ! = null) { 
    sqlSession.close();
    }
	}  
```

显然 MyBatis 在业务逻辑上和 Hibernate 是大同小异的。其区别在于，MyBatis 需要提供接口和 SQL ，这意味着它的工作量会 比 Hibernate 大，但是由于自定义 SQL 、 映射关系，所以其灵活性、可优化性就超过了 Hibernate 。互联网可优化性、灵活性是十分重要 的，因为一条 SQL 的性能可能相差十几倍到几十倍，这对于互联网系统是十分重要的 。

### Hibernate 和 MyBatis 的区别

Hibernate 和 MyBatis 的增、删、查、改，对于业务逻辑层来说大同小异，对于映射层而言 Hibernate 的配置不需要接口和 SQL ，相反 MyBatis 是需要的。

对于 Hibernate 而言 ，不需要编写大量的 SQL，就可以完全映射，同时提供了日志、缓存、级联（级联比 MyBatis 强大）等特性， 此外还提供 HQL (Hibernate Query Language ）对 POJO 进行操作，使用十分方便，但是它也有致命的缺陷。

由于无须 SQL ，当多表关联超过 3 个的时候，通过 Hibernate 的级联会造成太多性能的丢失 ，又或者我现在访问 一个财务的表，然后它会关联财产信息表，财产又分为机械、原料等，显然机械和原料的字段是不一样的，这样关联字段只能根据特定的条件变化而变化，而 Hibernate 无法支持这样的变化 。 遇到存储过程， Hibernate 只能作罢。更为关键的是性能，在管理系统的时代，对于性能的要求不是那么苛刻，但是在互联网时代性能就是系统的根本，响应过慢就会丧失客户，试想一下谁会去用一个经常需要等待超过10 秒以上的应用呢？

以上的问题 MyBatis 都可以解决，MyBatis 可以自由书写 SQL、支持动态 SQL、处理列表、动态生成表名、支持存储过程。这样就可 以灵活地定义查询语句 ，满足各类需求和性能优化的需要，这些在互联网系统中是十分重要 的。

但 MyBatis 也有缺陷 。

+ 首先，它要编写 SQL 和映射规则，其工作量稍微大于 Hibernate 。

+ 其次，它支持的工具也很有限，不能像 Hibernate 那样有许多的插件可以帮助生成映射代码和关联关系，而即使使用生成工具，往往也需要开发者进一步简化，MyBatis 通过手工编码，工作量相对大些。

所以对于性能要求不太苛刻 的系统，比如管理系统、ERP 等推荐使用 Hibernate；而对于性能要求高、响应快、灵活的系统则推荐使用 MyBatis 。

## 获取MyBatis

### GitHub

GitHub[地址](https://github.com/mybatis/mybatis-3)

**MyBatis SQL Mapper Framework for Java**

The MyBatis SQL mapper framework makes it easier to use a relational database with object-oriented applications. MyBatis couples objects with stored procedures or SQL statements using a XML descriptor or annotations. Simplicity is the biggest advantage of the MyBatis data mapper over object relational mapping tools.

是一个Maven项目，点击Releases，选择版本即可下载

### Maven仓库

Maven仓库[地址](https://mvnrepository.com/artifact/org.mybatis/mybatis)

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>
```

## HelloMyBatis

### 搭建环境

1、创建数据库

```mysql
CREATE DATABASE `mybatis`;
USE `mybatis`;

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `pwd` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of user
-- ----------------------------
INSERT INTO `user` VALUES (1, '张三', '123456');
INSERT INTO `user` VALUES (2, '李四', '123456');
INSERT INTO `user` VALUES (3, '王五', '123456');

SET FOREIGN_KEY_CHECKS = 1;
```

2、新建Maven项目

在IDEA中创建普通的Maven项目，并在pom.xml中导入以下三个依赖

```xml
<!--导入依赖-->
<dependencies>
    <!--mysql依赖-->
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.21</version>
    </dependency>

    <!--mybatis-->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.5</version>
    </dependency>

    <!--junit-->
    <!-- https://mvnrepository.com/artifact/junit/junit -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 创建模块

在父模块下创建mybatis-01的Maven子模块

1、编写mybatis的核心配置文件

在mybatis-01模块中的src->main->resources中创建mybatis-config.xml文件，导入以下内容

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <!--事物管理-->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

并将`property`中的`value`进行修改

2、编写MyBatis工具类

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。

```java
// 官方文档
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

```java
public class MyBatisUtils {

    /**
     * 提升作用域
     */
    private static SqlSessionFactory sqlSessionFactory;

    static {
        // 获取sqlSessionFactory对象
        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }
        catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

### 编写代码

1、创建pojo包并创建User.java

```java
public class User {
    // 变量名与数据库要对应
    private int id;
    private String name;
    private String pwd;
    
    /*** setter and getter ***/
    /*** Constructor ***/
    /*** toString ***/
}
```

2、 在dao包中创建接口

```java
public interface UserDao {
    /**
     * 获取用户列表
     */
    List<User> getUserList();
}
```

3、创建UserMapper.xml文件

