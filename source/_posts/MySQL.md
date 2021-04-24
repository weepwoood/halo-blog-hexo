---
title: MySQL
tags:
  - MySQL
categories:
  - [MySQL]
date: 2020-07-07 09:33:36
---

> 参考书籍《SQL必知必会》第4版 Ben Forta 著

## 数据库简介

### 数据库（DB，Data Base）

保存有组织的数据的容器（通常是一个文件或一组文件）。

表（table） ：某种特定类型数据的结构化清单。存储在表中的数据是同一种类型的数据或清单。

列（column） ：表中的一个字段。所有表都是由一个或多个列组成的。

行（row） ：表中的一个记录。表中的数据是按行存储的，所保存的每个记录存储在自己的行内。

> 如果将表想象为网格，网格中垂直的列为表列，水平行为表行。

主键（primary key）：一列（或一组列），其值能够**唯一标识**表中每一行。顾客表可以使用顾客编号，而订单表可以使用订单ID。雇员表可以使用雇员ID 或雇员社会安全号。

表中的任何列都可以作为主键，只要它满足以下条件：

+ 任意两行都不具有相同的主键值；
+ 每一行都必须具有一个主键值（主键列不允许NULL 值）；
+ 主键列中的值不允许修改或更新；
+ 主键值不能重用（如果某行从表中删除，它的主键不能赋给以后的新行）。

> 主键通常定义在表的一列上，但并不是必需这么做，也可以一起使用多个列作为主键。
>
> 在使用多列作为主键时，上述条件必须应用到所有列，所有列值的组合必须是唯一的（但单个列的值可以不唯一）。

分为关系型数据库和非关系型数据库

+ [关系型数据库](https://baike.baidu.com/item/%E5%85%B3%E7%B3%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E5%BA%93/8999831)：SQL（Structured Query Language）指采用了[关系模型](https://baike.baidu.com/item/关系模型/3189329)来组织数据的数据库，其以行和列的形式存储数据，以便于用户理解，关系型数据库这一系列的行和列被称为表，一组表组成了[数据库](https://baike.baidu.com/item/数据库/103728)。
+ [非关系型数据库](https://baike.baidu.com/item/NoSQL/8828247?fromtitle=%E9%9D%9E%E5%85%B3%E7%B3%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E5%BA%93&fromid=6376860&fr=aladdin)：NoSQL（Not Only SQL），对象存储，通过对象的属性来决定。

### 数据库管理系统（DBMS，Database Management System）

数据库管理软件，科学有效的管理数据。MySQL是一种DBMS，即它是一种数据库软件。

### MySQL

优点：

+ 成本，MySQL是开放源代码的，一般可以免费使用（甚至可以免费修改）。
+ 性能，MySQL执行很快（非常快）。
+ 可信赖，某些非常重要和声望很高的公司、站点使用MySQL， 这些公司和站点都用MySQL来处理自己的重要数据。
+ 简单，MySQL很容易安装和使用。

5.7版本稳定

## MySQL的安装

### Windows下的安装

官网[下载地址](https://dev.mysql.com/downloads/mysql/)，选择下载ZIP Archive

<img src="http://img.whl123456.top/image/MySQL官网下载.png" alt="MySQL官网下载" style="zoom:50%;" />

1. 压缩包下载完成后解压到自己指定的文件夹中
2. 编辑环境变量，将MySQL下的bin目录添加到Path
3. 在`D:\安装路径\mysql-8.0.20`下新建`my.ini`配置文件
4. 编辑`my.ini`文件

   ```shell
   [mysql]
   # 设置mysql客户端默认字符集
   default-character-set=utf8 
   [mysqld]
   #设置3306端口
   port = 3306 
   # 设置mysql的安装目录
   basedir=D:\Develop\MySQL\mysql-8.0.20
   # 允许最大连接数
   max_connections=200
   # 服务端使用的字符集默认为8比特编码的latin1字符集
   character-set-server=utf8
   # 创建新表时将使用的默认存储引擎
   default-storage-engine=INNODB
   ```

   > 路径选择自己安装目录，`data`目录不要自己创建，参考[文章](https://www.jb51.net/article/172172.htm)

5. 安装MySQL服务：在**管理员模式**下启动命令行终端（cmd），执行`mysqld -install`命令，安装成功则显示`Service successfully installed.`
6. 初始化数据库文件：再输入`mysqld --initialize-insecure --user=mysql`，等待一会不报错且在安装目录中生成`data`文件则表示成功
7. 启动MySQL：键入`net start mysql`启动服务，显示`MySQL服务已启动成功`
8. 进入MySQL管理界面：在启动成功MySQL服务后，输入命令`mysql -u root -p`，Enter password：时直接回车
9. 设置用户和密码：进入SQL命令行后输入。

   ```shell
   update mysql.user set authentication_string=MD5('123456') where User='root' and Host='locahost';
   ```

   > 在 MySQL5.7 中`user`表的`password`已换成了`authentication_string`。
   >
   > `password()`加密函数已经在 8.0.11 中移除了，可以使用`MD5()`函数代替。

10. 刷新权限：`FLUSH PRIVILEGES;`

11. 重启MySQL：退出MySQL命令行，输入`quit`，再在命令行中输入`net stop mysql`关闭MySQL之后启动`net start mysql`

## 使用Navicat进行可视化操作

连接号Navicat后，选择新建查询，输入SQL建表语句进行创建一个school表

```SQL
CREATE DATABASE `school` CHARACTER 
SET utf8 COLLATE utf8_general_ci;
```

![Navicat创建表](http://img.whl123456.top/image/Navicat创建表.png)



![Navicat可视化操作表](http://img.whl123456.top/image/Navicat可视化操作表.png)



## SQL语句

1. 查看当前所有数据库：`SHOW DATABASES;`

2. 打开指定的库：`USE 库名;`

3. 查看当前的所有表：`SHOW TABLES;`

4. 查看其他库的所有表：`SHOW TABLES FROM 库名;`

5. 创建表

   ```sql
   CREATE TABLE 表名(
   列名 列类型,
   列名 列类型,
   );
   ```

6. 查看表结构：`DESC 表名;`

7. 查看数据库版本：

   + 登录到MySQL`SELECT VERSION();` 
   + 没有登录MySQL命令行中`mysql -V`

```sql
SHOW DATABASES; --查看所有的数据库
USE 数据库名 --切换数据库
SHOW TABLES; --查看数据库中所有的表
DESCRIBE 表名; --显示表中信息
CREATE DATABASES 数据库名; --创建数据库
```

### 语法规范

**SQL**（Structured Query Language）结构化查询语言的缩写。

1. 不区分大小写，建议关键字大写，表名、列名小写
2. 多条SQL 语句必须以分号（;）分隔。每条命令建议用分号结尾
3. 每条命令根据需要进行缩进和换行
4. 注释
   + 单行注释1：#单行注释（这种形式很少得到支持）
   + 单行注释2：-- 单行注释
   + 多行注释：/\*多行注释\*/

关键字（keyword）：作为SQL 组成部分的保留字。关键字不能用作表或列的名字。

### 检索数据

#### `SELECT` 语句

最经常使用的SQL 语句大概就是 `SELECT` 语句了。它的用途是从一个或多个表中检索信息。

#### 检索单个列

利用`SELECT`语句从Products 表中检索一个名为prod_name的列。所需的列名写在`SELECT`关键字之后，用`FROM` 关键字指出从哪个表中检索数据。

```sql
SELECT prod_name FROM Products; 
/* 输出结果
prod_name 
-------------------
Fish bean bag toy
Bird bean bag toy
Rabbit bean bag toy
8 inch teddy bear
12 inch teddy bear
18 inch teddy bear
Raggedy Ann
King doll
Queen doll
*/
```

#### 检索多个列

要想从一个表中检索多个列，仍然使用相同的`SELECT` 语句。唯一的不同是必须在`SELECT` 关键字后给出多个列名，列名之间必须以逗号分隔。

```sql
SELECT prod_id, prod_name, prod_price 
FROM Products;
```

与前一个例子一样，这条语句使用 `SELECT` 语句从表 Products 中选择数据。在这个例子中，指定了 3 个列名，列名之间用逗号分隔。此语句的输出如下：

```
prod_id       prod_name                prod_price 
---------     --------------------     ----------
BNBG01        Fish bean bag toy        3.4900 
BNBG02        Bird bean bag toy        3.4900 
BNBG03        Rabbit bean bag toy      3.4900 
BR01          8 inch teddy bear        5.9900 
BR02          12 inch teddy bear       8.9900 
BR03          18 inch teddy bear       11.9900 
RGAN01        Raggedy Ann              4.9900 
RYL01         King doll                9.4900 
RYL02         Queen dool               9.4900
```

#### 检索所有列

除了指定所需的列外（如上所述，一个或多个列），`SELECT` 语句还可以检索所有的列而不必逐个列出它们。在实际列名的位置使用星号（`*`）通配符可以做到这点，如下所示

```
SELECT * FROM Products;
```

> 一般而言，除非你确实需要表中的每一列，否则最好别使用`*`通配符。虽然使用通配符能让你自己省事，不用明确列出所需列，但检索不需要的列通常会降低检索和应用程序的性能。
>
> 使用通配符有一个大优点。由于不明确指定列名（因为星号检索每一列），所以能检索出名字未知的列。

#### 检索不同的值

使用 DISTINCT 关键字，它指示数据库只返回不同的值

```sql
SELECT DISTINCT vend_id 
FROM Products;
```

`SELECT DISTINCT vend_id` 告诉 DBMS 只返回不同（具有唯一性）的vend_id 行，所以正如下面的输出，只有 3 行。如果使用 `DISTINCT` 关键字，它必须直接放在列名的前面。

```
vend_id 
----------
BRS01
DLL01
FNG01
```

> `DISTINCT` 关键字作用于所有的列，不仅仅是跟在其后的那一列。例如，你指定 `SELECT DISTINCT vend_id, prod_price`，除非指定的两列完全相同，否则所有的行都会被检索出来

#### 限制结果

SELECT 语句返回指定表中所有匹配的行，很可能是每一行。如果你只想返回第一行或者一定数量的行，该怎么办呢？这是可行的，然而遗憾的是，各种数据库中的这一 SQL 实现并不相同。

```sql
SELECT prod_name 
FROM Products;
/* 输出结果
prod_name 
-------------------
Fish bean bag toy
Bird bean bag toy
Rabbit bean bag toy
8 inch teddy bear
12 inch teddy bear
18 inch teddy bear
Raggedy Ann
King doll
Queen doll
*/
```

如果使用 MySQL、MariaDB、PostgreSQL 或者 SQLite，需要使用 `LIMIT` 子句，像这样：

```sql
SELECT prod_name 
FROM Products 
LIMIT 5;
/* 输出结果
prod_name 
-------------------
Fish bean bag toy
Bird bean bag toy
Rabbit bean bag toy
8 inch teddy bear
12 inch teddy bear
*/
```

上述代码使用 SELECT 语句来检索单独的一列数据。LIMIT 5 指示 MySQL等 DBMS 返回不超过 5 行的数据。

为了得到后面的 5 行数据，需要指定从哪儿开始以及检索的行数

```sql
SELECT prod_name 
FROM Products 
LIMIT 5 OFFSET 5;
/* 输出结果
prod_name 
-------------------
18 inch teddy bear
Raggedy Ann
King doll
Queen doll
*/
```

`LIMIT 5 OFFSET 5` 指示 MySQL 等 DBMS 返回从第 5 行起的 5 行数据。第一个数字是检索的行数，第二个数字是指从哪儿开始（从0开始计数）。

Products 表中只有 9 种产品，所以只返回了 4 行数据。

### 排序检索数据

如何使用 `SELECT` 语句的 `ORDER BY` 子句，根据需要排序检索出的数据。

#### 排序数据

SQL 语句返回某个数据库表的单个列。但其输出，并没有特定的顺序。

```sql
SELECT prod_name 
FROM Products;
/* 输出结果
prod_name 
-------------------
Fish bean bag toy
Bird bean bag toy
Rabbit bean bag toy
8 inch teddy bear
12 inch teddy bear
18 inch teddy bear
Raggedy Ann
King doll
Queen doll
*/
```

其实，检索出的数据并不是随机显示的。如果不排序，数据一般将以它在底层表中出现的顺序显示，这有可能是数据最初添加到表中的顺序。但是，如果数据随后进行过更新或删除，那么这个顺序将会受到 DBMS 重用回收存储空间的方式的影响。因此，如果不明确控制的话，则最终的结果不能（也不应该）依赖该排序顺序。关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有任何意义。

> 子句：SQL 语句由子句构成，有些子句是必需的，有些则是可选的。一个子句通常由一个关键字加上所提供的数据组成。子句的例子如 `SELECT` 语句的 `FROM` 子句。

为了明确地排序用 `SELECT` 语句检索出的数据，可使用 `ORDER BY` 子句。`ORDER BY` 子句取一个或多个列的名字，据此对输出进行排序。

```sql
SELECT prod_name 
FROM Products 
ORDER BY prod_name;
/* 输出结果
prod_name 
-------------------
12 inch teddy bear
18 inch teddy bear
8 inch teddy bear
Bird bean bag toy
Fish bean bag toy
King doll
Queen doll
Rabbit bean bag toy
Raggedy Ann
*/
```

> 注意：在指定一条 `ORDER BY` 子句时，应该**保证它是 `SELECT` 语句中最后一条子句**。如果它不是最后的子句，将会出现错误消息

#### 按多个列排序

经常需要按不止一个列进行数据排序。例如，如果要显示雇员名单，可能希望按姓和名排序（首先按姓排序，然后在每个姓中再按名排序）。如果多个雇员有相同的姓，这样做很有用。

要按多个列排序，简单指定列名，列名之间用逗号分开即可（就像选择多个列时那样）。

下面的代码检索 3 个列，并按其中两个列对结果进行排序——首先按价格，然后按名称排序。

```sql
SELECT prod_id, prod_price, prod_name 
FROM Products
ORDER BY prod_price, prod_name;
/* 输出结果
prod_id     prod_price     prod_name 
-------     ----------     -------------------- 
BNBG02      3.4900         Bird bean bag toy 
BNBG01      3.4900         Fish bean bag toy 
BNBG03      3.4900         Rabbit bean bag toy 
RGAN01      4.9900         Raggedy Ann 
BR01        5.9900         8 inch teddy bear 
BR02        8.9900         12 inch teddy bear 
RYL01       9.4900         King doll 
RYL02       9.4900         Queen doll 
BR03        11.9900        18 inch teddy bear
*/
```

上述例子中的输出，仅在多个行具有相同的 `prod_price` 值时才对产品按 `prod_name` 进行排序。如果 `prod_price` 列中所有的值都是唯一的，则不会按 `prod_name` 排序。

#### 按列位置排序

除了能用列名指出排序顺序外，`ORDER BY` 还支持按相对列位置进行排序。

```sql
SELECT prod_id, prod_price, prod_name 
FROM Products 
ORDER BY 2, 3;
/* 输出结果
prod_id     prod_price     prod_name 
-------     ----------     -------------------- 
BNBG02      3.4900         Bird bean bag toy 
BNBG01      3.4900         Fish bean bag toy 
BNBG03      3.4900         Rabbit bean bag toy 
RGAN01      4.9900         Raggedy Ann 
BR01        5.9900         8 inch teddy bear 
BR02        8.9900         12 inch teddy bear 
RYL01       9.4900         King doll 
RYL02       9.4900         Queen doll 
BR03        11.9900        18 inch teddy bear
*/
```

`ORDER BY 2`表示按 `SELECT` 清单中的第二个列 `prod_price` 进行排序。`ORDER BY 2，3` 表示先按 `prod_price`，再按 `prod_name` 进行排序。

这一技术的主要好处在于不用重新输入列名。但它也有缺点。

+ 首先，不明确给出列名有可能造成错用列名排序。
+ 其次，在对 `SELECT` 清单进行更改时容易错误地对数据进行排序（忘记对 `ORDER BY` 子句做相应的改动）。
+ 最后，如果进行排序的列不在 `SELECT` 清单中，显然不能使用这项技术。如果有必要，可以混合使用实际列名和相对列位置。

#### 指定排序方向

数据排序不限于升序排序（从 A 到 Z），这只是默认的排序顺序。还可以使用 `ORDER BY` 子句进行降序（从 Z 到 A）排序。为了进行降序排序，必须指定 `DESC` 关键字。

```sql
SELECT prod_id, prod_price, prod_name 
FROM Products 
ORDER BY prod_price DESC;
/* 输出结果
prod_id     prod_price     prod_name 
-------     ----------     -------------------- 
BR03        11.9900        18 inch teddy bear 
RYL01       9.4900         King doll 
RYL02       9.4900         Queen doll 
BR02        8.9900         12 inch teddy bear 
BR01        5.9900         8 inch teddy bear 
RGAN01      4.9900         Raggedy Ann 
BNBG01      3.4900         Fish bean bag toy 
BNBG02      3.4900         Bird bean bag toy 
BNBG03      3.4900         Rabbit bean bag toy
*/
```

```sql
SELECT prod_id, prod_price, prod_name 
FROM Products 
ORDER BY prod_price DESC, prod_name;
/* 输出结果
prod_id     prod_price     prod_name 
-------     ----------     -------------------- 
BR03        11.9900        18 inch teddy bear 
RYL01       9.4900         King doll 
RYL02       9.4900         Queen doll 
BR02        8.9900         12 inch teddy bear
BR01        5.9900         8 inch teddy bear 
RGAN01      4.9900         Raggedy Ann 
BNBG02      3.4900         Bird bean bag toy 
BNBG01      3.4900         Fish bean bag toy 
BNBG03      3.4900         Rabbit bean bag toy
*/
```

`DESC` 关键字只应用到直接位于其前面的列名。在上例中，只对 `prod_price`列指定 DESC，对 `prod_name` 列不指定。因此，`prod_price` 列以降序排序，而 `prod_name` 列（在每个价格内）仍然按标准的升序排序。

如果想在多个列上进行降序排序，必须对每一列指定 `DESC` 关键字。

请注意，`DESC` 是 `DESCENDING` 的缩写，这两个关键字都可以使用。与 `DESC`相对的是 `ASC`（或 `ASCENDING`），在升序排序时可以指定它。但实际上，`ASC` 没有多大用处，因为升序是默认的（如果既不指定 `ASC` 也不指定`DESC`，则假定为 `ASC`）。

### 过滤数据

如何使用 `SELECT` 语句的 `WHERE` 子句指定搜索条件

#### 使用 `WHERE` 子句

数据库表一般包含大量的数据，很少需要检索表中的所有行。通常只会根据特定操作或报告的需要提取表数据的子集。只检索所需数据需要指定搜索条件（search criteria），搜索条件也称为过滤条件（filter condition）。

在 `SELECT` 语句中，数据根据 `WHERE` 子句中指定的搜索条件进行过滤。`WHERE` 子句在表名（`FROM` 子句）之后给出

```sql
SELECT prod_name, prod_price 
FROM Products 
WHERE prod_price = 3.49;
/* 输出结果
prod_name               prod_price
-------------------     ---------- 
Fish bean bag toy       3.49 
Bird bean bag toy       3.49 
Rabbit bean bag toy     3.49
*/
```

检查这一列的值是否为指定值，据此过滤数据。不过，SQL 不止能测试等于，还能做更多的事情。

> 在同时使用 `ORDER BY` 和 `WHERE` 子句时，应该让 `ORDER BY` 位于`WHERE` 之后，否则将会产生错误

#### `WHERE` 子句操作符

| 操作符 | 说明     | 操作符  | 说明               |
| ------ | -------- | ------- | ------------------ |
| =      | 等于     | >       | 大于               |
| <>     | 不等于   | >=      | 大于等于           |
| !=     | 不等于   | !>      | 不大于             |
| <      | 小于     | BETEEN  | 在指定的两个值之间 |
| <=     | 小于等于 | IS NULL | 为NULL值           |
| !<     | 不小于   |         |                    |

#### 检查单个值

列出所有价格小于 10 美元的产品

```sql
SELECT prod_name, prod_price 
FROM Products 
WHERE prod_price < 10;
/*输出结果
prod_name               prod_price 
-------------------     ---------- 
Fish bean bag toy       3.49 
Bird bean bag toy       3.49 
Rabbit bean bag toy     3.49 
8 inch teddy bear       5.99 
12 inch teddy bear      8.99 
Raggedy Ann             4.99 
King doll               9.49 
Queen doll              9.49
*/
```

#### 不匹配检查

列出所有不是供应商 DLL01 制造的产品

```sql
SELECT vend_id, prod_name 
FROM Products 
WHERE vend_id <> 'DLL01';
/*输出结果
vend_id        prod_name 
----------     ------------------ 
BRS01          8 inch teddy bear
BRS01          12 inch teddy bear 
BRS01          18 inch teddy bear 
FNG01          King doll 
FNG01          Queen doll
*/
```

#### 范围值检查

要检查某个范围的值，可以使用 `BETWEEN` 操作符。其语法与其他 `WHERE`子句的操作符稍有不同，因为它需要两个值，即范围的开始值和结束值。例如，`BETWEEN` 操作符可用来检索价格在 5 美元和 10 美元之间的所有产品，或在指定的开始日期和结束日期之间的所有日期。

检索价格在 5 美元和 10美元之间的所有产品

```sql
SELECT prod_name, prod_price 
FROM Products 
WHERE prod_price BETWEEN 5 AND 10;
/*输出结果
prod_name               prod_price 
-------------------     ---------- 
8 inch teddy bear       5.99 
12 inch teddy bear      8.99 
King doll               9.49 
Queen doll              9.49
*/
```

在使用 BETWEEN 时，必须指定两个值——所需范围的低端值和高端值。这两个值必须用 AND 关键字分隔。BETWEEN 匹配范围中所有的值，**包括**指定的开始值和结束值。

#### 空值检查

在创建表时，表设计人员可以指定其中的列能否不包含值。在一个列不包含值时，称其包含空值 NULL。

> NULL ：无值（no value），它与字段包含 0、空字符串或仅仅包含空格不同。

确定值是否为 NULL，不能简单地检查是否= NULL。`SELECT` 语句有一个特殊的 `WHERE` 子句，可用来检查具有 NULL 值的列。这个 `WHERE` 子句就是 `IS NULL` 子句。

```sql
SELECT prod_name
FROM Products 
WHERE prod_price IS NULL;
/*输出结果
prod_name
-------------------
N/A
*/
```

```sql
SELECT cust_name 
FROM CUSTOMERS 
WHERE cust_email IS NULL;
/*输出结果
cust_name 
---------- 
Kids Place 
The Toy Store
*/
```

> 通过过滤选择不包含指定值的所有行时，你可能希望返回含 NULL 值的行。但是这做不到。因为未知（unknown）有特殊的含义，数据库不知道它们是否匹配，所以在进行匹配过滤或非匹配过滤时，不会返回这些结果。
>
> 过滤数据时，一定要验证被过滤列中含 NULL 的行确实出现在返回的数据中。

### 高级数据过滤

如何组合 WHERE 子句以建立功能更强、更高级的搜索条件，以及学习如何使用 NOT 和 IN 操作符

#### 组合 `WHERE` 子句

##### `AND`操作符

要通过不止一个列进行过滤，可以使用 AND 操作符给 WHERE 子句附加条件。

```sql
SELECT prod_id, prod_price, prod_name
FROM Products 
WHERE vend_id = 'DLL01' AND prod_price <= 4;
/*输出结果
prod_id     prod_price   prod_name 
-------     ----------   -------------------- 
BNBG02      3.49         Bird bean bag toy 
BNBG01      3.49         Fish bean bag toy 
BNBG03      3.49         Rabbit bean bag toy
*/
```

`AND` 指示 DBMS 只返回满足所有给定条件的行。

##### `OR`操作符

`OR` 操作符与 `AND` 操作符正好相反，它指示 DBMS 检索匹配任一条件的行。

```sql
SELECT prod_name, prod_price 
FROM Products 
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01';
/*输出结果
prod_name               prod_price 
-------------------     ---------- 
8 inch teddy bear	    5.99
12 inch teddy bear	    8.99
18 inch teddy bear	    11.99
Fish bean bag toy	    3.49
Bird bean bag toy	    3.49
Rabbit bean bag toy  	3.49
Raggedy Ann	            4.99
*/
```

##### 求值顺序

WHERE 子句可以包含任意数目的 `AND` 和 `OR` 操作符。允许两者结合以进行复杂、高级的过滤。

SQL（像多数语言一样）在处理 `OR` 操作符前，优先处理 `AND` 操作符。圆括号具有比 AND 或 OR 操作符更高的求值顺序

#### `IN` 操作符

`IN` 操作符用来指定条件范围，范围中的每个条件都可以进行匹配。`IN` 取一组由逗号分隔、括在圆括号中的合法值。

```sql
SELECT prod_name, prod_price 
FROM Products 
WHERE vend_id IN ( 'DLL01', 'BRS01' ) 
ORDER BY prod_name;
/*输出结果
prod_name               prod_price 
-------------------     ---------- 
12 inch teddy bear      8.99
18 inch teddy bear      11.99
8 inch teddy bear       5.99
Bird bean bag toy       3.49
Fish bean bag toy       3.49
Rabbit bean bag toy     3.49
Raggedy Ann             4.99
*/
```

此 `SELECT` 语句检索由供应商 DLL01 和 BRS01 制造的所有产品。`IN` 操作符后跟由逗号分隔的合法值，这些值必须括在圆括号中。

同样的`OR`操作符也可以实现

```sql
SELECT prod_name, prod_price 
FROM Products 
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01' 
ORDER BY prod_name;
/*输出结果
prod_name               prod_price 
-------------------     ---------- 
12 inch teddy bear      8.99
18 inch teddy bear      11.99
8 inch teddy bear       5.99
Bird bean bag toy       3.49
Fish bean bag toy       3.49
Rabbit bean bag toy     3.49
Raggedy Ann             4.99
*/
```

为什么要使用 `IN` 操作符？其优点如下。

+ 在有很多合法选项时，`IN` 操作符的语法更清楚，更直观。
+ 在与其他 `AND` 和 `OR` 操作符组合使用 `IN` 时，求值顺序更容易管理。
+ `IN` 操作符一般比一组 `OR` 操作符执行得更快（在上面这个合法选项很少的例子中，你看不出性能差异）。
+ `IN` 的最大优点是可以包含其他 `SELECT` 语句，能够更动态地建立`WHERE` 子句。

#### `NOT` 操作符

`WHERE` 子句中的 `NOT` 操作符有且只有一个功能，那就是否定其后所跟的任何条件。

```sql
SELECT prod_name 
FROM Products 
WHERE NOT vend_id = 'DLL01' 
ORDER BY prod_name;
/*输出结果
prod_name 
------------------ 
12 inch teddy bear 
18 inch teddy bear 
8 inch teddy bear 
King doll 
Queen doll
*/
```

`NOT` 否定跟在其后的条件匹配非 DLL01 之外的所有东西

> 同样也可以使用`<>`操作符来完成
>
> 对于简单的 `WHERE` 子句，使用 `NOT` 确实没有什么优势。但在更复杂的子句中，`NOT` 是非常有用的。例如，在与 `IN` 操作符联合使用时，`NOT` 可以非常简单地找出与条件列表不匹配的行。

### 用通配符进行过滤



#### `LIKE` 操作符

通配符：用来匹配值的一部分的特殊字符。

搜索模式：由字面值、通配符或两者组合构成的搜索条件。

通配符本身实际上是 SQL 的 `WHERE` 子句中有特殊含义的字符，SQL 支持几种通配符。为在搜索子句中使用通配符，必须使用 LIKE 操作符。`LIKE`指示 DBMS，后跟的搜索模式利用通配符匹配而不是简单的相等匹配进行比较。

通配符搜索只能用于文本字段（字符串），非文本数据类型字段不能使用通配符搜索。

##### 百分号（`%`）通配符

最常使用的通配符是百分号（`%`）。在搜索串中，`%`表示任何字符出现任意次数。

找出所有以词 Fish 起头的产品

```sql
SELECT prod_id, prod_name  
FROM Products  
WHERE prod_name LIKE 'Fish%';
/*输出结果
prod_id     prod_name  
-------     ------------------    
BNBG01      Fish bean bag toy
*/
```

`%`告诉 DBMS 接受 Fish 之后的任意字符，不管它有多少字符

> 区分大小写：根据 DBMS 的不同及其配置，搜索可以是区分大小写的。如果区分大小写，则'fish%'与 Fish bean bag toy 就不匹配。MySQL默认不区分大小写

通配符可在搜索模式中的任意位置使用，并且可以使用多个通配符。

```sql
SELECT prod_id, prod_name  
FROM Products  
WHERE prod_name LIKE '%bean bag%';
/*输出结果
prod_id      prod_name 
--------     --------------------  
BNBG01       Fish bean bag toy  
BNBG02       Bird bean bag toy  
BNBG03       Rabbit bean bag toy
*/
```

搜索模式`%bean bag%`表示匹配任何位置上包含文本 bean bag 的值，不论它之前或之后出现什么字符。

通配符也可以出现在搜索模式字符之间。

> 需要特别注意，除了能匹配一个或多个字符外，`%`还能匹配 0 个字符。`%`代表搜索模式中给定位置的 0 个、1 个或多个字符。

> 通配符`%`看起来像是可以匹配任何东西，但有个例外，这就是 `NULL`。

##### 下划线（`_`）通配符

另一个有用的通配符是下划线（`_`）。下划线的用途与`%`一样，但它只匹配单个字符，而不是多个字符。

#### 使用通配符的技巧

通配符搜索的处理一般要比前面讨论的其他搜索所花时间更长。

+ 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。
+ 在确实需要使用通配符时，也尽量不要把它们用在搜索模式的开始处。把通配符置于开始处，搜索起来是最慢的。
+ 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。

### 用正则表达式进行搜索

正则表达式是用来匹配文本的特殊的串（字符集合）

MySQL用`WHERE`子句对正则表达式提供了初步的支持，允许你指定正则表达式，过滤`SELECT`检索出的数据。

#### 基本字符匹配

```sql
SELECT prod_name
FROM products
WHERE prod_name REGEXP '12'
ORDER BY prod_name;
/*输出结果
prod_name
-------=
12 inch teddy bear
*/
```

除关键字`LIKE`被`REGEXP`替代外，这条语句看上去非常像使用`LIKE`的语句。

它告诉MySQL：`REGEXP`后所跟的东西作为正则表达式处理。

```sql
SELECT prod_name
FROM products
WHERE prod_name REGEXP '1. inch teddy bear'
ORDER BY prod_name;
/*输出结果
prod_name
-------=
12 inch teddy bear
18 inch teddy bear
*/
```

`.`是正则表达式语言中一个特殊的字符，它表示匹配任意一个字符

#### 进行OR匹配

为搜索两个串之一（或者为这个串，或者为另一个串），使用`|`

```sql
SELECT prod_name
FROM products
WHERE prod_name REGEXP '12|18'
ORDER BY prod_name;
/*输出结果
prod_name
-------=
12 inch teddy bear
18 inch teddy bear
*/
```

使用|从功能上类似于在`SELECT`语句中使用`OR`语句，多个OR条件可并入单个正则表达式。

#### 匹配几个字符之一

匹配任何单一字符。但是，如果你只想匹配特定的字符，怎么办？可通过指定一组用`[]`括起来的字符来完成，如下所示：

```SQL
SELECT prod_name
FROM products
WHERE prod_name REGEXP '[128] inch'
ORDER BY prod_name;
/*输出结果
prod_name
-------=
12 inch teddy bear
18 inch teddy bear
8 inch teddy bear
*/
```

`[128]`定义一组字符，的意思是匹配包含1或2或8

#### 匹配范围

集合可用来定义要匹配的一个或多个字符，如`[0-9]`、`[a-z]`

```sql
SELECT prod_name
FROM products
WHERE prod_name REGEXP '[1-7] inch'
ORDER BY prod_name;
/*输出结果
prod_name
--------
12 inch teddy bear
*/
```

#### 匹配特殊字符

正则表达式使用中会占据一些字符，例如`.`、`|`等，如果要匹配这些字符则需要转义使用`\\`

```sql
SELECT vend_name
FROM vendors
WHERE vend_name REGEXP '\\.'
ORDER BY vend_name;
/*输出结果
vend_name
--------
Doll House Inc.
Furball Inc.
*/
```

`\\`也用来引用元字符（具有特殊含义的字符）

| 字符  | 说明                  |
| ----- | --------------------- |
| `\\f` | 换页                  |
| `\\n` | 换行                  |
| `\\r` | 回车                  |
| `\\t` | 制表                  |
| `\\v` | 纵向制表              |
| `\\\` | 反斜杠（`\`）字符本身 |

#### 匹配字符类

存在找出你自己经常使用的数字、所有字母字符或所有数字字母字符等的匹配。为更方便工作，可以使用预定义的字符集，称为字符类

| 类           | 说明                                                |
| ------------ | --------------------------------------------------- |
| `[:alnum:]`  | 任意字母和数字（同`[a-zA-Z0-9]`）                   |
| `[:alpha:]`  | 任意字符（同`[a-zA-Z]`）                            |
| `[:blank:]`  | 空格和制表（同`[\\t]`）                             |
| `[:cntrl:]`  | ASCII控制字符（ASCII 0到31和127）                   |
| `[:digit:]`  | 任意数字（同`[0-9]`）                               |
| `[:graph:]`  | 与`[:print:]`相同，但不包括空格                     |
| `[:lower:]`  | 任意小写字母（同`[a-z]`）                           |
| `[:print:]`  | 任意可打印字符                                      |
| `[:punct:]`  | 既不在`[:alnum:]`又不在`[:cntrl:]`中的任意字符      |
| `[:space:]`  | 包括空格在内的任意空白字符（同`[\\f\\n\\r\\t\\v]`） |
| `[:upper:]`  | 任意大写字母（同`[A-Z]`）                           |
| `[:xdigit:]` | 任意十六进制数字（同`[a-fA-F0-9]`）                 |

#### 匹配多个实例

你可能需要寻找所有的数，不管数中包含多少数字，或者你可能想寻找一个单词并且还能够适应一个尾随的s（如果存在），等等。

| 元字符  | 说明                         |
| ------- | ---------------------------- |
| `*`     | 0个或多个匹配                |
| `+`     | 1个或多个匹配（等于`{1,}`）  |
| `？`    | 0个或1个匹配（等于`{0,1}`）  |
| `{n}`   | 指定数目的匹配               |
| `{n,}`  | 不少于指定数目的匹配         |
| `{n,m}` | 匹配数目的范围（m不超过255） |

#### 定位符

匹配特定位置的文本

| 元字符    | 说明       |
| --------- | ---------- |
| `^`       | 文本的开始 |
| `$`       | 文本的结尾 |
| `[[:<:]]` | 词的开始   |
| `[[:>:]]` | 词的结尾   |









### 数据库定义语言（DDL）

#### 操作数据库

CRUD：增(Create)查(Retrieve)改(Update)删(Delete)

##### 查(Retrieve)

`SHOW DATABASES;`查询所有数据库的名称

`SHOW CREATE DATABASE <数据库名称>;`查询某个数据库的字符集(查询某个数据库的创建语句)

##### 增(Create)

`CREATE DATABASE <数据库名称>;`创建数据库

`CREATE DATABASE IF NOT EXISTS <数据库名称>;`创建前先进行判断

```mysql
CREATE DATABASE IF NOT EXISTS <数据库名称> CHARACTER SET <字符集>;-- 创建指定字符集数据库
```

##### 改(Update)

```mysql
ALTER DATABASE <数据库名称> CHARACTER SET utf8;
```

##### 删(Delete)

```mysql
DROP DATABASE IF EXISTS <数据库名称>;
```

#### 操作表

### 数据库操作语言（DML）



### 数据库查询语言（DQL）

查询表中的数据

#### 基础查询

语法：`SELECT 查询列表 FROM 表名;`

> 可以查询表中的字段、常量值、表达式、函数
>
> 查询结果是一个虚拟的表格

**查询表中的单个字段**

实例`SELECT last_name FROM employees;`

**查询表中的多个字段**

实例`SELECT last_name,salary,email FROM employees;`

**查询表中的所有字段**

`SELECT * FROM employees;`


### 数据库控制语言（DCL）





## 示例数据

建表语句

```sql
-- -----------------------------------------
-- Sams Teach Yourself SQL in 10 Minutes
-- http://forta.com/books/0672336073/
-- Example table creation scripts for MySQL.
-- -----------------------------------------


-- ----------------------
-- Create Customers table
-- ----------------------
CREATE TABLE Customers
(
  cust_id      char(10)  NOT NULL ,
  cust_name    char(50)  NOT NULL ,
  cust_address char(50)  NULL ,
  cust_city    char(50)  NULL ,
  cust_state   char(5)   NULL ,
  cust_zip     char(10)  NULL ,
  cust_country char(50)  NULL ,
  cust_contact char(50)  NULL ,
  cust_email   char(255) NULL 
);

-- -----------------------
-- Create OrderItems table
-- -----------------------
CREATE TABLE OrderItems
(
  order_num  int          NOT NULL ,
  order_item int          NOT NULL ,
  prod_id    char(10)     NOT NULL ,
  quantity   int          NOT NULL ,
  item_price decimal(8,2) NOT NULL 
);


-- -------------------
-- Create Orders table
-- -------------------
CREATE TABLE Orders
(
  order_num  int      NOT NULL ,
  order_date datetime NOT NULL ,
  cust_id    char(10) NOT NULL 
);

-- ---------------------
-- Create Products table
-- ---------------------
CREATE TABLE Products
(
  prod_id    char(10)      NOT NULL ,
  vend_id    char(10)      NOT NULL ,
  prod_name  char(255)     NOT NULL ,
  prod_price decimal(8,2)  NOT NULL ,
  prod_desc  text          NULL 
);

-- --------------------
-- Create Vendors table
-- --------------------
CREATE TABLE Vendors
(
  vend_id      char(10) NOT NULL ,
  vend_name    char(50) NOT NULL ,
  vend_address char(50) NULL ,
  vend_city    char(50) NULL ,
  vend_state   char(5)  NULL ,
  vend_zip     char(10) NULL ,
  vend_country char(50) NULL 
);


-- -------------------
-- Define primary keys
-- -------------------
ALTER TABLE Customers ADD PRIMARY KEY (cust_id);
ALTER TABLE OrderItems ADD PRIMARY KEY (order_num, order_item);
ALTER TABLE Orders ADD PRIMARY KEY (order_num);
ALTER TABLE Products ADD PRIMARY KEY (prod_id);
ALTER TABLE Vendors ADD PRIMARY KEY (vend_id);


-- -------------------
-- Define foreign keys
-- -------------------
ALTER TABLE OrderItems ADD CONSTRAINT FK_OrderItems_Orders FOREIGN KEY (order_num) REFERENCES Orders (order_num);
ALTER TABLE OrderItems ADD CONSTRAINT FK_OrderItems_Products FOREIGN KEY (prod_id) REFERENCES Products (prod_id);
ALTER TABLE Orders ADD CONSTRAINT FK_Orders_Customers FOREIGN KEY (cust_id) REFERENCES Customers (cust_id);
ALTER TABLE Products ADD CONSTRAINT FK_Products_Vendors FOREIGN KEY (vend_id) REFERENCES Vendors (vend_id);
```

具体数据

```sql
-- -------------------------------------------
-- Sams Teach Yourself SQL in 10 Minutes
-- http://forta.com/books/0672336073/
-- Example table population scripts for MySQL.
-- -------------------------------------------


-- ------------------------
-- Populate Customers table
-- ------------------------
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES('1000000001', 'Village Toys', '200 Maple Lane', 'Detroit', 'MI', '44444', 'USA', 'John Smith', 'sales@villagetoys.com');
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact)
VALUES('1000000002', 'Kids Place', '333 South Lake Drive', 'Columbus', 'OH', '43333', 'USA', 'Michelle Green');
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES('1000000003', 'Fun4All', '1 Sunny Place', 'Muncie', 'IN', '42222', 'USA', 'Jim Jones', 'jjones@fun4all.com');
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES('1000000004', 'Fun4All', '829 Riverside Drive', 'Phoenix', 'AZ', '88888', 'USA', 'Denise L. Stephens', 'dstephens@fun4all.com');
INSERT INTO Customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact)
VALUES('1000000005', 'The Toy Store', '4545 53rd Street', 'Chicago', 'IL', '54545', 'USA', 'Kim Howard');

-- ----------------------
-- Populate Vendors table
-- ----------------------
INSERT INTO Vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES('BRS01','Bears R Us','123 Main Street','Bear Town','MI','44444', 'USA');
INSERT INTO Vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES('BRE02','Bear Emporium','500 Park Street','Anytown','OH','44333', 'USA');
INSERT INTO Vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES('DLL01','Doll House Inc.','555 High Street','Dollsville','CA','99999', 'USA');
INSERT INTO Vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES('FRB01','Furball Inc.','1000 5th Avenue','New York','NY','11111', 'USA');
INSERT INTO Vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES('FNG01','Fun and Games','42 Galaxy Road','London', NULL,'N16 6PS', 'England');
INSERT INTO Vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES('JTS01','Jouets et ours','1 Rue Amusement','Paris', NULL,'45678', 'France');

-- -----------------------
-- Populate Products table
-- -----------------------
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('BR01', 'BRS01', '8 inch teddy bear', 5.99, '8 inch teddy bear, comes with cap and jacket');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('BR02', 'BRS01', '12 inch teddy bear', 8.99, '12 inch teddy bear, comes with cap and jacket');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('BR03', 'BRS01', '18 inch teddy bear', 11.99, '18 inch teddy bear, comes with cap and jacket');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('BNBG01', 'DLL01', 'Fish bean bag toy', 3.49, 'Fish bean bag toy, complete with bean bag worms with which to feed it');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('BNBG02', 'DLL01', 'Bird bean bag toy', 3.49, 'Bird bean bag toy, eggs are not included');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('BNBG03', 'DLL01', 'Rabbit bean bag toy', 3.49, 'Rabbit bean bag toy, comes with bean bag carrots');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('RGAN01', 'DLL01', 'Raggedy Ann', 4.99, '18 inch Raggedy Ann doll');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('RYL01', 'FNG01', 'King doll', 9.49, '12 inch king doll with royal garments and crown');
INSERT INTO Products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('RYL02', 'FNG01', 'Queen doll', 9.49, '12 inch queen doll with royal garments and crown');

-- ---------------------
-- Populate Orders table
-- ---------------------
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20005, '2012-05-01', '1000000001');
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20006, '2012-01-12', '1000000003');
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20007, '2012-01-30', '1000000004');
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20008, '2012-02-03', '1000000005');
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20009, '2012-02-08', '1000000001');

-- -------------------------
-- Populate OrderItems table
-- -------------------------
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20005, 1, 'BR01', 100, 5.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20005, 2, 'BR03', 100, 10.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20006, 1, 'BR01', 20, 5.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20006, 2, 'BR02', 10, 8.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20006, 3, 'BR03', 10, 11.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20007, 1, 'BR03', 50, 11.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20007, 2, 'BNBG01', 100, 2.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20007, 3, 'BNBG02', 100, 2.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20007, 4, 'BNBG03', 100, 2.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20007, 5, 'RGAN01', 50, 4.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20008, 1, 'RGAN01', 5, 4.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20008, 2, 'BR03', 5, 11.99);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20008, 3, 'BNBG01', 10, 3.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20008, 4, 'BNBG02', 10, 3.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20008, 5, 'BNBG03', 10, 3.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 1, 'BNBG01', 250, 2.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 2, 'BNBG02', 250, 2.49);
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 3, 'BNBG03', 250, 2.49);
```

