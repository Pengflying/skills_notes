# Tomcat

## 定义

Web服务器是一个应用程序（软件），**对HTTP协议的操作进行封装**，使得程序员不必对协议进行操作，让web开发更加便捷，主要功能是“提供网上信息浏览服务”

Tomcat是一个开源免费的轻量级Web服务器，支持Servlet/JSP少量JavaEE规范

Tomcat也被称为Web容器、Servlet容器。Servlet需要依赖于Tomcat才能运行。

## 作用

- 封装HTTP协议操作，简化开发
- 可以将web项目部署到服务器中，对外提供网上浏览服务

## 使用

*（需要本地的JAVA_HOME之前配置正确）*

下载解压即可使用，无需配置。

bin\startup.bat启动服务

bin\shutdown.bat关闭服务（正常关闭）

默认8080端口，需要修改则在conf/server.xml内修改



如果不部署到服务器上的话，可以配置插件tomcat7来运行（idea）

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>8080</port>
          <path>/</path>
        </configuration>
      </plugin>
    </plugins>
</build>
```



