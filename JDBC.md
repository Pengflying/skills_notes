# JDBC

Java DataBase Connectivity

Java程序访问数据库的标准接口(JDBC接口)。

JDBC**接口**通过JDBC**驱动**来实现真正对数据库的访问。

JDBC接口是Java标准库自带的，所以可以直接编译。

具体的JDBC驱动是由数据库厂商提供的。

Java App -> JDBC Interface (in JDK) -> JDBC Driver (in Vendor) -> Database



Java程序编写时，只需要调用java.sql包下的相关接口就可以。

MySQL JDBC Driver is a jar package

Java App -> java.sql.* -> mysql-xxx.jar -> MySQL



## JDBC的好处

- 各数据库厂商使用相同的接口，Java代码不需要针对不同数据库分别开发
- Java程序编译期仅依赖java.sql包，不依赖具体数据库的jar包
- 可随时替换底层数据库，访问数据库的Java代码基本不变

连接的url后面可以跟的参数

| 后缀追加参数             | 说明                                                         | 缺省值（默认值） | 版本要求 |
| ------------------------ | ------------------------------------------------------------ | ---------------- | -------- |
| autoReconnect            | 当数据库连接异常中断时，是否自动重新连接？                   | FALSE            | 1.1      |
| autoReconnectForPools    | 是否使用针对数据库连接池的重连策略                           | FALSE            | 3.1.3    |
| allowMultiQueries        | 是否允许批量更新（一般和  rewriteBatchedStatements 参数一起用） | FALSE            |          |
| characterEncoding        | 当useUnicode设置为true时，指定字符编码。比如可设置为gb2312或gbk | FALSE            | 1.1      |
| connectTimeout           | 和数据库服务器建立socket连接时的超时，单位：毫秒。  0表示永不超时，适用于JDK 1.4及更高版本 | 0                | 3.0.1    |
| failOverReadOnly         | 自动重连成功后，连接是否设置为只读？                         | TRUE             | 3.0.12   |
| initialTimeout           | autoReconnect设置为true时，两次重连之间的时间间隔，单位：秒  | 2                | 1.1      |
| maxReconnects            | autoReconnect设置为true时，重试连接的次数                    | 3                | 1.1      |
| password                 | 用户密码（用于连接数据库）                                   |                  | all      |
| rewriteBatchedStatements | 批处理（一般和 allowMultiQueries 参数一起用）                | FALSE            |          |
| socketTimeout            | socket操作（读写）超时，单位：毫秒。 0表示永不超时           | 0                | 3.0.1    |
| serverTimezone           | 时区设置                                                     |                  |          |
| user                     | 数据库用户名（用于连接数据库）                               |                  | all      |
| useSSL                   | 高版本需要指明是否进行SSL连接                                |                  |          |
| useUnicode               | 是否使用Unicode字符集，如果有参数characterEncoding设置，该参数就需要等于true | FALSE            | 1.1      |
| zeroDateTimeBehavior     | 日期数据处理（三个属性值：exception抛SQL异常、convertToNull将日期转换为NULL、round替换成最近的日期） | exception        |          |

## 使用以及细节

### 简单使用

```java
import java.sql.*;  // 调用接口

public class MysqlDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //0. 将mysql-connector-java-5.1.48.jar加入project lib（右键加入）
        //1. 注册驱动, MySQL5之后的驱动包，可以省略注册驱动的步骤 【META-INF/services/java.sql.Driver文件中的驱动类】
        Class.forName("com.mysql.jdbc.Driver");

        //2. 获取连接对象
        String url = "jdbc:mysql://localhost:3306/ruiji?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&useSSL=false";
        String username = "root";
        String password = "123456";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义sql
        String sql = "SELECT * FROM dish";

        //4. 获取执行sql的对象
        Statement statement = conn.createStatement();

        //5. 执行sql， statement后可调用DML或DDL或DQL,跟的方法名不一样。
        ResultSet resultSet = statement.executeQuery(sql);  //执行DQL语句，返回ResultSet对象

        //6. 处理结果
        resultSet.next();
        String name = resultSet.getString("name");
        System.out.println(name);
        
        //7. 释放资源
        statement.close();
        conn.close();

    }
}

/**
     * **Class.forName** 方法的作用，就是初始化给定的类。而我们给定的 MySQL 的 Driver 类中，
     * 它在静态代码块中通过 JDBC 的 DriverManager 注册了一下驱动。我们也可以直接使用 JDBC 的
     * 驱动管理器注册 mysql 驱动，从而代替使用 **Class.forName**。
     */
```

### Connection

除了基本使用中的创建Statement以外，还有特殊的用法。

```java
// 预编译SQL的执行SQL对象：防止SQL注入
PreparedStatement prepareStatement(sql)
// 预编译要先开启，他会把参数部分用？替代，然后把后续输入的值补充进去，其中的特殊符号会自动帮我们转义，防止sql注入：（也就是使用基本的拼接字符串导致的问题）url+&useServerPrepStmts=true
```

预编译的好处

1. 预编译SQL,性能更高
2. 防止SQL注入：将敏感字符进行转义

```java
public void test1() throws ClassNotFoundException, SQLException {
        //0. 将mysql-connector-java-5.1.48.jar加入project lib（右键加入）
        //1. 注册驱动
        Class.forName("com.mysql.jdbc.Driver");

        //2. 获取连接对象
        String url = "jdbc:mysql://localhost:3306/ruiji?serverTimezone=Asia/Shanghai&" +
                "useUnicode=true&characterEncoding=utf-8&useSSL=false&useServerPrepStmts=true";
        String username = "root";
        String password = "123456";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义sql
        String sql = "SELECT * FROM dish where price < ?";

        //4. 获取执行sql的预编译对象
        PreparedStatement preparedStatement = conn.prepareStatement(sql);
        preparedStatement.setInt(1, 10000);

        //5. 执行sql， statement后可调用DML或DDL或DQL,跟的方法名不一样。
        ResultSet resultSet = preparedStatement.executeQuery();  //执行DQL语句，返回ResultSet对象

        //6. 处理结果
        while (resultSet.next()){
            String name = resultSet.getString("name");
            int price = resultSet.getInt("price");
            System.out.println(name+":"+price);
        }

        //7. 释放资源
        resultSet.close();preparedStatement.close();
        conn.close();
    }
```

注意出现了一个？的报错，注意是否多传了sql

```java
报错场景：用jdbc连接mysql，用PreparedStatement时候报了sql语句的错误。

com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '?' at line 1

报错原因：
ResultSet rs = ps.executeQuery(sql);这一句里面多给了一个sql的参数，这个参数不用给，给了就报错

解决方法：
ResultSet rs = ps.executeQuery(sql);
这一句改成
ResultSet rs = ps.executeQuery();
```



```java
//执行存储过程的对象，不过我没用过，不知道是什么
CallableStatement prepareCall(sql)
```







#### 事务管理

 ->对应的JDBC事务管理: Connection接口中定义了3个对应的方法

```
开启事务：setAutoCommit(boolean autoCommit): true 为自动提交事务；false为手动提交事务，即为开启事务
提交事务：commit()
回滚事务：rollback()
```

```java
try{
            //开启事务手动提交
            conn.setAutoCommit(false);

            //3. 定义sql
            String sql = "SELECT * FROM dish";

            //4. 获取执行sql的对象
            Statement statement = conn.createStatement();

            //5. 执行sql， statement后可调用DML或DDL或DQL,跟的方法名不一样。
            ResultSet resultSet = statement.executeQuery(sql);  //执行DQL语句，返回ResultSet对象

            //6. 处理结果
            resultSet.next();
            String name = resultSet.getString("name");
            System.out.println(name);
            
            //提交事务
            conn.commit();

            statement.close();
        } catch (Exception e){
            //遇到异常就回滚
            conn.rollback();
        } finally {
            //7. 释放资源
            conn.close();
        }
```

JDBC事务一般包裹在try...catch...逻辑中，这里关闭连接是有一些问题的，仍然不完备

#### ResultSet

分装了DQL的结果，指的是一行数据，可以迭代

主要调用两个方法

```java
boolean next() //类似迭代器里的用法，一开始就要调用

xxx getXxx(参数): 获取数据
//参数：
//int：列的编号，从1开始
//String 列的名字
```

**他的对象需要调用.close();来释放资源**

