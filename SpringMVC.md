# SpringMVC

[TOC]

## 基本介绍

SpringMVC技术与servlet技术功能等同，均属于web层开发技术

SpringMVC是一种基于Java实现MVC模型的轻量级web框架

### 优点：

- 使用简单，开发敏捷（相比于Servlet）
- 灵活性强



## 使用

导入servlet和springmvc的依赖坐标

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```

创建SpringMVC控制器类（等同于Servlet）

```java
@Controller
public class BookController {
    @Autowired
    private BookService bookService;

    @RequestMapping("/save")
    @ResponseBody
    public String save() {
        boolean flag = bookService.save(book);
        return "content";
    }
}
```

初始化SpringMVC环境（同Spring环境），设定SpringMVC加载对应的Bean

```java
@Configuration
@ComponentScan({"com.itheima.controller"})
public class SpringMvcConfig{
}
```

初始化Servlet容器，加载SpringMVC环境，并设置SpringMVC技术处理的请求

```java
public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

## 启动服务器初始化过程

1. 服务器启动，执行ServletContainersInitConfig类，初始化web容器
2. 执行createServletApplicationContext方法，创建了WebApplicationContext对象
3. 加载SpringMvcConfig
4. 执行@ComponentScan加载对应的bean
5. 加载UserController，每个@RequestMapping的名称对应一个具体的方法
6. 执行getServletMappings方法，定义的所有请求都通过SpringMVC



### 注意

因为功能不同，如何避免Spring错误的加载到SpringMVC的bean？

加载Spring控制的bean的时候排除掉SpringMVC控制到的bean

SpringConfig -> 排除掉controller的扫描

显示排除则

```java
@ComponentScan(value={"com.itheima.service"},
excludeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Controller.class
))
```



## 有关controller内的mapping和参数注解

### @RequestMapping("/???")

可以放在类或者方法上，匹配路径

### Get请求参数

url地址传参，地址参数名与形参变量名相同，定义形参即可接收参数

如果不同则要使用@RequestParam参数关系

```java
public void xxx(@RequestParam("name") String username){}
```



### Post请求参数

普通参数：form表单post请求传参，表单参数名与形参变量名相同，定义形参即可接收参数。



### POJO参数

请求参数名与形参对象属性名相同，定义POJO类型形参即可接收参数

```java
public void xxx(User user){}
```



### 数组参数

请求参数名与形参对象属性名相同且请求参数为多个，定义数组类型形参即可接收参数

```java
public void xxx(String[] likes){}
```



### 集合保存普通参数

集合保存普通参数：请求参数名与形参集合对象名相同且请求参数为多个， @RequestParam绑定参数关系

```java
public void xxx(@requestParam List<String> likes){}
```

### 乱码可以为web容器添加过滤器

```java
public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("utf-8");
        return new Filter[]{filter};
    }
}
```

### json数据的传输和接受

#### 添加依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```

开启自动转换json数据的支持@EnableWebMvc, 这个注解会开启SpringMVC多项辅助功能

```java
@Configuration
@ComponentScan({"com.itheima.controller"})
@EnableWebMvc
public class SpringMvcConfig implements WebMvcConfigurer {}
```



### @RequestBody

形参注解

将请求体中的所有数据传递给请求参数，一个方法中最多只有一个

```java
//pojo
public void xxx(@RequestBody User user){}
//list
 public void xxx(@RequestBody List<String> likes){}
```

### @RequestBody与@RequestParam区别

@RequestBody用于接收json数据

@RequestParam用于接收url地址传参，表单传参

后期开发中，json格式的数据为主



#### @DateTimeFormat

传递日期型数据类型 

```java
@RequestMapping("/??")
@ResponseBody
public String dataPara(Date date,
                       @DateTimeFormat(pattern = "yyyy-MM-dd") Date date1,
                       @DateTimeFormat(pattern = "yyyy/MM/dd HH:mm:ss") Date date2
                      ){
    System.out.println(date);
    System.out.println(date1);
    System.out.println(date2);
}
```



## 响应

### 响应页面

```java
@RequestMapping("/??")
//不需要@ResponseBody
public String toPage(){
   return "page.jsp" 
}
```

### 响应文本

```java
@RequestMapping("/??")
@ResponseBody
public String toText(){
   return "response test" 
}
```

### 响应json数据

```java
@RequestMapping("/??")
@ResponseBody
public User toJson(){
    User user = new User();
    ...
   return user
}
```

### @ResponseBody

放在SpringMVC控制器方法定义上方

设置当前控制器方法响应内容为当前返回值，无需解析

设置当前控制器返回值作为响应体



## REST风格

根据REST风格对资源进行访问成为RESTful

### 优点

- 隐藏资源的访问行为，无法通过地址得知资源是**何种操作**
- 书写简化

一般的

- GET
  - 查询全部用户信息
  - 查询指定用户信息
- POST
  - 添加用户信息
- PUT
  - 修改用户信息
- DELETE
  - 删除用户信息

### 使用

```java
@GetMapping("/{id}")
public Result getById(@PathVariable Integer id) {
    Book book = bookService.getById(id);
    Integer code = book!=null?Code.GET_OK:Code.GET_ERR;
    String msg = book!=null?"":"数据查询失败，请重试!";
    return new Result(code, book, msg);
}
```

路径变量要在请求mapping里标出，并在形参前面添加@PathVariable注解



### @RestController

将@RequestBody同意抽出，与类上的@Controller合并 => RestController



### @GetMapping

### @PostMapping

### @PutMapping

### @DeleteMapping



