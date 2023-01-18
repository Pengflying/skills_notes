# SpringBoot

[TOC]

## 基本介绍

SpringBoot设计的目的是用来**简化**Spring应用的初始搭建以及开发过程

与Spring比较，SpringBoot省去了web3.0配置类，Spring/SpringMVC配置类。

Spring程序的缺点：

- 配置繁琐
- 依赖设置繁琐

SpringBoot程序优点

自动配置

起步依赖（简化依赖配置）

辅助功能（内置服务器，。。。）



### 创建项目

1. 直接使用idea创建spring项目就好
2. 选择当前模块需要使用的技术集
3. 开发控制类







### SpringBoot项目快速启动

1. 对SpringBoot项目打包（执行Maven构建指令package）
2. 执行启动指令

```bash
java -jar springboot.jar
```

jar支持命令行启动需要依赖Maven插件支持

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```







### SpringBoot起步依赖

- starter
  - SpringBoot中常见项目名称，定义了当前项目使用的所有项目坐标，以达到减少依赖配置的目的
- parent
  - 所有SpringBoot项目要继承的项目，定义了若干个**坐标版本号**（依赖管理，而非依赖），以达到减少依赖冲突的目的
  - spring-boot-starter-parent (2.5.0)与spring-boot-starter-parent(2.4.6)共计57处坐标版本不同 
- 实际开发
  - 只要写G、A不需要版本号
  - 如果发生坐标错误再指定version



### 启动方式

```java
@SpringBootApplication
public class SpringDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringDemoApplication.class, args);
    }
}
```



SpringBoot在创建项目时，采用jar的打包方式

SpringBoot的引导类是项目的入口，运行main方法就可以启动项目



### 改变起步依赖

例子：将web服务器从tomcat换为jetty

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```



### 配置文件方式

这边建议使用yml（主流）或yaml

application.yml

一种数据序列化格式



加载顺序

application.properties > application.yml > application.yaml

注意：如果没有提示，则将依赖文件在Facets内将配置文件加入管理



#### 优点：

- 容易阅读
- 容易与脚本语言交互
- 以数据为核心，重数据轻格式



#### 语法规则

- 大小写敏感
- 属性层级关系使用多行描述，每行结尾使用冒号结束
- 使用缩进表示层级关系，同层级左侧对齐，只允许使用空格（不允许使用Tab键）
- 属性值前面添加 空格（属性名与属性值之间使用冒号+空格作为分割）
- #表示注释



##### 数组数据

```yaml
subject:
  - java
  - python
```

使用"${subject[0]}"



##### 还可以自定对象封装指定数据

```yaml
eee:
  name: hello
  suject:
    - java
    - sss
```

```java
@Component
@ConfigurationProperties(prefix = "eee")
public class eee {
    private String name;
    private String[] suject;
}
```

自定义对象封装数据警告解决方案

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

#### 多环境开发

```yaml
spring:
  profiles:
    active: pro
---
spring:
  profiles: pro
server:
  port: 80
---
spring:
  profiles: dev
server:
  port: 81
---
spring:
  profiles: test
server:
  port: 82
```



##### 多环境开发兼容问题

1. pom.xml, maven中的属性

```xml
<profiles>
    <profile>
        <id>dev_env</id>
        <properties>
            <profile.active>dev</profile.active>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>

    <profile>
        <id>pro_env</id>
        <properties>
            <profile.active>pro</profile.active>
        </properties>
    </profile>

    <profile>
        <id>test_env</id>
        <properties>
            <profile.active>test</profile.active>
        </properties>
    </profile>
</profiles>
```

2. 引用Maven属性

```yaml
spring:
  profiles:
    active: ${profile.active}
---
spring:
  profiles: pro
server:
  port: 80
---
spring:
  profiles: dev
server:
  port: 81
---
spring:
  profiles: test
server:
  port: 82
```

3. 对资源文件开启对默认占位符的解析

```xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-resources-plugin</artifactId>
            <configuration>
                <encoding>utf-8</encoding>
                <useDefaultDelimiters>true</useDefaultDelimiters>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 配置文件的路径

##### SpringBoot中4级配置文件

1. file:config/application.yml (最高)
2. file:application.yml
3. classpath:config/application.yml
4. classpath:application.yml (最低)



##### 作用：

1级与2级留作系统打包后设置通用属性

3级与4级用于系统开发阶段设置通用属性



#### 整合Junit

##### @SpringBootTest

```java
@SpringBootTest(classes = SpringDemoApplication.class)
class SpringDemoApplicationTests {

    @Test
    void contextLoads() {
    }

}
```

如果测试类在SpringBoot启动类的包或子包中，可以省略启动类的设置，也就是省略classes的设定



#### 整合MyBatis, MySQL

加入依赖

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>
```

设置数据源参数(application.yml内)

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ssm_db
    username: root
    password: 123456

```

SpringBoot版本低于2.4.3（不含），Mysql驱动版本大于8.0时，需要在url连接串中配置时区

```
jdbc:mysql://localhost:3306/ssm_db?serverTimezone=UTC
```



##### @Mapper

不需要配置指定Mapper位置，只需要在接口上添加注解@Mapper

