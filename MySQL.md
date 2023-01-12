# MySQL

[TOC]

## 基本概念

webpage - JavaWeb program - database

Through these three parts, a web service will be constructed.

Mysql是关系型数据库，简单的理解就是表与表之间是有关系的，比如说外键等，连接了两个表之间的关联属性。

关系型数据库的好处是：

1. 都是使用表结构，格式一致，易于维护。
2. 使用通用的SQL语言操作，使用方便，可用于复杂查询（也就是多表查询）
3. 数据存在磁盘中，安全 ---这点其实是对应了非关系数据库，例如Redis，他是把数据存在内存中，一旦服务关闭就会删除所有数据

## SQL (Structured Query Language)

书写要求：

可以单行书写，可以多行书写，但是要以分号结尾。

不区分大小写，关键字建议使用全大写（SELECT等）



注释：

可以单行注释： -- 注释内容 或 #注释内容（MySQL 特有）

多行注释：/* 注释 */



SQL分类

DDL: （Definition）数据定义语言，用来定义数据库对象：数据库，表，列等

DML: (Manipulation) 数据操作语言，用来对数据库中的表的数据进行增删改

DQL: (Query) 数据查询语言，用来查询数据库中表的记录（数据）

DCL: (Control) 数据控制语言，用来定义数据库的访问权限和安全级别，及创建用户

### DDL

```sql
SHOW DATABASES; # 显示现在有哪些数据库
CREATE DATABASE 数据库名称;  # 创建数据库
CREATE DATABASE IF NOT EXISTS 数据库名称;  # 创建数据库,如果不存在再创建
DROP DATABASE 数据库名称; # 删除数据库
DROP DATABASE IF EXISTS 数据库名称; # 删除数据库，如果数据库存在则删除
SELECT DATABASES(); # 查看当前使用的数据库
USE 数据库名称; # 使用数据库
```

```sql
# 查询表
SHOW TABLES; # 查询当前数据库下所有表的名称
DESC 表的名称; # 查询表结构
```

```sql
CREATE TABLE 表名 （
	字段名1 数据类型1，
	字段名2 数据类型2，
	...
	字段名n 数据类型n   #最后一行末尾，不能加逗号
）
```

数值类型

| name         | 大小                                     | 用途         |
| ------------ | ---------------------------------------- | ------------ |
| TINYINT      | 1  Bytes                                 | 小整数值     |
| SMALLINT     | 2 Bytes                                  | 大整数值     |
| MEDIUMINT    | 3 Bytes                                  | 大整数值     |
| INT或INTEGER | 4 Bytes                                  | 大整数值     |
| BIGINT       | 8 Bytes                                  | 极大整数值   |
| FLOAT        | 4 Bytes                                  | 单精度浮点数 |
| DOUBLE       | 8  Bytes                                 | 双精度浮点值 |
| DECIMAL      | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 小数值       |

日期和时间类型

| 类型      | 大小 （bytes） | 用途                     |
| --------- | -------------- | ------------------------ |
| DATE      | 3              | 日期值                   |
| TIME      | 3              | 时间值或持续时间         |
| YEAR      | 1              | 年份值                   |
| DATETIME  | 8              | 混合日期和时间值         |
| TIMESTAMP | 4              | 混合日期和时间值，时间戳 |

字符串类型

| 类型       | 大小                  | 用途                            |
| ---------- | --------------------- | ------------------------------- |
| CHAR       | 0-255  bytes          | 定长字符串                      |
| VARCHAR    | 0-65535 bytes         | 变长字符串                      |
| TINYBLOB   | 0-255 bytes           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 bytes           | 短文本字符串                    |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 bytes        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                    |

```sql
# 删除表
DROP TABLE 表名;  # 删除表
DROP TABLE IF EXISTS 表名;  # 删除表时判断表是否存在
```

```sql
# 修改表
ALTER TABLE 表名 RENAME TO 新的表名;  # 重命名表的名字
ALTER TABLE 表名 ADD 列名 数据类型;  # 新增一列
ALTER TABLE 表名 MODIFY 列名 新数据类型; # 修改列名的数据类型
ALTER TABLE 表名 CHANGE 列名 新列名 新的数据类型; # 将表中的老列名改成新列名并且赋予新的数据类型
ALTER TABLE 表名 DROP 列名;  # 删除指定列名
```

修改表首先要记起ALTER TABLE

然后想要进行什么操作：RENAME TO, ADD, MODIFY, CHANGE, DROP

之后跟上指定参数



### DML

添加数据，这是针对表里的数据来说的

```sql
INSERT INTO 表名(列名1, 列名2, ...) VALUES(值1，值2，。。。);  # 给指定列添加数据
INSERT INTO 表名 VALUES(值1，值2，。。。);  # 给全部列添加数据
# 批量添加数据
INSERT INTO 表名(列名1, 列名2, ...) VALUES(值1，值2，。。。), (值1，值2，。。。) ....; 
INSERT INTO 表名 VALUES(值1，值2，。。。),(值1，值2，。。。)...;
```

修改数据

```sql
#开发中切记一定要加where条件，否则所有的数据都会被修改
UPDATE 表名 SET 列名1=值1，列名2=值2，... [WHERE 条件];
```

删除数据

```sql
#开发中切记一定要加where条件，否则所有的数据都会被删除
DELETE FROM 表名 [WHERE 条件];
```

### DQL

具体的值都放在单引号里面

```sql
# 查询语句要按如下顺序来，可以缺少关键词，但是不能乱序
SELECT 字段列表
FROM 表名列表
WHERE 条件列表
GROUP BY 分组字段
HAVING 分组后的条件
ORDER BY 排序字段
LIMIT 分页限定;
```

#### 基础查询

```sql
# 查询多个字段
SELECT 字段列表 FROM 表名;
# 查询多个字段,并去除重复的记录
SELECT DISTINCT 字段列表 FROM 表名;
# 查询所有数据
SELECT * FROM 表名;
# 起别名
AS: AS也可以省略
```

#### where后面可以跟的查询条件符号

| 符号                 | 功能                           |
| -------------------- | ------------------------------ |
| >                    |                                |
| <                    |                                |
| >=                   |                                |
| <=                   |                                |
| =                    | 等于，这里和Java里的比较不一样 |
| <> 或 !=             | 不等于                         |
| BETWEEN ... AND .... | 在某个范围之内（都包含）       |
| IN(...)              | 多选一，记得是小括号包围       |
| LIKE 占位符          | _单个任意字符 %多个任意字符    |
| IS NULL              | 是NULL                         |
| IS NOT NULL          | 不是NULL                       |
| AND 或者 &&          | 并且                           |
| OR 或者 \|\|         | 或者                           |
| NOT 或者 !           | 非， 不是                      |

#### 排序查询

```sql
# 排序一共就两种
SELECT 字段列表 FROM 表名 ORDER BY 排序字段名1 [排序方式1], 排序字段名2 [排序方式2] ... ;
# 排序方式
ASC：升序排列（默认值）
DESC: 降序排列
# 注意：如果有多个排序条件，当前边的条件值一样时，才会根据第二条件进行排序。
```

#### 聚合函数

| 函数名      | 功能                             |
| ----------- | -------------------------------- |
| count(列名) | 统计数量（一般选用不为null的列） |
| max(列名)   |                                  |
| min(列名)   |                                  |
| sum(列名)   | 求和                             |
| avg(列名)   | 平均值                           |

注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义

where 和 having的区别

1. 执行时机：

2. 可判断的条件不一样：where不能对聚合函数进行判断，having可以。

#### 分页查询 LIMIT

```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引，查询条目数
# 起始索引是从0开始的
```

#### 约束

1. 约束的作用于列上的规则，用于限制加入表的数据

2. 约束的存在保证了数据库中数据的正确性、有效性和完整性

##### 分类

| 约束名称   | 描述                                                         | 关键词         |
| ---------- | ------------------------------------------------------------ | -------------- |
| 非空约束   |                                                              | NOT NULL       |
| 唯一约束   |                                                              | UNIQUE         |
| 主键约束   | 唯一 + 非空                                                  | PRIMARY KEY    |
| 检查约束   | 保证列中的值满足某一条件                                     | CHECK          |
| 默认约束   |                                                              | DEFAULT        |
| 外键约束   | 设置表与表之间的约束性                                       | FOREIGN KEY    |
| 自增长约束 | 列的类型是整数类型；通常给主键用；一张表只有一个             | AUTO_INCREMENT |
| 无符号约束 | 当前列的数值为非负数，针对整数                               | UNSIGNED       |
| 零填充约束 | 整型字段有个ZEROFILL属性，在数字长度不够的数据前面填充0，以达到设定的长度。 | ZEROFILL       |

```sql
#一些特殊的关键词
DISTINCT  # 不重复
```

###### DEFAULT

```SQL
CREATE TABLE t1(
       id INT DEFAULT 1,
       name VARCHAR(20) DEFAULT '老王'
);
```

###### 删除唯一约束

注意：如果删除的唯一约束列具有自增长约束，则必须先删除自增长约束，再去删除唯一约束

###### 主键

```sql
 [constraint id_primary] primary(id, name) -- 联合约束

# 通过alter语句添加： 
# alter .... modify/change ....   /   alter .... add primary key ....
ALTER TABLE t4 MODIFY id INT PRIMARY KEY;
ALTER TABLE t3 ADD CONSTRAINT un_primary PRIMARY KEY(id, name);
```

###### ZEROFILL

1. 这种用法，可以大量用于所谓“流水号”的生成上。比如，想要生成日期_0x的流水号。可以直接拼接

```SQL
CREATE TABLE `staffs` (  
  `id` int(11) unsigned zerofill NOT NULL AUTO_INCREMENT,  
  `col2` varchar(20) NOT NUL,  
  PRIMARY KEY (`id`),  
) ENGINE=InnoDB;  
```

2. 比较常用的应该是月份或日期前补0，还是整形数字不是字符串。

```SQL
CREATE TABLE `t1` (  
  `year` year(4) DEFAULT NULL,  
  `month` int(2) unsigned zerofill DEFAULT NULL,  
  `day` int(2) unsigned zerofill DEFAULT NULL  
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

###### FOREIGN KEY

- 建立表与表之间的关系，建立参照完整性，一个表可以有多个外键，每个外键必须参照另一个主键。
- 被外键约束的列，取值必须参照其主表列中的值
- 注意：通常先创建主表，再创建从表

创建外键

```sql
CREATE TABLE emp(
       empno int promary key auto_increment,
       ename varchar(32) not null,
       deptno int,
       [CONSTRAINT fk_name] FOREIGN KEY(deptno) REFERENCES dept(deptno) -- 添加外键约束
);
```

删除外键

```sql
ALTER ... DROP FOREIGN KEY fk_name;
ALTER TABLE emp DROP FOREIGN KEY fk_name;
```

注意：

- 在创建表时，不去明确指定外键约束的名称，系统会自动地生成一个外键的名称。
- 使用 show create table 表名 查看具体的外键名称

设置外键中的级联关系

- on delete cascade: 删除主表中的数据时，从表中的数据随之删除
- on update cascade: 更新主表中的数据时，从表中的数据随之更新
- on delete set null: 删除主表中的数据时，从表中的数据置空

级联删除

```sql
CREATE TABLE emp(
       empno int PRIMARY KEY AUTO_INCREMENT, 
       ename varchar(32) NOT NULL,
       deptno int,
       [CONSTRAINT fk_name]FOREIGN KEY(deptno) REFERENCES dept(deptno) ON DELETE CASCADE-- 添加外键约束
);
```

注意：

- 插入数据时，先插入主表的数据，再插入从表的数据
- 删除数据时，先删除从表的数据，再删除主表的数据


###### CHECK

添加check约束

```SQL
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    City varchar(255),
    [CONSTRAINT CHK_Person] CHECK (Age>=18 AND City='Sandnes')
);
```

在创建表后添加check键

```sql
ALTER TABLE Persons ADD CHECK (Age>=18);
ALTER TABLE Persons ADD CONSTRAINT CHK_PersonAge CHECK (Age>=18 AND City='Sandnes');
```

删除check键

```sql
ALTER TABLE Persons DROP CHECK CHK_PersonAge;
```

## 数据库设计

表关系：

1. 一对一：举例：用户和用户详情
2. 一对多：举例：部门和员工
3. 多对多：举例：商品和订单，建立第三张表，作为中间表（id, order_id, commodity_id）

## 连接查询

https://www.w3schools.com/mysql/mysql_join.asp

- `INNER JOIN`: Returns records that have matching values in both tables
- `LEFT JOIN`: Returns all records from the left table, and the matched records from the right table
- `RIGHT JOIN`: Returns all records from the right table, and the matched records from the left table
- `CROSS JOIN`: Returns all records from both tables

### 内连接

```sql
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate 
FROM Orders 
INNER JOIN Customers 
ON Orders.CustomerID=Customers.CustomerID;
```

### 左(外)连接

```sql
SELECT column_name(s)
FROM table1
LEFT [OUTER] JOIN table2
ON table1.column_name = table2.column_name;
```

### 右(外)链接

```sql
SELECT column_name(s)
FROM table1
RIGHT [OUTER] JOIN table2
ON table1.column_name = table2.column_name;
```

### 交叉连接

```sql
SELECT column_name(s)
FROM table1
CROSS JOIN table2;
```



## 子查询

```sql
#其实就是查询嵌套，一个查询的结果是另一个查询需要的
#单行单列：作为条件值
SELECT 字段列表 FROM 表 WHERE 字段名 = (子查询);
#多行单列: 作为条件值，使用in等关键字进行条件判断
SELECT 字段列表 FROM 表 WHERE 字段名 in (子查询);
#多行多列：作为虚拟表
SELECT 字段列表 FROM (子查询) WHERE 条件;
#可以灵活使用as
SELECT 字段列表 FROM (子查询) [as] sub_table WHERE 条件;
```



## 事务（Transaction）

一种机制，包含一系列的sql，作为整体，同时成功，同时失败。不可分割的工作逻辑单元。

```SQL
# 开启事务
START TRANSACTION; 
# 或者
BEGIN;
# 提交事务
COMMIT;
# 回滚事务
ROLLBACK;
```

### 四大特征

1. 原子性：事务是不可分割的最小操作单位，要么同时成功，要么同时失败。
2. 一致性：事务完成时，必须使所有的数据都保持一致状态。
3. 隔离性：多个事务之间，操作的可见性。
4. 持久性：事务一旦提交或者回滚，他对数据库中的数据的改变就是永久的。

```SQL
# 查询事务的默认提交方式
SELECT @@AUTOCOMMIT;
# 修改事务的提交方式， 手动提交
SET @@AUTOCOMMIT = 0;
```

