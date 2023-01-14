# Cookie and Session

[TOC]

## 会话跟踪技术

- 会话：用户打开浏览器，访问web服务器的资源，会话建立，直到有一方断开连接，会话结束。再一次会话中可以包含多次请求和响应。
- 会话跟踪：一种维护浏览器状态的方法，服务器需要识别多次请求是否来自同一个浏览器，以便在同一次会话的多次请求间共享数据
- HTTP协议是无状态的，每次浏览器像服务器请求时，服务器都会将该请求视为新的请求，因此我们需要会话跟踪技术来实现会话内数据共享

实现方式：

1. 客户端：Cookie
2. 服务端：Session

## Cookie

### 定义

客户端会话技术，将数据保存到客户端，以后每次请求都携带Cookie数据进行访问

### 使用

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //回去请求参数中的所有cookie
    Cookie[] cookies = req.getCookies();
    for (Cookie item : cookies) {
        item.getName();
        item.getValue();
    }
    //加入新的cookie
    Cookie cookie = new Cookie("key", "value");
    resp.addCookie(cookie);
}
```

在response中加入cookie其实就是响应头里设置set-cookie:k=v

Cookie存活时间

默认情况下， Cookie存储在浏览器内存中，当浏览器关闭时，内存释放，则Cookie被销毁

setMaxAge(int seconds):设置Cookie的存活时间

1. 正数：将cookie写入浏览器所在的电脑硬盘，持久化到存储，到时间自动删除
2. 负数：默认操作，浏览器关则销毁
3. 零：删除对应Cookie

注意：存中文的话要转码：URL编码



## Session

基于cookie实现

cookie中添加JSESSIONID = value

### Session钝化、活化

钝化：服务器正常关闭后，Tomcat会自动将Session数据写到硬盘的文件中

活化：再次启动服务器后，从文件中加载数据到Session中

### Session销毁

无操作30分钟销毁，可以配置

在web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
  
    <!--配置session无操作自动销毁时间-->
  <session-config>
    <session-timeout>30</session-timeout>
  </session-config>
    
</web-app>

```

也可以在程序中调用Session的invalidate()方法



## 小结

Cookie和Session都是来完成一次会话内多次请求间数据共享的

区别：

存储位置：Cookie是将数据存在客户端，Session将数据存储在服务端

安全性：Cookie不安全，Session安全

数据大小：Cookie最大3KB，Session无大小限制

存储时间：Cookie可以长时间存储，Session默认30分钟

服务器性能：Cookie可以长时间存储，Session占用服务器的资源
