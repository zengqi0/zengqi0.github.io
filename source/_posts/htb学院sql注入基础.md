---
title: htb学院htb学院sql注入基础
date: 2023-06-28 16:11:22
tags:
---

# 简介

​	大多数现代 Web 应用程序在后端使用数据库结构。此类数据库用于存储和检索与 Web 应用程序相关的数据，从实际 Web 内容到用户信息和内容等。

为了使 Web 应用程序动态化，Web 应用程序必须与数据库实时交互。当来自用户的 HTTP(S) 请求到达时，Web 应用程序的后端将向数据库发出查询以构建响应。

这些查询可以包括来自 HTTP(S) 请求的信息或其他相关信息。

![](htb学院sql注入基础\1.png)

​	当用户提供的信息用于构造对数据库的查询时，恶意用户可以诱使查询用于原始程序员预期之外的其他用途，从而使用称为 SQL 注入的攻击为用户提供查询数据库的权限（ SQLi).

SQL 注入是指针对关系数据库的攻击，例如`MySQL`（而非关系数据库的注入，例如 MongoDB，是 NoSQL 注入）

本模块将重点`MySQL`介绍 SQL 注入概念。

## SQL 注入 (SQLi)

Web 应用程序中可能存在许多类型的注入漏洞，例如 HTTP 注入、代码注入和命令注入。

然而，最常见的例子是 SQL 注入。

​	当恶意用户试图传递更改 Web 应用程序发送到数据库的最终 SQL 查询的输入时，就会发生 SQL 注入，从而使用户能够直接对数据库执行其他意外的 SQL 查询。



​	有很多方法可以做到这一点。要使 SQL 注入发挥作用，攻击者必须首先注入 SQL 代码，然后通过更改原始查询或执行全新的查询来颠覆 Web 应用程序逻辑。



​	首先，攻击者必须在预期的用户输入限制之外注入代码，因此它不会作为简单的用户输入来执行。在最基本的情况下，这是通过注入单引号 ( `'`) 或双引号 ( `"`) 来避开用户输入的限制并将数据直接注入 SQL 查询来完成的。

​	一旦攻击者可以注入，他们就必须寻找一种方法来执行不同的 SQL 查询。这可以通过使用 SQL 代码来组成一个工作查询来完成，该查询可以同时执行预期的 SQL 查询和新的 SQL 查询。

​	有很多方法可以实现这一点，例如使用[堆叠](https://www.sqlinjection.net/stacked-queries/)查询或使用[联合](https://www.mysqltutorial.org/sql-union-mysql.aspx/)查询。最后，要检索新查询的输出，我们必须在 Web 应用程序的前端对其进行解释或捕获。

## 用例和影响

SQL 注入可能会产生巨大的影响，尤其是在后端服务器和数据库的权限非常松懈的情况下。

首先，我们可能会检索我们不应该看到的秘密/敏感信息，例如用户登录名和密码或信用卡信息，然后可以将其用于其他恶意目的。SQL 注入导致许多网站密码和数据泄露，这些网站随后被重新用于窃取用户帐户、访问其他服务或执行其他恶意操作。

SQL 注入的另一个用例是破坏预期的 Web 应用程序逻辑。最常见的示例是在不传递有效的用户名和密码凭据对的情况下绕过登录。另一个例子是访问锁定给特定用户的功能，比如管理面板。攻击者还可以直接在后台服务器上读写文件，进而在后台服务器上设置后门，直接控制后台服务器，最终控制整个后台服务器。网站。

## 预防

SQL 注入通常是由编码不当的 Web 应用程序或不安全的后端服务器和数据库权限引起的。稍后，我们将讨论通过安全编码方法（例如用户输入清理和验证以及适当的后端用户权限和控制）来减少易受 SQL 注入攻击的机会的方法。

# 数据库简介

在我们了解 SQL 注入之前，我们需要更多地了解数据库和结构化查询语言 (SQL)，哪些数据库将执行必要的查询。网络应用程序利用后端数据库来存储与网络应用程序相关的各种内容和信息。这可以是核心 Web 应用程序资产，如图像和文件、内容（如帖子和更新）或用户数据（如用户名和密码）。

有许多不同类型的数据库，每一种都适合特定的用途。传统上，应用程序使用基于文件的数据库，随着大小的增加，速度非常慢。`Database Management Systems`这导致了( )的采用`DBMS`。

## 数据库管理系统

数据库管理系统 (DBMS) 有助于创建、定义、托管和管理数据库。随着时间的推移，设计了各种 DBMS，例如基于文件、关系 DBMS (RDBMS)、NoSQL、基于图形和键/值存储。

有多种方式可以与 DBMS 交互，例如命令行工具、图形界面，甚至 API（应用程序编程接口）。DBMS 用于各种银行、金融和教育部门以记录大量数据。DBMS 的一些基本特征包括：

| **特征**                    | **描述**                                                     |
| --------------------------- | ------------------------------------------------------------ |
| `Concurrency`               | 一个真实世界的应用程序可能有多个用户同时与之交互。DBMS 确保这些并发交互成功而不会损坏或丢失任何数据。 |
| `Consistency`               | 对于如此多的并发交互，DBMS 需要确保数据在整个数据库中保持一致和有效。 |
| `Security`                  | DBMS 通过用户身份验证和权限提供细粒度的安全控制。这将防止未经授权查看或编辑敏感数据。 |
| `Reliability`               | 备份数据库并在数据丢失或破坏的情况下将它们回滚到以前的状态很容易。 |
| `Structured Query Language` | SQL 通过支持各种操作的直观语法简化了用户与数据库的交互。     |

### 结构

![](htb学院sql注入基础\2.png)

`Tier I`通常由客户端应用程序组成，例如网站或 GUI 程序。这些应用程序由高级交互组成，例如用户登录或评论。`Tier II`来自这些交互的数据通过 API 调用或其他请求传递。

第二层是中间件，它解释这些事件并将它们放入 DBMS 所需的格式中。最后，应用层根据 DBMS 的类型使用特定的库和驱动程序与它们进行交互。DBMS 从第二层接收查询并执行请求的操作。这些操作可能包括数据的插入、检索、删除或更新。处理后，DBMS 返回任何请求的数据或错误代码以应对无效查询。

可以在同一台主机上托管应用程序服务器和 DBMS。但是，具有支持许多用户的大量数据的数据库通常单独托管以提高性能和可伸缩性。

# 数据库类型

一般来说，数据库分为`Relational Databases`和`Non-Relational Databases`。只有关系数据库使用 SQL，而非关系数据库使用多种通信方法。

## 关系数据库

​	关系数据库是最常见的数据库类型。它使用模式、模板来指示存储在数据库中的数据结构。例如，我们可以想象一家向其客户销售产品的公司拥有关于这些产品的去向、销售对象和数量的某种形式的存储知识。然而，这通常是在后端完成的，而在前端没有明显的通知。每种方法都可以使用不同类型的关系数据库。例如，第一个表可以存储和显示基本的客户信息，第二个表可以存储销售的产品数量及其成本，第三个表可以列举谁购买了这些产品以及支付数据。

​	关系数据库中的表与提供快速数据库摘要或在需要查看特定数据时访问特定行或列的键相关联。这些表，也称为实体，都相互关联。例如，客户信息表可以为每个客户提供一个特定的 ID，该 ID 可以指示我们需要了解的有关该客户的所有信息，例如地址、姓名和联系信息。此外，产品描述表可以为每个产品分配一个特定的 ID。存储所有订单的表只需要记录这些 ID 及其数量。这些表格中的任何更改都会影响所有这些表格，但可以预见和系统地影响。

`relational database management system`但是，在处理集成数据库时，需要一个概念，即使用称为( )的键将一个表链接到另一个表`RDBMS`。许多最初使用不同概念的公司正在转向 RDBMS 概念，因为这个概念易于学习、使用和理解。最初，这个概念只被大公司使用。然而，许多类型的数据库现在都实现了 RDBMS 概念，例如 Microsoft Access、MySQL、SQL Server、Oracle、PostgreSQL 等等。

例如，我们可以`users`在关系数据库中有一个表，其中包含`id`、`username`、`first_name`、`last_name`和其他列。可以`id`用作表键。另一个表`posts`可能包含所有用户发布的帖子，列有`id`、`user_id`、`date`、`content`等。

![](htb学院sql注入基础\3.png)

​	我们可以将表中的`id`from链接`users`到`user_id`表中`posts`以检索每个帖子的用户详细信息，而无需存储每个帖子的所有用户详细信息。一个表可以有多个键，因为另一列可以用作键来链接另一个表。因此，例如，该`id`列可以用作将`posts`表链接到另一个包含评论的表的键，每个评论都属于一个特定的帖子，等等。

数据库中表之间的关系称为模式。

​	这样，通过使用关系数据库，可以快速轻松地从所有数据库中检索有关特定元素的所有数据。因此，例如，我们可以使用单个查询从所有表中检索链接到特定用户的所有详细信息。这使得关系数据库对于具有清晰结构和设计以及高效数据管理的大数据集非常快速和可靠。关系数据库最常见的示例是`MySQL`，我们将在本模块中介绍。

## 非关系数据库

非关系数据库（也称为`NoSQL`数据库）不使用表、行和列或主键、关系或模式。相反，NoSQL 数据库根据存储的数据类型使用各种存储模型来存储数据。由于没有为数据库定义的结构，NoSQL 数据库具有很强的可扩展性和灵活性。因此，在处理定义和结构不是很好的数据集时，NoSQL 数据库将是存储此类数据的最佳选择。NoSQL 数据库有四种常见的存储模型：

- 核心价值
- 基于文档
- 宽栏
- 图形

上述每个模型都有不同的数据存储方式。例如，该`Key-Value`模型通常以 JSON 或 XML 格式存储数据，每一对都有一个键，并将其所有数据存储为它的值：

![](htb学院sql注入基础\4.png)

上面的示例可以使用 JSON 表示为：

代码：json

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

​	它看起来类似于`Python`or `PHP`（即`{'key':'value'}`）等语言中的字典项，其中 the`key`通常是字符串，而 the`value`可以是字符串、字典或任何类对象。

NoSQL 数据库最常见的示例是`MongoDB`.

非关系数据库有一种不同的注入方法，称为 NoSQL 注入。SQL 注入与 NoSQL 注入完全不同。NoSQL 注入将在后面的模块中介绍。



# MySQL 简介

本模块通过 介绍 SQL 注入`MySQL`，了解更多有关`MySQL`SQL 的信息以了解 SQL 注入的工作原理并正确利用它们至关重要。因此，本节将介绍一些 MySQL/SQL 的基础知识和语法以及在 MySQL/MariaDB 数据库中使用的示例。

## 结构化查询语言 (SQL)

SQL 语法可能因一个 RDBMS 而异。但是，它们都必须遵循结构化查询语言的[ISO 标准](https://en.wikipedia.org/wiki/ISO/IEC_9075)。对于所示示例，我们将遵循 MySQL/MariaDB 语法。SQL 可用于执行以下操作：

- 检索数据
- 更新数据
- 删除数据
- 创建新表和数据库
- 添加/删除用户
- 为这些用户分配权限

## 命令行

该`mysql`实用程序用于对 MySQL/MariaDB 数据库进行身份验证并与之交互。该`-u`标志用于提供用户名和`-p`密码标志。该`-p`标志应为空，因此系统会提示我们输入密码并且不要直接在命令行上传递它，因为它可能以明文形式存储在 bash_history 文件中。

 

```shell-session
vnswer77@htb[/htb]$ mysql -u root -p

Enter password: <password>
...SNIP...

mysql> 
```

同样，也可以直接在命令中使用密码，但应避免这种情况，因为这可能导致密码保存在日志和终端历史记录中：

```shell-session
vnswer77@htb[/htb]$ mysql -u root -p<password>

...SNIP...

mysql> 
```

上面的例子让我们以超级用户的身份登录，即，“ `root`”和密码“ `password`，”拥有执行所有命令的权限。其他 DBMS 用户对他们可以执行的语句有一定的特权。我们可以使用稍后讨论的[SHOW GRANTS命令查看我们拥有哪些权限。](https://dev.mysql.com/doc/refman/8.0/en/show-grants.html)

当我们不指定主机时，它将默认为`localhost`服务器。`-h`我们可以使用和标志指定远程主机和端口`-P`。

```shell-session
vnswer77@htb[/htb]$ mysql -u root -h docker.hackthebox.eu -P 3306 -p 

Enter password: 
...SNIP...

mysql> 
```

注意：默认的 MySQL/MariaDB 端口为（3306），但可以配置为其他端口。它是使用大写的“P”指定的，这与用于密码的小写“p”不同。

注意：为了跟随示例，尝试使用 PwnBox 上的“mysql”工具登录到本节末尾问题中找到的 DBMS，使用其 IP 和端口。使用“root”作为用户名，使用“password”作为密码。

## 创建数据库

一旦我们使用该实用程序登录到数据库`mysql`，我们就可以开始使用 SQL 查询与 DBMS 进行交互。例如，可以使用[CREATE DATABASE](https://dev.mysql.com/doc/refman/5.7/en/create-database.html)语句在 MySQL DBMS 中创建一个新数据库。

```shell-session
mysql> CREATE DATABASE users;

Query OK, 1 row affected (0.02 sec)
```

MySQL 期望命令行查询以分号结束。上面的示例创建了一个名为`users`. [我们可以使用SHOW DATABASES](https://dev.mysql.com/doc/refman/8.0/en/show-databases.html)查看数据库列表，我们可以`users`使用以下`USE`语句切换到数据库：

```shell-session
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+

mysql> USE users;

Database changed
```

SQL 语句不区分大小写，这意味着“使用用户；” 和“使用用户”；参考相同的命令。但是，数据库名称区分大小写，因此我们不能执行“USE USERS;” 而不是“使用用户；”。因此，最好用大写字母指定语句以避免混淆。

## 表

​	DBMS 以表的形式存储数据。表格由水平行和垂直列组成。行和列的交集称为单元格。每个表都是用一组固定的列创建的，其中每一列都是一种特定的数据类型。

​	数据类型定义列要保存什么样的值。常见示例有`numbers`、`strings`、`date`、`time`和`binary data`。也可能有特定于 DBMS 的数据类型。[可以在此处](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)找到 MySQL 中数据类型的完整列表。例如，让我们使用[CREATE TABLE](https://dev.mysql.com/doc/refman/8.0/en/creating-tables.html)`logins` SQL 查询创建一个名为存储用户数据的表：

代码：sql

```sql
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
    );
```

​	如我们所见，`CREATE TABLE`查询首先指定表名，然后（在括号内）我们通过名称和数据类型指定每一列，所有列均以逗号分隔。在名称和类型之后，我们可以指定具体的属性，这将在后面讨论。

```shell-session
mysql> CREATE TABLE logins (
    ->     id INT,
    ->     username VARCHAR(100),
    ->     password VARCHAR(100),
    ->     date_of_joining DATETIME
    ->     );
Query OK, 0 rows affected (0.03 sec)
```

上面的 SQL 查询创建了一个`logins`以四列命名的表。第一列，`id`是一个整数。以下两列，每列`username`设置`password`为 100 个字符的字符串。任何超过此长度的输入都会导致错误。`date_of_joining`类型列存储`DATETIME`添加条目的日期。

 

```shell-session
mysql> SHOW TABLES;

+-----------------+
| Tables_in_users |
+-----------------+
| logins          |
+-----------------+
1 row in set (0.00 sec)
```

可以使用该语句获取当前数据库中的表列表`SHOW TABLES`。此外，[DESCRIBE](https://dev.mysql.com/doc/refman/8.0/en/describe.html)关键字用于列出表结构及其字段和数据类型。

```shell-session
mysql> DESCRIBE logins;

+-----------------+--------------+
| Field           | Type         |
+-----------------+--------------+
| id              | int          |
| username        | varchar(100) |
| password        | varchar(100) |
| date_of_joining | date         |
+-----------------+--------------+
4 rows in set (0.00 sec)
```

#### 表格属性

在查询中，可以为表和每一列设置`CREATE TABLE`许多[属性。](https://dev.mysql.com/doc/refman/8.0/en/create-table.html)例如，我们可以`id`使用关键字将列设置为自动递增`AUTO_INCREMENT`，每次向表中添加新项目时，id 自动递增 1：

代码：sql

```sql
    id INT NOT NULL AUTO_INCREMENT,
```

该`NOT NULL`约束确保特定列永远不会留空“即必填字段”。我们还可以使用`UNIQUE`约束来确保插入的项目始终是唯一的。例如，如果我们将它与`username`列一起使用，我们可以确保没有两个用户具有相同的用户名：

代码：sql

```sql
    username VARCHAR(100) UNIQUE NOT NULL,
```

另一个重要的关键字是`DEFAULT`关键字，用于指定默认值。例如，在`date_of_joining`列中，我们可以将默认值设置为[Now()](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_now)，这在 MySQL 中返回当前日期和时间：

代码：sql

```sql
    date_of_joining DATETIME DEFAULT NOW(),
```

最后，最重要的属性之一是`PRIMARY KEY`，我们可以使用它来唯一标识表中的每条记录，引用关系数据库表中记录的所有数据，如前一节所述。我们可以为该表创建`id`列：`PRIMARY KEY`

代码：sql

```sql
    PRIMARY KEY (id)
```

最终`CREATE TABLE`查询将如下所示：

代码：sql

```sql
CREATE TABLE logins (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    date_of_joining DATETIME DEFAULT NOW(),
    PRIMARY KEY (id)
    );
```

# 练习

mysql -h 46.101.81.60 -P 30450 -u root -ppassword; 登录远程

![](htb学院sql注入基础\5.png)

# SQL语句

现在我们了解了如何使用该`mysql`实用程序以及如何创建数据库和表，让我们看看一些基本的 SQL 语句及其用途。

## 插入语句

[INSERT](https://dev.mysql.com/doc/refman/8.0/en/insert.html)语句用于向给定表添加新记录。遵循以下语法的语句：

代码：sql

```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```

上面的语法要求用户为表中存在的所有列填写值。

```shell-session
mysql> INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');

Query OK, 1 row affected (0.00 sec)
```

上面的示例显示了如何向登录表添加新登录，并为每一列添加适当的值。但是，我们可以跳过使用默认值填充列，例如`id`和`date_of_joining`。这可以通过指定列名以选择性地将值插入表中来完成：

代码：sql

```sql
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);
```

注意：跳过带有“NOT NULL”约束的列将导致错误，因为它是必需的值。

我们可以做同样的事情来将值插入表中`logins`：

```shell-session
mysql> INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');

Query OK, 1 row affected (0.00 sec)
```

我们在上面的示例中插入了一个用户名-密码对，同时跳过了`id`和`date_of_joining`列。

注意：示例将明文密码插入表中，仅用于演示。这是一个不好的做法，因为密码在存储之前应该总是被散列/加密。

我们还可以一次插入多条记录，方法是用逗号分隔它们：

```shell-session
mysql> INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');

Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

上面的查询一次插入了两条新记录。

## 选择语句

现在我们已经将数据插入到表中，让我们看看如何使用[SELECT](https://dev.mysql.com/doc/refman/8.0/en/select.html)语句检索数据。这个语句也可以用于许多其他目的，我们稍后会遇到。查看整个表的一般语法如下：

代码：sql

```sql
SELECT * FROM table_name;
```

星号 (*) 用作通配符并选择所有列。关键字`FROM`用于表示要从中选择的表。也可以查看特定列中的数据：

代码：sql

```sql
SELECT column1, column2 FROM table_name;
```

上面的查询将仅选择 column1 和 column2 中存在的数据。

```shell-session
mysql> SELECT * FROM logins;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)


mysql> SELECT username,password FROM logins;

+---------------+------------+
| username      | password   |
+---------------+------------+
| admin         | p@ssw0rd   |
| administrator | adm1n_p@ss |
| john          | john123!   |
| tom           | tom123!    |
+---------------+------------+
4 rows in set (0.00 sec)
```

上面示例中的第一个查询查看登录表中存在的所有记录。我们可以看到之前输入的四条记录。第二个查询只选择用户名和密码列，而跳过其他两个

## 删除声明

我们可以使用[DROP](https://dev.mysql.com/doc/refman/8.0/en/drop-table.html)从服务器中删除表和数据库。

```shell-session
mysql> DROP TABLE logins;

Query OK, 0 rows affected (0.01 sec)


mysql> SHOW TABLES;

Empty set (0.00 sec)
```

如我们所见，该表已被完全删除。

'DROP' 语句将在没有确认的情况下永久彻底删除表，因此应谨慎使用。

## 改变声明

最后，我们可以使用[ALTER](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html)更改任何表及其任何字段的名称，或者删除现有表或向现有表添加新列。

下面的示例使用以下方法`newColumn`向表中添加一个新列：`logins``ADD`

```shell-session
mysql> ALTER TABLE logins ADD newColumn INT;

Query OK, 0 rows affected (0.01 sec)
```

要重命名列，我们可以使用`RENAME COLUMN`：

```shell-session
mysql> ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn;

Query OK, 0 rows affected (0.01 sec)
```

我们还可以更改列的数据类型`MODIFY`：

```shell-session
mysql> ALTER TABLE logins MODIFY oldColumn DATE;

Query OK, 0 rows affected (0.01 sec)
```

最后，我们可以使用删除一列`DROP`：

```shell-session
mysql> ALTER TABLE logins DROP oldColumn;

Query OK, 0 rows affected (0.01 sec)
```

只要我们有足够的权限，我们就可以对任何现有表使用上述任何语句。

## 更新声明

虽然`ALTER`用于更改表的属性，但[UPDATE](https://dev.mysql.com/doc/refman/8.0/en/update.html)语句可用于根据特定条件更新表中的特定记录。它的一般语法是：

代码：sql

```sql
UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;
```

我们指定表名、每一列及其新值，以及更新记录的条件。让我们看一个例子：

 

```shell-session
mysql> UPDATE logins SET password = 'change_password' WHERE id > 1;

Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0


mysql> SELECT * FROM logins;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:47:16 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)
```

​	上面的查询更新了 id 比 1 更重要的所有记录中的所有密码。

​	注意：我们必须使用 UPDATE 指定“WHERE”子句，以指定更新哪些记录。接下来将讨论“WHERE”子句。

# 查询结果

在本节中，我们将学习如何控制任何查询的结果输出。

## 排序结果

[我们可以使用ORDER BY](https://dev.mysql.com/doc/refman/8.0/en/order-by-optimization.html)并指定要排序的列来对任何查询的结果进行排序：

```shell-session
mysql> SELECT * FROM logins ORDER BY password;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)
```

默认情况下，排序是按升序进行的，但我们也可以按`ASC`or对结果进行排序`DESC`：

```shell-session
mysql> SELECT * FROM logins ORDER BY password DESC;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)
```

也可以按多列排序，对一列中的重复值进行二次排序：

```shell-session
mysql> SELECT * FROM logins ORDER BY password DESC, id ASC;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:50:20 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)
```

## 限制结果

如果我们的查询返回大量记录，我们可以将结果[限制](https://dev.mysql.com/doc/refman/8.0/en/limit-optimization.html)`LIMIT`为我们想要的，使用我们想要的记录数：

```shell-session
mysql> SELECT * FROM logins LIMIT 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

如果我们想用偏移量限制结果，我们可以在 LIMIT 计数之前指定偏移量：

```shell-session
mysql> SELECT * FROM logins LIMIT 1, 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

注意：偏移量标记了要包含的第一条记录的顺序，从0开始。对于上面，它开始并包含第2条记录，并返回两个值。

## WHERE 子句

要过滤或搜索特定数据，我们可以使用条件和`SELECT`使用[WHERE](https://dev.mysql.com/doc/refman/8.0/en/where-optimization.html)子句的语句来微调结果：

代码：sql

```sql
SELECT * FROM table_name WHERE <condition>;
```

上面的查询将返回满足给定条件的所有记录。让我们看一个例子：

```shell-session
mysql> SELECT * FROM logins WHERE id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)
```

上面的示例选择了 的值`id`大于 的所有记录`1`。如我们所见，`id`输出中跳过了 as 1 的第一行。我们可以为用户名做类似的事情：

```shell-session
mysql> SELECT * FROM logins where username = 'admin';

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
+----+----------+----------+---------------------+
1 row in set (0.00 sec)
```

上面的查询选择了用户名为 的记录`admin`。我们可以使用`UPDATE`语句来更新满足特定条件的某些记录。

注意：字符串和日期数据类型需要用单引号（'）或双引号（"）括起来，而数字可以直接使用。

## 喜欢条款

另一个有用的 SQL 子句是[LIKE](https://dev.mysql.com/doc/refman/8.0/en/pattern-matching.html)，它可以通过匹配特定模式来选择记录。下面的查询检索用户名以 开头的所有记录`admin`：

```shell-session
mysql> SELECT * FROM logins WHERE username LIKE 'admin%';

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | administrator | adm1n_p@ss | 2020-07-02 15:19:02 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

该`%`符号充当通配符并匹配 之后的所有字符`admin`。它用于匹配零个或多个字符。同样，该`_`符号用于精确匹配一个字符。下面的查询匹配所有用户名中只有三个字符，在本例中是`tom`：

 

```shell-session
mysql> SELECT * FROM logins WHERE username like '___';

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  3 | tom      | tom123!  | 2020-07-02 15:18:56 |
+----+----------+----------+---------------------+
1 row in set (0.01 sec)
```

# SQL 运算符

有时，具有单一条件的表达式并不能满足用户的要求。为此，SQL 支持[逻辑运算符](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html)一次使用多个条件。最常见的逻辑运算符是`AND`、`OR`和`NOT`。

## AND运算符

运算`AND`符接受两个条件并根据它们的评估返回`true`或：`false`

代码：sql

```sql
condition1 AND condition2
```

操作的结果`AND`是`true`当且仅当两者`condition1`都`condition2`评估为`true`：

```shell-session
mysql> SELECT 1 = 1 AND 'test' = 'test';

+---------------------------+
| 1 = 1 AND 'test' = 'test' |
+---------------------------+
|                         1 |
+---------------------------+
1 row in set (0.00 sec)

mysql> SELECT 1 = 1 AND 'test' = 'abc';

+--------------------------+
| 1 = 1 AND 'test' = 'abc' |
+--------------------------+
|                        0 |
+--------------------------+
1 row in set (0.00 sec)
```

在 MySQL 术语中，任何`non-zero`值都被认为是`true`，它通常将值返回`1`给 signify `true`。`0`被认为`false`。正如我们在上面的示例中看到的，第一个查询返回`true`，因为两个表达式都被评估为`true`. `false`但是，作为第二个条件返回的第二个查询`'test' = 'abc'`是`false`。

## 或运算符

该`OR`运算符也接受两个表达式，并`true`在其中至少一个表达式的计算结果为时返回`true`：

```shell-session
mysql> SELECT 1 = 1 OR 'test' = 'abc';

+-------------------------+
| 1 = 1 OR 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT 1 = 2 OR 'test' = 'abc';

+-------------------------+
| 1 = 2 OR 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set (0.00 sec)
```

上面的查询演示了`OR`运算符的工作原理。`true`评估为条件的第一个查询`1 = 1`是`true`。第二个查询有两个`false`条件，导致`false`输出。

## 非运算符

运算`NOT`符简单地切换一个`boolean`值“即`true`转换为`false`，反之亦然”： 

```shell-session
mysql> SELECT NOT 1 = 1;

+-----------+
| NOT 1 = 1 |
+-----------+
|         0 |
+-----------+
1 row in set (0.00 sec)

mysql> SELECT NOT 1 = 2;

+-----------+
| NOT 1 = 2 |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)
```

​	如上面的示例所示，第一个查询结果是`false`因为它是 的求值的逆运算`1 = 1`，即`true`，所以它的逆运算是`false`。另一方面，第二个是返回的查询，因为“which is ”`true`的倒数是。`1 = 2``false``true`

## 符号运算符

,`AND`和运算`OR`符`NOT`也可以分别表示为`&&`,`||`和`!`。下面是相同的前面的例子，通过使用符号运算符：

```shell-session
mysql> SELECT 1 = 1 && 'test' = 'abc';

+-------------------------+
| 1 = 1 && 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT 1 = 1 || 'test' = 'abc';

+-------------------------+
| 1 = 1 || 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT 1 != 1;

+--------+
| 1 != 1 |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)
```

## 查询中的运算符

让我们看看如何在查询中使用这些运算符。以下查询列出了不是的所有`username`记录`john`：

```shell-session
mysql> SELECT * FROM logins WHERE username != 'john';

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)
```

`id`下一个查询选择大于且不`1`等于`username`的用户`john`：

```shell-session
mysql> SELECT * FROM logins WHERE username != 'john' AND id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

## 多个运算符优先级

SQL 支持各种其他操作，例如加法、除法和位运算。因此，一个查询可以同时包含多个表达式和多个操作。这些操作的顺序由运算符优先级决定。

这是常见操作及其优先级的列表，如[MariaDB 文档](https://mariadb.com/kb/en/operator-precedence/)中所示：

- 除法 ( `/`)、乘法 ( `*`) 和模数 ( `%`)
- 加法 ( `+`) 和减法 ( `-`)
- 比较 ( `=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
- 不是 ( `!`)
- 与 ( `&&`)
- 或 ( `||`)

顶部的操作在列表底部的操作之前进行评估。让我们看一个例子：

代码：sql

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;
```

该查询有四种操作：`!=`、`AND`、`>`和`-`。从运算符的优先级，我们知道减法在前，所以它首先计算`3 - 2`为`1`：

代码：sql

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 1;
```

接下来，我们有两个比较操作，`>`和`!=`。这两个具有相同的优先级，将一起评估。所以，它会返回所有用户名不为 的记录`tom`，以及所有大于`id`1 的记录，然后申请`AND`返回所有满足这两个条件的记录：

```shell-session
mysql> select * from logins where username != 'tom' AND id > 3 - 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-03 12:03:53 |
|  3 | john          | john123!   | 2020-07-03 12:03:57 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

在接下来的部分中，我们将看到一些其他的运算符优先级场景。

## SQL 注入简介

现在我们对 MySQL 和 SQL 查询的工作原理有了一个大致的了解，让我们了解 SQL 注入。

### 在 Web 应用程序中使用 SQL

首先，让我们看看 Web 应用程序如何使用 MySQL 数据库来存储和检索数据。一旦在后端服务器上安装并设置了 DBMS 并启动并运行，Web 应用程序就可以开始利用它来存储和检索数据。

例如，在`PHP`Web 应用程序中，我们可以连接到我们的数据库，并`MySQL`通过`MySQL`语法开始使用数据库，就在 中`PHP`，如下所示：

代码：php

```php
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);
```

​	然后，查询的输出将存储在 中`$result`，我们可以将其打印到页面或以任何其他方式使用它。下面的 PHP 代码将在新行中打印 SQL 查询的所有返回结果：

代码：php

```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}
```

​	Web 应用程序在检索数据时通常也使用用户输入。例如，当用户使用搜索功能搜索其他用户时，他们的搜索输入将传递给 Web 应用程序，该应用程序使用输入在数据库中进行搜索：

代码：php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
If we use user-input within an SQL query, and if not securely coded, it may cause a variety of issues, like SQL Injection vulnerabilities.
```

### 什么是注入？

​	在上面的示例中，我们接受用户输入并将其直接传递给 SQL 查询而不进行清理。

​	清理是指删除用户输入中的任何特殊字符，以中断任何注入尝试。

​	当应用程序将用户输入错误解释为实际代码而不是字符串时，就会发生注入，从而更改代码流并执行它。这可以通过注入特殊字符（如 ( `'`)）来转义用户输入边界，然后编写要执行的代码（如 JavaScript 代码或 SQL 注入中的 SQL）来实现。除非对用户输入进行清理，否则很可能会执行注入的代码并运行它。

### SQL注入

当用户输入被输入到 SQL 查询字符串中而没有正确清理或过滤输入时，就会发生 SQL 注入。前面的示例展示了如何在 SQL 查询中使用用户输入，并且它没有使用任何形式的输入清理：

代码：php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```

在典型情况下，`searchInput`将输入 以完成查询，返回预期结果。我们键入的任何输入都会进入以下 SQL 查询：

代码：sql

```sql
select * from logins where username like '%$searchInput'
```

​	所以，如果我们输入`admin`，它变成`'%admin'`。在这种情况下，如果我们编写任何 SQL 代码，它只会被视为搜索词。例如，如果我们输入`SHOW DATABASES;`，它将被执行为`'%SHOW DATABASES;'`Web 应用程序将搜索类似于 的用户名`SHOW DATABASES;`。

​	但是，由于没有清理，在这种情况下，**我们可以添加一个单引号 ( `'`)，它将结束用户输入字段，然后我们可以编写实际的 SQL 代码**。例如，如果我们搜索`1'; DROP TABLE users;`，搜索输入将是：

代码：php

```php
'%1'; DROP TABLE users;'
```

请注意我们如何在“1”之后添加单引号 (')，以避开用户输入 ('%$searchInput') 的范围。

因此，最终执行的 SQL 查询如下：

代码：sql

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

​	正如我们从语法突出显示中看到的那样，我们可以转义原始查询的边界并执行新注入的查询。`Once the query is run, the `用户` table will get deleted.`

​	注意：在上面的示例中，为了简单起见，我们在分号（;）之后添加了另一个 SQL 查询。虽然这对于 MySQL 实际上是不可能的，但对于 MSSQL 和 PostgreSQL 是可能的。在接下来的部分中，我们将讨论在 MySQL 中注入 SQL 查询的真正方法。

## 语法错误

前面的 SQL 注入示例将返回错误：

代码：php

```php
Error: near line 1: near "'": syntax error
```

这是因为最后一个尾随字符，我们有一个`'`没有关闭的额外引号 ()，这会导致执行时出现 SQL 语法错误：

代码：sql

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

​	在这种情况下，我们只有一个尾随字符，因为我们的搜索查询输入接近 SQL 查询的末尾。然而，用户输入通常在 SQL 查询的中间，原始 SQL 查询的其余部分在它之后。

​	要成功注入，我们必须确保新修改的 SQL 查询在注入后仍然有效并且没有任何语法错误。在大多数情况下，我们无法访问源代码来查找原始 SQL 查询并开发适当的 SQL 注入来进行有效的 SQL 查询。那么，我们如何才能成功地注入到 SQL 查询中呢？

​	一个答案是使用`comments`，我们将在后面的部分讨论这个问题。另一种方法是通过传入多个单引号使查询语法起作用，我们将在接下来讨论 ( `'`)。

​	现在我们了解了 SQL 注入的基础知识，让我们开始学习一些实际用途。

## SQL注入的类型

SQL 注入根据我们检索其输出的方式和位置进行分类。

![](htb学院sql注入基础\6.png)

​	在简单的情况下，预期和新查询的输出可能会直接打印在前端，我们可以直接读取。这称为`In-band`SQL 注入，它有两种类型：`Union Based`和`Error Based`。

使用`Union Based`SQL 注入，我们可能必须指定我们可以读取的确切位置，即“列”，以便查询将输出定向到那里打印。至于SQL 注入，当我们可以在前端`Error Based`获取`PHP`或错误时使用它，因此我们可能会故意导致返回查询输出的 SQL 错误。`SQL`

在更复杂的情况下，我们可能无法打印输出，因此我们可以利用 SQL 逻辑逐个字符地检索输出。这称为`Blind`SQL 注入，它也有两种类型：`Boolean Based`和`Time Based`。

通过`Boolean Based`SQL 注入，我们可以使用 SQL 条件语句来控制页面是否返回任何输出，“即原始查询响应”，如果我们的条件语句返回`true`。对于`Time Based`SQL 注入，我们使用 SQL 条件语句，如果条件语句`true`使用`Sleep()`函数返回，则延迟页面响应。

最后，在某些情况下，我们可能无法直接访问输出，因此我们可能必须将输出定向到远程位置，“即 DNS 记录”，然后尝试从那里检索它。这称为`Out-of-band`SQL 注入。

在本模块中，我们将只专注于通过学习 SQL 注入来介绍`Union Based`SQL 注入。

# 颠覆查询逻辑

现在我们对 SQL 语句的工作原理有了基本的了解，让我们开始 SQL 注入。在我们开始执行整个 SQL 查询之前，我们将首先学习通过注入运算符`OR`和使用 SQL 注释来颠覆原始查询逻辑来修改原始查询。这方面的一个基本示例是绕过 Web 身份验证，我们将在本节中对此进行演示。

## 身份验证绕过

考虑以下管理员登录页面。

![](htb学院sql注入基础\7.png)

我们可以使用管理员凭据登录`admin / p@ssw0rd`。

![](htb学院sql注入基础\8.png)

该页面还显示了正在执行的 SQL 查询，以便更好地了解我们将如何颠覆查询逻辑。我们的目标是在不使用现有密码的情况下以管理员用户身份登录。正如我们所见，当前正在执行的 SQL 查询是：

代码：sql

```sql
SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';
```

​	该页面接收凭据，然后使用`AND`运算符选择与给定用户名和密码匹配的记录。如果`MySQL`数据库返回匹配的记录，则凭据有效，

因此代码`PHP`会将登录尝试条件评估为`true`。如果条件评估为`true`，则返回管理员记录，并验证我们的登录。让我们看看当我们输入错误的凭据时会发生什么。

![](htb学院sql注入基础\9.png)

不出所料，由于密码错误导致`false`操作结果导致登录失败`AND`。

## SQLi 发现

​	在我们开始破坏 Web 应用程序的逻辑并试图绕过身份验证之前，我们首先必须测试登录表单是否容易受到 SQL 注入的攻击。为此，我们将尝试在我们的用户名后添加以下有效负载之一，看看它是否会导致任何错误或改变页面的行为方式：

| 有效载荷 | 网址编码 |
| -------- | -------- |
| `'`      | `%27`    |
| `"`      | `%22`    |
| `#`      | `%23`    |
| `;`      | `%3B`    |
| `)`      | `%29`    |

​	注意：在某些情况下，我们可能必须使用负载的 URL 编码版本。这方面的一个例子是当我们将我们的有效负载直接放在 URL“即 HTTP GET 请求”中时。

​	因此，让我们从注入单引号开始：

![](htb学院sql注入基础\10.png)

我们看到抛出了 SQL 错误而不是`Login Failed`消息。该页面引发错误，因为生成的查询是：

代码：sql

```sql
SELECT * FROM logins WHERE username=''' AND password = 'something';
```

​	我们输入的引号导致了奇数个引号，从而导致语法错误。一种选择是注释掉查询的其余部分，并将查询的其余部分作为我们注入的一部分来编写，以形成一个有效的查询。另一种选择是在我们注入的查询中使用偶数个引号，这样最终的查询仍然有效。

## 或注射

​	`true`无论输入的用户名和密码如何，我们都需要查询始终返回，以绕过身份验证。为此，我们可以`OR`在 SQL 注入中滥用运算符。

`AND`操作符将在操作符之前被评估`OR`。`TRUE`这意味着如果整个查询中至少有一个条件和一个`OR`运算符，则整个查询的计算结果将是 to `TRUE`，因为如果其操作数之一是 ，`OR`则运算符返回。`TRUE``TRUE`

​	始终返回的条件示例`true`是`'1'='1'`。但是，为了保持 SQL 查询正常工作并保持偶数个引号，而不是使用 ('1'='1')，

​	我们将删除最后一个引号并使用 ('1'='1)，因此剩下的单引号原始查询中的引用将取而代之。

​	因此，如果我们注入以下条件并`OR`在它和原始条件之间有一个运算符，它应该总是返回`true`：

代码：sql

```sql
admin' or '1'='1
```

最终查询应如下所示：

代码：sql

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```

这意味着以下内容：

- 如果用户名是`admin`
  `OR`
- 如果`1=1`返回`true`'总是返回`true`'
  `AND`
- 如果密码是`something`

![](htb学院sql注入基础\11.png)

运算`AND`符将首先被评估，然后返回`false`。然后，`OR`运算符将被评估，如果其中一个语句是`true`，它将返回`true`。由于`1=1`总是返回`true`，此查询将返回`true`，并且它会授予我们访问权限。

注意：我们上面使用的有效载荷是我们可以用来破坏身份验证逻辑的众多身份验证绕过有效载荷之一。[您可以在PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL Injection#authentication-bypass)中找到完整的 SQLi 身份验证绕过负载列表，每个负载都适用于特定类型的 SQL 查询。

## 使用 OR 运算符绕过身份验证

让我们尝试将此作为用户名并查看响应。

![](htb学院sql注入基础\12.png)

我们能够以管理员身份成功登录。但是，如果我们不知道有效的用户名怎么办？这次让我们用不同的用户名尝试相同的请求。

![](htb学院sql注入基础\13.png)

登录失败，因为`notAdmin`表中不存在，导致整体查询错误。

![](htb学院sql注入基础\14.png)

要再次成功登录，我们需要一个整体`true`查询。这可以通过`OR`在密码字段中注入一个条件来实现，因此它总是会返回`true`。让我们尝试`something' or '1'='1`作为密码。

![](htb学院sql注入基础\15.png)

附加`OR`条件导致`true`整体查询，因为该`WHERE`子句返回表中的所有内容，并且第一行中的用户已登录。在这种情况下，由于两个条件都将返回，`true`我们不必提供测试用户名和密码，可以直接开始注入`'`，直接用`' or '1' = '1`.

![](htb学院sql注入基础\16.png)

这是有效的，因为查询的计算结果与`true`用户名或密码无关。

### 练习

尝试以用户“tom”身份登录。登录成功后显示的flag值是多少？

tom'or '1' ='1

# 使用注释

## 注释

​	就像任何其他语言一样，SQL 也允许使用注释。注释用于记录查询或忽略查询的特定部分。除了内联注释外，我们还可以在 MySQL`-- `和中使用两种类型的行注释（尽管这通常不用于 SQL 注入）。可以按如下方式使用：`#``/**/``--`

```shell-session
mysql> SELECT username FROM logins; -- Selects usernames from the logins table 

+---------------+
| username      |
+---------------+
| admin         |
| administrator |
| john          |
| tom           |
+---------------+
4 rows in set (0.00 sec)
```

​	注意：在 SQL 中，仅使用两个破折号不足以开始注释。所以，它们后面必须有一个空格，所以注释以 (-- ) 开头，末尾有一个空格。有时 URL 编码为 (--+)，因为 URL 中的空格被编码为 (+)。为清楚起见，我们将在末尾 (-- -) 添加另一个 (-)，以显示空格字符的使用。

符号`#`也可以使用。

 

```shell-session
mysql> SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
+----+----------+----------+---------------------+
1 row in set (0.00 sec)
```

​	提示：如果您在浏览器的 URL 中输入有效负载，(#) 符号通常被视为标记，不会作为 URL 的一部分传递。为了在浏览器中使用 (#) 作为注释，我们可以使用 '%23'，这是一个 URL 编码 (#) 符号。

服务器将在评估期间忽略查询的部分`AND password = 'something'`。

## 带有注释的身份验证绕过

让我们回到我们之前的例子并注入`admin'-- `我们的用户名。最终查询将是：

代码：sql

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
```

正如我们从语法突出显示中看到的那样，用户名现在是`admin`，查询的其余部分现在作为注释被忽略。此外，通过这种方式，我们可以确保查询没有任何语法问题。

`admin'-- `让我们尝试在登录页面上使用这些，并使用用户名和任何密码登录：

![](htb学院sql注入基础\17.png)

如我们所见，我们能够绕过身份验证，因为新修改的查询会检查用户名，没有其他条件。

## 另一个例子

如果应用程序需要先检查特定条件，SQL 支持使用括号。括号内的表达式优先于其他运算符并首先计算。让我们来看这样一个场景：

![](htb学院sql注入基础\18.png)

​	上面的查询确保用户的 id 总是大于 1，这将阻止任何人以管理员身份登录。此外，我们还看到密码在用于查询之前已经过哈希处理。这将阻止我们通过密码字段进行注入，因为输入已更改为哈希。

让我们尝试使用有效凭据登录`admin / p@ssw0rd`以查看响应。

![](htb学院sql注入基础\19.png)

​	正如预期的那样，即使我们提供了有效的凭据，登录也会失败，因为管理员的 ID 等于 1。因此让我们尝试使用另一个用户的凭据登录，例如`tom`。

![](htb学院sql注入基础\20.png)

​	以 id 不等于 1 的用户身份登录成功。那么，我们如何以管理员身份登录呢？从前面关于评论的部分我们知道我们可以使用它们来评论查询的其余部分。

​	因此，让我们尝试使用`admin'-- `as 用户名。

![](htb学院sql注入基础\22.png)

由于语法错误，登录失败，因为关闭的没有平衡开括号。要成功执行查询，我们必须添加一个右括号。让我们尝试使用用户名`admin')-- `关闭并注释掉其余部分。

![](htb学院sql注入基础\23.png)

查询成功，我们以管理员身份登录。我们输入的最终查询是：

代码：sql

```sql
SELECT * FROM logins where (username='admin')
```

上面的查询类似于上一个示例中的查询，并返回包含 admin 的行。

## 练习

以 ID 为 5 的用户身份登录以获取标志。

```
' or id = 5 )#
```

# Union Clause

我们只使用OR操作符和注释来操纵原始查询来颠覆web应用程序逻辑并绕过身份验证。然而，另一种类型的SQL注入是注入与原始查询一起执行的整个SQL查询。本节将通过使用MySQL Union子句执行SQL Union注入来演示这一点。

## Union

在开始学习联合注入之前，我们应该首先了解更多关于SQL Union子句的知识。[Union](https://dev.mysql.com/doc/refman/8.0/en/union.html)子句用于组合多个' SELECT '语句的结果。这意味着通过UNION注入，我们将能够在整个DBMS中，从多个表和数据库中，“SELECT”和转储数据。让我们尝试在示例数据库中使用“UNION”操作符。首先，让我们看看' ports '表的内容:

```shell-session
SELECT * FROM ports UNION SELECT * FROM ships;
```

正如我们所看到的，UNION将两个SELECT语句的输出合并为一个语句，因此来自ports表和ships表的条目被合并为包含四行的单个输出。正如我们所看到的，一些行属于ports表，而另一些属于ships表。

## Even Columns

UNION语句只能对具有相同列数的SELECT语句进行操作。

For example, if the query is:

```sql
SELECT * FROM products WHERE product_id = 'user_input'
```

我们可以在输入中注入一个' UNION '查询，这样就会返回另一个表中的行:

```sql
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```

假设“products”表有两列，上面的查询将返回“password”表中的“username”和“password”条目。

## Un-even Columns

​	我们将发现，原始查询的列数通常与我们想要执行的SQL查询的列数不同，因此我们必须解决这个问题。例如，假设我们只有一列。

​	在这种情况下，我们想要SELECT，我们可以为剩余的必需列放置垃圾数据，以便我们要联合的列总数与原始查询保持相同。例如，我们可以使用任何字符串作为垃圾数据，查询将返回该字符串作为该列的输出。

​	如果我们使用字符串“junk”进行UNION, SELECT查询将从密码中SELECT“junk”，这将始终返回垃圾。

​	我们也可以用数字。例如，对密码的查询SELECT 1将始终返回1作为输出。

​	注意:当用垃圾数据填充其他列时，必须确保数据类型与列的数据类型匹配，否则查询将返回错误。为了简单起见，我们将使用数字作为垃圾数据，这对于跟踪有效载荷位置也很方便，我们将在后面讨论。

​	提示:对于高级SQL注入，我们可能希望简单地使用'NULL'填充其他列，因为'NULL'适合所有数据类型。

​	在上面的示例中，products表有两列，因此我们必须使用两列进行UNION。如果我们只想获得一列，例如username，我们必须执行username, 2，这样我们就有相同数量的列:

```sql
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```

​	如果在原始查询的表中有更多的列，我们必须添加更多的数字来创建剩余的所需列。例如，如果最初的查询在一个有四列的表上使用了SELECT，那么我们的UNION注入将是:

```sql
UNION SELECT username, 2, 3, 4 from passwords-- '
```

 这个查询将返回:

```shell-session
mysql> SELECT * from products where product_id UNION SELECT username, 2, 3, 4 from passwords-- '

+-----------+-----------+-----------+-----------+
| product_1 | product_2 | product_3 | product_4 |
+-----------+-----------+-----------+-----------+
|   admin   |    2      |    3      |    4      |
+-----------+-----------+-----------+-----------+
```

正如我们所看到的，我们想要的“UNION SELECT username from passwords”查询的输出位于第二行的第一列，而数字则填充在其余列中。

# Union Injection

我们在搜索参数中看到潜在的SQL注入。我们通过注入一个单引号(')来应用SQLi Discovery步骤，我们确实得到了一个错误:



由于我们导致了一个错误，这可能意味着该页面容易受到SQL注入的攻击。这个场景非常适合通过基于union的注入进行利用，因为我们可以看到查询的结果。

## 检测列数

在继续使用基于union的查询之前，我们需要找到服务器选择的列的数量。有两种检测列数的方法:

- Using `ORDER BY`
- Using `UNION`

​	检测列数的第一种方法是通过ORDER BY函数，我们在前面讨论过。我们必须注入一个查询，该查询按照我们指定的列(即列1、列2，等等)对结果进行排序，直到我们得到一个错误，指出指定的列不存在。例如，我们可以从按1排序开始，按第一列排序，并且成功，因为表必须至少有一列。

​	然后我们将按2排序，然后按3排序，直到我们到达一个返回错误的数字，或者页面没有显示任何输出，这意味着该列号不存在。

​	我们成功排序的最后一个成功列给出了列的总数。如果按4排序失败，这意味着表有三列，这是我们能够成功排序的列数。让我们回到前面的例子并尝试相同的方法，使用以下有效负载:

```sql
' order by 1-- -
```

提醒:我们在末尾添加了一个额外的破折号(-)，以表明(——)后面有一个空格。

如我们所见，我们得到了一个正常的结果:



我们对第3列和第4列做同样的处理，得到结果。然而，当我们尝试ORDER BY第5列时，我们得到以下错误:



这意味着这个表恰好有4列。

另一种方法是尝试使用不同数量的列进行Union注入，直到成功返回结果。第一个方法总是返回结果，直到我们遇到错误，而这个方法总是给出一个错误，直到我们获得成功。我们可以从注入一个3列的UNION查询开始:

```sql
cn' UNION select 1,2,3-- -
```

## 注射位置

​	虽然查询可能返回多个列，但 Web 应用程序可能只显示其中的一些列。因此，如果我们将查询注入到页面上未打印的列中，我们将无法获得其输出。这就是为什么我们需要确定将哪些列打印到页面上，以确定在何处放置注入。

​	在前面的示例中，虽然注入的查询返回 1、2、3 和 4，但我们只看到 2、3 和 4 作为输出数据显示在页面上：

​	并非每一列都会显示给用户，这是很常见的。例如，ID字段经常用于将不同的表链接在一起，但用户不需要看到它。这告诉我们打印了第 2 列、第 3 列和第 4 列，以便将我们的注入放入其中的任何一个中。我们不能在开始时输入注射,或者它的输出不会打印出来。

​	这是使用数字作为垃圾数据的好处，因为它可以轻松跟踪打印了哪些列，因此我们知道在哪一列中放置查询。为了测试我们是否可以从数据库中获取实际数据“而不仅仅是数字”，我们可以使用 SQL`@@version`查询作为测试，并将其放在第二列而不是数字 2 中：

```
cn' UNION select 1,@@version,3,4-- -
```

# 数据库枚举

## MySQL 指纹识别

​	在枚举数据库之前，我们通常需要确定我们正在处理的 DBMS 的类型。这是因为每个 DBMS 都有不同的查询，了解它是什么将帮助我们知道要使用哪些查询。

​	作为初步猜测，如果我们在 HTTP 响应中看到的 Web 服务器是`Apache`或`Nginx`，则可以很好地猜测该 Web 服务器在 Linux 上运行，因此 DBMS 很可能是`MySQL`。

​	如果网络服务器是的话，这同样适用于 Microsoft DBMS `IIS`，所以很可能是这样`MSSQL`。然而，这是一个牵强的猜测，因为许多其他数据库可以在操作系统或 Web 服务器上使用。

​	因此，我们可以测试不同的查询来识别我们正在处理的数据库类型。

​	正如我们`MySQL`在本模块中介绍的那样，让我们对`MySQL`数据库进行指纹识别。以下查询及其输出将告诉我们正在处理`MySQL`：

| 有效载荷           | 何时使用                 | 预期输出                                  | 输出错误                                         |
| ------------------ | ------------------------ | ----------------------------------------- | ------------------------------------------------ |
| `SELECT @@version` | 当我们有完整的查询输出时 | MySQL 版本“ie `10.3.22-MariaDB-1ubuntu1`” | 在 MSSQL 中，它返回 MSSQL 版本。其他 DBMS 出错。 |
| `SELECT POW(1,1)`  | 当我们只有数字输出时     | `1`                                       | 其他 DBMS 错误                                   |
| `SELECT SLEEP(5)`  | 盲/无输出                | 延迟页面响应 5 秒并返回`0`。              | 不会延迟与其他 DBMS 的响应                       |

​	正如我们在上一节的示例中看到的，当我们尝试时`@@version`，它给了我们：

​	输出`10.3.22-MariaDB-1ubuntu1`意味着我们正在处理`MariaDB`类似于 MySQL 的 DBMS。由于我们有直接查询输出，因此我们不必测试其他有效负载。相反，我们可以测试它们并看看我们会得到什么。

## INFORMATION_SCHEMA 数据库

要使用从表中提取数据`UNION SELECT`，我们需要正确地构建`SELECT`查询。为此，我们需要以下信息：

- 数据库列表
- 每个数据库中的表列表
- 每个表中的列列表

有了上述信息，我们就可以形成`SELECT`语句来转储 DBMS 内任何数据库中任何表中任何列的数据。这是我们可以利用`INFORMATION_SCHEMA`数据库的地方。

INFORMATION_SCHEMA数据库包含有关服务器上存在[的](https://dev.mysql.com/doc/refman/8.0/en/information-schema-introduction.html)数据库和表的元数据。该数据库在利用 SQL 注入漏洞时发挥着至关重要的作用。由于这是一个不同的数据库，我们不能直接用语句调用它的表`SELECT`。如果我们只为语句指定表的名称`SELECT`，它将在同一数据库中查找表。

因此，要引用另一个数据库中存在的表，我们可以使用点“ ”`.`运算符。例如，对于名为 的数据库中存在的`SELECT`表，我们可以使用：`users``my_database`

```
SELECT * FROM my_database.users;
```

样，我们可以查看`INFORMATION_SCHEMA`数据库中存在的表。

## 图式

为了开始我们的枚举，我们应该找到 DBMS 上有哪些数据库可用。数据库中的表[SCHEMATA](https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html)`INFORMATION_SCHEMA`包含有关服务器上所有数据库的信息。它用于获取数据库名称，以便我们可以查询它们。该`SCHEMA_NAME`列包含当前存在的所有数据库名称。

让我们首先在本地数据库上测试一下，看看查询是如何使用的：

```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)
```

我们看到`ilfreight`和`dev`数据库。

注意：前三个数据库是默认的 MySQL 数据库，并且存在于任何服务器上，因此我们通常在 DB 枚举时忽略它们。有时还有第四个“sys”数据库。

现在，让我们使用`UNION`SQL 注入执行相同的操作，负载如下：

代码：sql

```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

除了默认数据库之外，我们再次看到两个数据库`ilfreight`和。`dev`让我们找出 Web 应用程序正在运行哪个数据库来检索端口数据。我们可以通过查询找到当前的数据库`SELECT database()`。我们可以像上一节中查找 DBMS 版本的方式类似地执行此操作：

```sql
cn' UNION select 1,database(),2,3-- -
```

我们看到数据库名称是`ilfreight`。然而，另一个数据库 ( `dev`) 看起来很有趣。那么，让我们尝试从中检索表。

## 表格

在从数据库转储数据之前`dev`，我们需要获取表的列表，以便使用`SELECT`语句查询它们。要查找数据库中的所有表，我们可以使用数据库`TABLES`中的表`INFORMATION_SCHEMA`。

[TABLES](https://dev.mysql.com/doc/refman/8.0/en/information-schema-tables-table.html)表包含有关整个数据库中所有表的信息。该表包含多个列，但我们对`TABLE_SCHEMA`和`TABLE_NAME`列感兴趣。该`TABLE_NAME`列存储表名，而该`TABLE_SCHEMA`列则指向每个表所属的数据库。这可以类似于我们查找数据库名称的方式来完成。例如，我们可以使用以下有效负载来查找`dev`数据库中的表：

代码：sql

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

请注意我们如何将数字“2”和“3”替换为“TABLE_NAME”和“TABLE_SCHEMA”，以获取同一查询中两列的输出。

## 列

要转储表的数据`credentials`，我们首先需要找到表中的列名，这些列名可以在数据库`COLUMNS`的表中找到`INFORMATION_SCHEMA`。COLUMNS表包含有关所有数据库中存在的所有列的信息[。](https://dev.mysql.com/doc/refman/8.0/en/information-schema-columns-table.html)这有助于我们找到要查询表的列名。、`COLUMN_NAME`、`TABLE_NAME`和`TABLE_SCHEMA`列可用于实现此目的。正如我们之前所做的那样，让我们尝试使用此有效负载来查找表中的列名称`credentials`：

代码：sql

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

该表有两列，名为`username`和`password`。我们可以使用此信息并从表中转储数据。

## 数据

现在我们已经有了所有信息，我们可以形成查询来转储数据库表中和列`UNION`的数据。我们可以用和来代替第 2 列和第 3 列：`username``password``credentials``dev``username``password`

代码：sql

```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

请记住：不要忘记使用点运算符来引用“dev”数据库中的“凭据”，因为我们在“ilfreight”数据库中运行，如前所述。

我们能够获取表中的所有条目`credentials`，其中包含密码哈希值和 API 密钥等敏感信息。
