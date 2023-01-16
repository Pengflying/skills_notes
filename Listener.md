# Listener

[TOC]

## 基本概念

概念：Listener表示监听器，是javaWeb三大组件（Servlet, Filter, Listener）之一

监听器可以监听就是在application， session， request三个对象、销毁或者往其中添加删除属性时自动执行代码的功能组件

- applicatiopn -> ServletContext监听
  - ServletContext：创建销毁；属性的增删改
- Session监听：Session创建销毁；属性的增删改；Session的绑定和解除；数据的钝化和活化
- Request监听：创建销毁；属性的增删改





## 使用

```java
@WebListener
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("创建成功");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("已经销毁");
    }
}
```

