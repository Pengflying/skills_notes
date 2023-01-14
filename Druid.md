# Druid

## 数据连接池

数据库连接池是一个容器，负责分配，管理数据库连接。

它允许应用程序重复使用一个现有的数据库连接，而不是重新建立一个。

释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏。

### 好处

- 资源重用 （多次SQL调用一个连接）
- 提升系统的响应速度 （不需要每个SQL都创建新的连接）
- 避免数据库连接遗漏 （如果不用连接池，每次连接数据库都需要创建一个连接，然后手动释放资源，连接池可以自动帮我们释放资源，很人性化，避免遗漏）

### 常见的数据库连接池

DBCP, C3P0, Druid

## 使用

```java
System.out.println(System.getProperty("user.dir"));
// 1. 导入jar包: mysql-connector-java和druid
// 2. 定义配置文件
// 3. 加载配置文件
Properties prop = new Properties();
prop.load(new FileInputStream("src/druid.properties"));

// 4. 获取连接池对象
DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);

// 5. 获取数据库连接 Connection
Connection connection = dataSource.getConnection();
System.out.println(connection);
```

### 配置文件druid.properties

```
driverClassName=com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/ruiji?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&useSSL=false
username = root
password = 123456

# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大等待时间
maxWait=3000
```

