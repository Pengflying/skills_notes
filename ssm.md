# SSM

[TOC]



## 表现层数据封装

前端接收数据格式--封装特殊消息到message属性中

类似的

```java
public class Result{
	private Object data;
    private Integer code;
    private String msg;
}
```



## 异常处理器

### 常见的异常原因

- 框架内抛出异常：使用不合规
- 数据层抛出异常：外部服务器故障导致（服务器访问超时）
- 业务层抛出异常：业务逻辑书写错误导致
- 表现层抛出异常：数据收集校验等规则导致
- 工具类抛出异常：工具类书写不严谨不健壮导致



问题：各个层级均出现异常，异常处理代码书写在哪一层

解决策略：所有的异常均抛出到表现层进行处理

分析：如果每个一场方法单独书写，代码书写量巨大且意义不强，需要借助AOP思想解决

示例：针对不同的异常，捕获并且返回相应结果

@RestControllerAdvice：rest风格的表现层通知->对表现层抛出的异常进行AOP增强

@ExceptionHandler：设置指定异常的处理方案，功能等同于控制器方法，出现异常后终止原始控制器执行，并转入到当前方法执行

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandler(SystemException.class)
    public Result doSystemException(SystemException e){
        System.out.println("异常你来啦");
        return new Result(e.getCode(), null, e.getMessage());
    }

    @ExceptionHandler(BusinessException.class)
    public Result doBusinessException(BusinessException e){
        System.out.println("异常你来啦");
        return new Result(e.getCode(), null, e.getMessage());
    }


    @ExceptionHandler(Exception.class)
    public Result doUnknownException(Exception e){
        System.out.println("异常你来啦");
        return new Result(Code.SYSTEM_UNKNOWN_ERR, null, "系统繁忙，请稍后尝试");
    }
}
```



### 项目异常分类

业务异常（BusinessException）

- 规范的用户行为产生的异常
- 不规范的用户行为操作产生的异常

系统异常（SystemException）

- 项目运行过程中可预计且无法避免的异常

其他异常（Exception）

- 编程人员未预期到的异常



## 拦截器

拦截器（interceptor）是一种动态拦截方法调用的机制，在SpringMVC中动态拦截控制器方法的执行

作用：

- 在指定的方法调用前后执行预先设定的代码
- 组织原始方法的执行

### 拦截器与过滤器的区别

- 归属不同：Filter属于servlet技术，Interceptor属于SpringMVC技术
- 拦截内容不同：Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强

### 使用

1. 声明拦截器的bean，并实现HandlerInterceptor接口（注意扫描加载bean）

```java
@Component
public class ProjectInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;  //可以终止原始操作
    }

    //如果有返回值，可以读取到对应数据与页面信息，进行调整
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    //如果出现异常对象
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
        System.out.println("afterHandle");
    }
}
```

2. 定义配置类SpringMvcSupport，继承WebMvcConfigurationSupport实现addInterceptor方法
3. 但是可以使用标准接口WebMvcConfigurer简化开发（替换操作2）（侵入式强）

```java
@Configuration
@ComponentScan({"com.itheima.controller"})
@EnableWebMvc
public class SpringMvcConfig implements WebMvcConfigurer {
    @Autowired
    private ProjectInterceptor projectInterceptor;
    
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books", "/books/*");
        // 可以添加多个，添加顺序就是运行顺序（嵌套）
    }
}
```

