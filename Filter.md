# Filter

[TOC]

## 基本定义

Filter表示过滤器，是JavaWeb三大组件（Servlet、Filter、Listener）之一

过滤器可以把对资源的请求拦截下来，从而实现一些特殊的功能。

过滤器一般完成一些通用的操作，比如：权限控制、统一编码处理、敏感字符处理等等。。。

## 实现

匹配的路径才会进入过滤器，不匹配的可以直接访问

```java
@WebFilter("/*")
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //处理
        System.out.println("请求到达过滤器");
        //放行
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {

    }
}

```

### 执行逻辑

执行放行前逻辑->放行->访问逻辑->执行放行后逻辑

### 拦截路径配置

具体：/index.jsp

目录：/user/*

后缀名：*.jsp

拦截所有：/*



过滤器链

一个web应用可以配置多个过滤器，在这多个过滤器称谓过滤器链

```java
@WebFilter(filterName="FirstFilter",urlPatterns="/index.jsp")
```

注解配置的Filter，优先级按照过滤器类名（filterName字符串）的自然排序