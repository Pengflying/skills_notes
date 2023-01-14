# Servlet

[TOC]



## 定义

Servlet是Java提供的一门动态web资源开发技术

Servlet是JavaEE规范之一，其实就是一个接口，将来我们需要定义Servlet类实现Servlet接口，并由web服务器运行Servlet

Servlet由web服务器创建，Servlet方法由web服务器调用。

## 基本使用

1. 导入坐标

```xml
<dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
</dependency>
```

Tomcat里带有servlet所以不用

2. 定义service类实现Servlet接口
3. 在类上加注解 @WebServlet("/demo1")

```java
@WebServlet("/demo1")
public class DishService implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        servletResponse.getWriter().write("hello, servlet");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
    }
}
```

4. 启动tomcat，输入url访问该Servlet

## 生命周期

servlet运行在Servlet容器（web服务器）中。

1. 加载和实例化：第一次被访问，容器创建Servlet对象
2. 初始化：实例化后调用init()
3. 请求处理: 获得请求，调用service()处理
4. 服务终止：释放内存或者容器关闭，调用destroy()方法完成资源释放。destory()方法调用后，容器释放这个Servlet实例，该实例随后被Java的垃圾收集器所回收。



## 注解

WebServlet(urlPatterns = "/demo", loadOnStartup=1)

loadOnStartup

1. 负整数：第一次被访问是创建Servlet对象
2. 0或正整数：服务启动时创建Servlet对象，数字越小优先级越高

```
多个就放在{}内，用，隔开

也可以目录匹配“/user/*”

扩展名匹配“*.do”

任意匹配 “/”或者“/*”
```

“/”和“/*”的区别

当我们的项目中Servlet配置了“/”,会覆盖掉tomcat中的DefaultServlet，当其他的url-pattern都匹配不上时会走这个servlet

当我们的项目配置了“/*”，意味着匹配任意访问路径

优先级

精确路径 > 目录路径 > 扩展名路径 > /* > /



## 体系结构

Servlet：Servlet体系根接口

GenericServlet：Servlet抽象实现类

HttpServlet：对HTTP协议封装的Servlet实现类 （B/S架构的项目）

```java
public class DishService01 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

### request

#### 继承体系（由上到下）

- ServletRequest：Java提供的对象请求根接口
- HttpServletRequest：Java提供的对Http协议封装的请求对象接口
- RequestFacade：Tomcat定义的实现类



1. Tomcat需要解析请求数据，封装为Request对象，并且创建request对象传递到service方法中
2. 使用request对象，查阅JavaEE API文档的HttpServletRequest接口

get和post这里获取参数涉及到输入流以及编码的问题，但在后续的框架运用中都会由框架解决到这些问题，所以这里不做展开



#### 请求转发

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setAttribute("attr", new Object());
        req.getAttribute("attr");
        req.removeAttribute("attr");
        req.getRequestDispatcher("B路径").forward(req, resp);
    }
```

##### 特点

- 浏览器的地址栏路径不发生变化
- 只能转发到当前服务器的内部资源
- 一次请求，可以在转发的资源间使用request共享数据



### response

#### 重定向（redirect）

资源跳转的方式

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.setStatus(302);
    resp.setHeader("Location", "资源B的路径");
    resp.sendRedirect("资源B的路径");
}
```

- ##### 特点

- 浏览器地址栏路径发生改变

- 可以重定向到任意位置的资源（服务器内部、外部均可）

- 两次请求，不能在多个资源使用request共享数据

### 路径问题

浏览器使用：需要加虚拟目录（项目访问路径）

服务端使用：不需要加虚拟目录



