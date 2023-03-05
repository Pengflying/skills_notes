# Eureka

为了解决服务调用的硬编码地址问题



服务提供者会在启动时，将自己的服务信息注册到Eureka-server （例如ip+port）

服务消费者需要拉去服务，注册中心会提供一个合适的给他（负载均衡算法）

之后开始远程调用



## 心跳续约

每隔30秒服务向Eureka-server发送一次请求，表示自己还活跃。

收不到心跳，就将信息从列表移除。





启动两个springboot项目

copy configure

然后再vm内输入-Dserver.port=新端口



eureka使用依赖

注册中心

```xml
<!--eureka服务端-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

```xml
<!--eureka客户端-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```



实现负载均衡，改变硬编码

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate(){
    return new RestTemplate();
}
```

```java
// 2.发送请求
String url = "http://userservice/user/"+order.getUserId();
User user = restTemplate.getForObject(url, User.class);
```

