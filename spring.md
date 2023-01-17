# Spring

[TOC]

## Spring Framework系统架构

- Data Access
- Data Integration
- Web
- AOP
  - Aspects
- Core Container
- Test

### API

https://docs.spring.io/spring-framework/docs/current/javadoc-api/index.html

## IoC 

inversion of control：控制反转

将对象的创建控制权由程序转移到外部，这种思想成为控制反转。

### 代码现状

耦合度过高（例如controller层的类中需要创建service类，不断地new，耦合度高且占用内存）

### 解决方案

使用对象时，在程序中不要主动使用new产生对象，转化为外部对象提供对象。

在对象内，声明变量，但不初始化，IoC容器会将bean注入



Spring技术对IoC思想进行了实现

Spring提供了一个容器，成为IoC容器，用来充当IoC思想中的”外部“

IoC容器负责对象的创建、初始化等一系列工作，被创建或者被管理的对象在IoC容器中统称为Bean

### 使用

导入依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>
```

在resources内创建applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

配置bean

```xml
<bean id="bean name" class="指定类的全限定名（包+类）"/>
```

获取接口调用方法

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
Object beanObj = ctx.getBean("bean id");
```



## DI (dependency injection)

在容器中建立bean与bean之间依赖关系的整个过程，称谓依赖注入。

### 目的：充分解耦

- 使用IoC容器管理bean（IoC）
- 在IoC容器内将有依赖关系的bean进行绑定 （DI）

### 最终效果

- 使用对象时不仅可以直接从IoC容器中获取到bean已经绑定了所有的依赖关系
- 类中不需要用new创建对象
- 但是需要提供setter方法（也就是说其实时容器通过setter方法进行的注入）



### 配置bean（有属性依赖）

```xml
<bean id="bean name" class="指定类的全限定名（包+类）">
	<property name="property name (inner class)" ref="other bean id"/>
</bean>
```

## Bean属性

| 属性                     | 描述                                                         |
| :----------------------- | :----------------------------------------------------------- |
| class                    | 这个属性是强制性的，并且指定用来创建 bean 的 bean 类。       |
| name                     | 这个属性指定唯一的 bean 标识符。在基于 XML 的配置元数据中，你可以使用 ID 和/或 name 属性来指定 bean 标识符。 |
| scope                    | 这个属性指定由特定的 bean 定义创建的对象的作用域。singleton&prototype |
| constructor-arg          | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| properties               | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| autowiring mode          | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| lazy-initialization mode | 延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例。 |
| initialization 方法      | 在 bean 的所有必需的属性被容器设置之后，调用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |
| destruction 方法         | 当包含该 bean 的容器被销毁时，使用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |

### 适合交给容器管理的bean

- 表现层对象
- 业务层对象
- 数据层对象
- 工具对象



### 不适合交给容器进行管理的bean

封装实体的域对象（domain）

### 实例化Bean的三种方式

#### 无参构造器

```xml
<bean id="bean name" class="指定类的全限定名（包+类）"/>
```

如果类内没有无参构造器，则抛出异常BeanCreationException

#### 静态工厂方法

```java
public class OrderDaoFactory{
	public static OrderDao getOrderDao(){  // static
		return new OrderDaoImpl();
	}
}
```

```xml
<bean id="bean name" factory-method="getOrderDao" class="xxx.OrderDaoFactory"/>
```

#### 实例工厂

```java
public class OrderDaoFactory{
	public OrderDao getOrderDao(){
		return new OrderDaoImpl();
	}
}
```

```xml
<bean id="factory" class="xxx.OrderDaoFactory"/>

<bean id="bean name" factory-method="getOrderDao" factory-bean="factory"/>
```

#### FactoryBean

类实现FactoryBean\<Beanclass>接口，重写两个get方法

```java
public class OrderFactoryBean implements FactoryBean<OrderDao>{
    public OrderDao getObject() throws Exception{
        return new OrderDaoImpl();
    }
    
    public Class<?> getObjectType(){
        return OrderDao.class
    }
}
```

```xml
<bean id="bean name" class="xxx.OrderFactoryBean"/>
```

### bean生命周期

#### 初始化容器

1. 创建对象（内存分配）
2. 执行构造方法
3. 执行属性注入（set操作）
4. 执行bean初始化方法

#### 使用bean

1. 执行业务操作

#### 关闭/销毁容器

1. 执行bean销毁方法

### 注入

```xml
<bean id="" class="">
        <constructor-arg name="" ref=""/>  <!--   构造器注入类型     -->
        <constructor-arg name="" ref=""/>
        <constructor-arg name="" value=""/>  <!--    简单类型    -->
        <constructor-arg type="" index="" value=""/>  <!--   类型匹配和索引(0开始)匹配     -->

        <property name="" ref=""/> <!--        -->
        <property name="" ref=""/> <!--        -->
        <property name="" ref=""/> <!--        -->
        <property name="">    <!--   注入集合     -->
            <list>
                <value>简单类型</value>
                <ref bean=""/>  <!--   应用类型     -->
            </list>
        </property>

</bean>
```

#### 自动装配

IoC容器根据bean所依赖的资源在容器中自动查找并注入到bean中的过程为自动装配

自动装配方式

- 按类型（常用）
- 按名称
- 按构造方法
- 不启用自动装配

比方说service需要注入一个Dao依赖，那么给它的配置添加属性autowire

```xml
<bean id="service bean name" class="xxx.xxxService" autowire="byType"/>  

<bean id="service bean name" class="xxx.xxxService" autowire="byName"/>
```

#### 集合注入

```xml
<bean id="bean name" class="xxx">
        <!--   属性     -->
        <property name="properties">
            <props>
                <prop key="country">china</prop>
                <prop key="province">jiangsu</prop>
            </props>
        </property>

        <!--   map     -->
        <property name="map">
            <map>
                <entry key="country" value="china"/>
                <entry key="province" value="jiangsu"/>
            </map>
        </property>

        <!--   array     -->
        <property name="array">
            <array>
                <value>100</value>
                <value>200</value>
                <value>300</value>
                <ref bean="xxx"/>
            </array>
        </property>

        <!--   list     -->
        <property name="list">
            <list>
                <value>123</value>
                <value>223</value>
            </list>
        </property>

        <!--   set     -->
        <property name="set">
            <set>
                <value>123</value>
                <value>223</value>
            </set>
        </property>
    </bean>
```

## 数据源对象管理

### Druid

导入Druid的数据源连接池依赖

```xml
<dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
</dependency>
```

配置bean

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/ruiji?useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
</bean>
```

### C3P0

(或者C3P0数据源连接池依赖)

```xml
<dependency>
      <groupId>c3p0</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.1.2</version>
</dependency>
```

配置bean

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ruiji?useSSL=false"/>
    <property name="user" value="root"/>
    <property name="password" value="123456"/>
</bean>
```

### 若果要加载并使用properties文件

1. 添加

   xmlns:context="http://www.springframework.org/schema/context"

2. 向xsi:schemaLocation加入
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd

3. 加载properties

   <context:property-placeholder location="jdbc.properties"/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
">
    
    <context:property-placeholder location="jdbc.properties"/>


    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>


<!--    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">-->
<!--        <property name="driverClass" value="com.mysql.jdbc.Driver"/>-->
<!--        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ruiji?useSSL=false"/>-->
<!--        <property name="user" value="root"/>-->
<!--        <property name="password" value="123456"/>-->
<!--    </bean>-->

</beans>
```

#### 补充

```xml
<!--    基本使用-->
<context:property-placeholder location="jdbc.properties"/>
<!--    不加载系统属性-->
<context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
<!--    加载多个文件-->
<context:property-placeholder location="jdbc.properties， jdbc1.properties"/>
<!--    加载所有文件-->
<context:property-placeholder location="*.properties"/>
<!--    加载文件标准格式-->
<context:property-placeholder location="classpath:*.properties"/>
<!--    从类路径或jar包中搜索并加载properties文件-->
<context:property-placeholder location="classpath*:*.properties"/>
```

## 容器

### 创建

```java
//类路径加载配置文件
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
//文件路径加载配置文件
ApplicationContext ctx = new FileSystemXmlApplicationContext("绝对路径");
//加载多个配置文件
ApplicationContext ctx = new ClassPathXmlApplicationContext("bean1.xml", "bean2.xml");
```

### 获取bean

```java
//获取bean
//方式一：使用bean名称获取 (id或者name)
NewClass bean_name = (NewClass) ctx.getBean("bean name");
//方式二：使用bean名称获取,并指定类型
NewClass bean_name1 = ctx.getBean("bean name", NewClass.class);
//方式三：使用bean类型获取
NewClass bean_name2 = ctx.getBean(NewClass.class);
```

BeanFactory老方法，这里略



## 注解开发

### 定义Bean

@Component

核心配置文件中通过组件扫描加载bean

```xml
<context:component-scan base-package="com.pengflying"/>
```

关于@Component注解，有三个衍生注解

@Controller：用于表现层bean定义

@Service：用于业务层bean定义

@Repository：用于数据层bean定义



### 纯注解开发

Spring3.0开启了纯注解开发，使用Java类替代品配置文件，开启Spring快速开发赛道

Java类替代Spring核心配置文件

```Java
@Configuration  // 设置当前类为配置类
@ComponentScan("com.pengflying")  // 设定扫描路径，此注解只能添加一次，多个数据请用数组格式
public class SpringConfig{
    
}
```

获取容器对象

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
```



### Bean管理

@Scope("singleton")：在bean类上加，表示bean是单例形式

@PostConstruct：加在方法（非static）上，表示bean初始化后，这个方法被调用 **Spring calls the methods annotated with \*@PostConstruct\* only once, just after the initialization of bean properties**.

@PreDestroy：加在方法（非static）上，表示bean销毁前，这个方法调用 **A method annotated with *@PreDestroy* runs only once, just before Spring removes our bean from the application context.**



### 依赖注入

#### @Autowired

加在变量上，开启自动装配模式（**按类型**）

注意：自动装配基于反射设计创建对象并暴力反射对应属性为私有属性初始化数据，因此无需提供setter方法

注意：自动装配建议使用无参构造方法创建对象（默认），如果不提供对应构造方法，请提供唯一的构造方法



#### @Qualifier

**必须**配合@Autowired使用，开启指定名称装配bean

```java
@Autowired
@Qualifier("xxx")
private Xxx xxx;
```



#### @Value

简单类型的注入

```java
@Value("100")
private String xxx;
```



#### @PropertySource

加载properties文件,放在配置文件SpringConfig类上

```java
@PropertySource("classpath:jdbc.properties")
```

在别的注解里就可以使用

```java
@Value("${jdbc.username}")
```



### 第三方bean管理

#### 数据源

```java
@Configuration
@ComponentScan("com.pengflying")
@PropertySource("classpath:jdbc.properties")
public class SpringConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    
    // 定义一个方法获得要管理的对象
    //添加@Bean， 表示当前方法的返回值是一个bean
    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(username);
        ds.setPassword(password);
        return ds;
    }
}

```

这里有三个选择

1. 就是直接在SpringConfig内创建数据源bean
2. 另一种就是再创建一个JdbcConfig类来创建这个bean，这个类自身不加@Configuration注解，而是在SpringConfig上面导入这个类

```java
@Import({JdbcConfig.class})
```

3. 最后一种就是在JdbcConfig类上添加@Configuration，然后在核心配置类上把配置存放的类加入扫描数组

```java
@ComponentScan({"com.penflying.config", "com.penflying.dao"})
```



#### 引用类型依赖注入

除了简单注入，这里涉及到一种常见的依赖注入方式

```java
@Bean
public DataSource datatSource(BookService bookService){
	System.out.println(bookService);
    DruidDataSource ds = new DruidDataSource();
    //属性设置
    return ds;
}
```

这里的BookService类型的对象，容器会自动查找类型，然后自动注入，**只需要定义形参**



#### spring整合Mybatis

添加依赖，除了之前的mybatis包还需要，spring-jdbc和mybatis-spring

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.6</version>
 </dependency>
 
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>


<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.0</version>
 </dependency>
```

具体配置类

```java
public class MybatisConfig {

    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        factoryBean.setTypeAliasesPackage("com.itheima.domain");
        return factoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.itheima.dao");
        return msc;
    }
}
```

之后使用不需要显示创建mapper，直接XxxDao.func(xxx)即可，简化操作



#### spring整合Junit

除了Junit还需要添加依赖spring-test依赖坐标

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>
```

在测试类上加上

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
```

测试方法依旧是@Test

不过需要的bean对象可以使用@Autowired注入



## AOP

Aspect oriented programming

面向切面式编程，一种编程范式。指导开发者如何组织程序结构。

作用：在不惊动原始设计的基础上为其进行功能增强

spring理念：无侵入式



连接点（JointPoint）：程序执行过程中的任意位置，粒度为执行方法、抛出异常、设置变量等。在SpringAOP中理解为方法的执行

切入点（PointCut）：匹配连接点的式子

在SpringAOP中，一个切入点可以只描述一个具体方法，也可以匹配多个方法

- 具体方法：com.penflying.dao.XxxDao.func()
- 多个方法：所有的save方法，所有的get开头的方法，所有以Dao结尾的接口中的任何方法，所有带有一个参数的方法等

通知（Advice）：在切入点执行的操作，也就是共性功能。在SpringAOP中，功能最终以方法的形式呈现。

通知类：定义通知的类

切面（Aspect）：描述通知与切入点的对应关系

### 使用方法

1. 导入aop相关坐标

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

2. 定义通知类受Spring容器管理

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(void com.pengflying.xxx.Xxx.update())")
    private void pt(){}
    
    @Before("pt()")
    public void before(){
        System.out.println("before this function");
    }

}
```

3. 开启Spring对AOP注解驱动支持

```java
...
@EnableAspectJAutoProxy
public class SpringConfig {
}
```

### AOP工作流程

1. Spring容器启动
2. 读取所有切面配置中的切入点
3. 初始化bean，判定bean对应的类中的方法是否匹配到任意切入点
   1. 匹配失败，创建对象
   2. 匹配成功，创建原始对象（目标对象）的代理对象
4. 获取bean执行方法
   1. 获取bean，调用方法并执行，完成操作
   2. 获取的bean是代理对象时，根据代理对象的运行模式运行原始方法与增强的内容，完成操作。



### AOP核心概念

目标对象（Target）：原始功能去掉共性功能对应的类产生的对象，这种对象是无法直接完成工作的

代理（Proxy）：目标对象无法直接完成工作，需要对其进行功能回填，通过原始对象的代理对象实现



动作关键词：描述切入点的行为动作，例如execution表示执行到指定切入点

访问修饰符：public、private等，可以省略

返回值

包名

类\接口

方法名

参数

异常名：方法定义中抛出指定异常，可以省略



可以使用通配符描述切入点，快速描述

*：单个独立的任意符号，可以独立出现，也可以作为前缀或者后缀的匹配夫出现

```
execution (public * com.xxx.*.UserService.find*(*))
```

find开头

..：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写

```
execute (public User com..UserService.findById(..))
```

+：专用于匹配子类类型

```
execution(* *..*Service+.*(..))
```



#### 书写技巧

- 所有代码按照标准规范开发，否则以下技巧全部失效
- 描述切入点通常描述**接口**，而不描述实现类
- 访问控制修饰符针对接口开发均采用public描述（可省略访问控制修饰符描述）
- 返回值类型对于增删改类使用精准类型加速匹配，对于查询类使用\*通配快速描述
- 包名书写尽量不要使用..匹配，效率过低，常用\*做单个包描述匹配，或精准匹配
- 接口名/类名书写名称与模块相关的采用\*匹配，例如UserService书写成\*Service,绑定业务层接口名
- 方法名书写以动词进行精准匹配，名词采用\*匹配，例如getById书写成getBy\*,selectAll书写成sellect\*
- 参数规则较为复杂，根据业务方法灵活调整
- 通常不使用异常作为匹配规则



### AOP通知类型

#### 前置通知

@Before

#### 后置通知

@After

#### 环绕通知 （重点）

```java
@Around("pt()")
public Object around(ProceedingJoinPoint pjp) throws Throwable{
    System.out.println("around before advice ...");
    Object ret = pjp.proceed();
    System.out.println("around after advicce");
    return ret;
}
```

#### 返回后通知

@AfterReturning

```java
@AfterReturning(value = "pt()", returning = "ret")
public void afterThrowing(String ret){
    System.out.println("afterReturning advice"+ret);
}
```



#### 抛出异常后通知

@AfterThrowing

```java
@AfterThrowing(value = "pt()", throwing = "t")
public void afterThrowing(Throwable t){
    System.out.println("afterThrowing advice"+t);
}
```



## Spring事务

事务的作用：在数据层保障一些列的数据库操作同成功同失败

Spring事务作用：在数据层或业务层保障一些列的数据库操作同成功同失败

### 使用

@Transactional

Sping注解式事务通常添加在**业务层接口**中而不会添加到业务层实现类中，降低耦合

添加到方法上；加到类上则类中所有方法全部开启事务



在JdbcConfig内设置事务管理器bean

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){
    DataSourceTransactionManager ds = new DataSourceTransactionManager();
    ds.setDataSource(dataSource);
    return ds;
}
```



开启注解式事务驱动

```
@EnableTransactionManagement
```



### 事务角色

事务管理员：发起事务方，在Spring中通常指代业务层开启事务的方法

事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法



有些属性可以配置 https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html

常用的propagation = Propagation.REQUIRES_NEW

