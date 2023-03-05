# Maven

[TOC]



## 介绍

Maven是专门用于管理和构建Java项目的工具，它的主要功能有：

- 提供一套标准化的项目结构 (main & test)
- 提供了一套标准化的构建流程（编译、测试、打包、发布）
- 提供了一套依赖管理机制 （POM: project object model）

### 基本结构

```
my-app
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- mycompany
    |               `-- app
    |                   `-- App.java
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java
```



### 仓库

本地项目 <-> 本地仓库 <-> [远程仓库(私服) <->] 中央仓库



## 安装

1. 解压maven压缩包
2. 配置环境变量MAVEN_HOME为安装路径的bin目录
3. 配置本地仓库：创建文件夹为本地仓库，在conf/settings.xml中的\<localRepository>启用编辑。
4. 配置阿里云私服：在conf/settings.xml中的\<mirrors>中添加如下

```xml
<mirror>
      <id>aliyunmaven</id>
      <mirrorOf>*</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

## 生命周期

3套生命周期

```
# clean: 清理工作
pre-clean -> clean -> post-clean
```

```
# default: 核心工作，例如编译，测试，打包，安装
compile -> test -> package ->install
```

```
# site: 产生报告，发布站点等
pre-site -> site -> post-site
```

同一生命周期内，执行后边的命令，前边的所有命令会自动执行

## 使用

常用命令

```bash
清理 mvn clean
编译 mvn compile
测试 mvn test
打包 mvn package
安装 mvn install
```

### 在idae中配置

设置.xml文件一定要是settings.xml,其他名字的配置文件设置了也不会生效

## POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>maven-demo01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

### 坐标

资源的唯一标识，使用坐标来定义项目或引入项目中需要的依赖

#### 组成

```
<groupId>: 定义当前Maven项目隶属组织名称（通常是域名反写， com.itheima）
<artifactId>: 定义当前Maven项目名称（通常是模块名， order-service） 
<version>: 版本
```

#### jar包

```xml
<dependencies>
        <dependency>
            <groupId></groupId>
            <artifactId></artifactId>
            <version></version>
            <scope></scope>
        </dependency>
</dependencies>
```

#### 依赖范围 \<scope>

##### Compile

默认，可传递

```xml
<dependency>
    <groupId>commons-lang</groupId>
    <artifactId>commons-lang</artifactId>
    <version>2.6</version>
</dependency>
```

##### Provided

We use this scope to mark **dependencies that should be provided at runtime by JDK or a container.**

A good use case for this scope would be a web application deployed in some container, where the container already provides some libraries itself. For example, this could be a web server that already provides the Servlet API at runtime.

In our project, we can define those dependencies with the *provided* scope:

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

The *provided* dependencies are available only at compile time and in the test classpath of the project. These dependencies are also not transitive.

##### Runtime

**The dependencies with this scope are required at runtime.** But we don't need them for the compilation of the project code. Because of that, dependencies marked with the *runtime* scope will be present in the runtime and test classpath, but they will be missing from the compile classpath.

A JDBC driver is a good example of dependencies that should use the runtime scope:

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.28</version>
    <scope>runtime</scope>
</dependency>
```

##### Test

We use this scope to indicate that dependency isn't required at standard runtime of the application but is used only for test purposes.

***Test\* dependencies aren't transitive and are only present for test and execution classpaths.**

The standard use case for this scope is adding a test library such as JUnit to our application:

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

##### System

***System\* scope is very similar to the \*provided\* scope.** The main difference is that *system* requires us to directly point to a specific jar on the system.

It's worthwhile to mention that **[\*system\* scope is deprecated](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#system-dependencies).**

The important thing to remember is that building the project with *system* scope dependencies may fail on different machines if dependencies aren't present or are located in a different place than the one *systemPath* points to:

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>custom-dependency</artifactId>
    <version>1.3.2</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/libs/custom-dependency-1.3.2.jar</systemPath>
</dependency>
```

##### Import

**It's only available for the dependency type \*pom\*.**

*import* indicates that this dependency should be replaced with all effective dependencies declared in its POM.

Here, below *custom-project* dependency will be replaced with all dependencies declared in custom-project's *pom.xml* \<dependencyManagement> section.

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>custom-project</artifactId>
    <version>1.3.2</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

### 导入项目

选择Maven面板，点击+，选择对应Maven项目的pom.xml，双击。

面板位置View -> Appearance -> Tool Window Bars



