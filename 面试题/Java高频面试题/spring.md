[TOC]

# Spring框架中Bean的创建过程是怎样的？

首先，简单来说，Spring框架中的Bean经过四个阶段：实例化-》属性赋值-》初始化-》销毁

然后，具体来说，Spring中Bean经过了以下几个步骤：

1. 实例化： new xxx();

   两个时机：

   1. 当客户端向容器申请一个Bean时
   2. 当容器在初始化一个Bean时发现还需要依赖另一个Bean。BeanDefinition对象保存。

2. 设置对象属性（依赖注入）：Spring通过BeanDefinition找到对象依赖的其他对象，并将这些对象赋予当前对象。

3. 处理Aware接口：Spring会检测对象是否实现了xxxAware接口，如果实现了，就会调用对应的方法。BeanNameAware、BeanClassLoaderAware、BeanFactoryAware、ApplicationContextAware

4. BeanPostProcessor前置处理：调用BeanPostProcessor的postProcessBeforeInitialization方法

5. InitializingBean: Spring检测人对象如果实现了这个接口，就会执行他的afterPropertiesSet()方法，定制初始化逻辑。

6. init-method: < bean init-method=xxx>如果Spring发现Bean配置了这个属性，就会调用他的配置方法，执行初始化逻辑。@PostConstruct

7. BeanPostProcessor后置处理：调用BeanPostProcessor的postProcessAfterInitialization方法

   到这里，这个Bean的创建过程就完成了，Bean就可以正常使用了。

8. DisposableBean: 当Bean实现了这个接口，在对象销毁前就会调用destory()方法。

9. destory-method：<bean destory-method=xxx> @PreDestory



# Spring框架中的Bean是安全的么？如果线程不安全，要如何处理？

Spring容器本身没有提供Bean的线程安全策略，因此，也可以说Spring容器中的Bean不是线程安全的。

要如何处理线程安全问题，就要分情况来分析。

Spring中的作用域：

1. singleton
2. prototype: 为每个Bean请求创建实例。
3. request：为每个request请求创建一个实例，请求完成后失效。
4. session：与request是类似的
5. global-session：全局作用域。

对于线程安全问题：

1 》 对于prototype作用域，每次都是生成一个新的对象，所以不存在线程安全问题。

2 》singleton作用域：默认就是线程不安全的。但是对于开发中大部分的Bean，其实是无状态的，不需要保证线程安全。所以在平常的MVC开发中，是不会有线程安全问题的。



无状态表示这个实例没有属性对象，不能保存数据，是不变的类。比如controller、service、dao

有状态表示示例是有属性对象，可以保存数据，是线程不安全的，比如pojo



但是如果要保证线程安全，可以将Bean的作用域改为prototype比如像Model  View。

另外还可以采用ThreadLocal来解决线程安全问题。ThreadLocal为每个线程保存一个副本变量，每个线程只操作自己的副本变量。



## Spring如何处理循环依赖问题？

三级缓存

循环依赖：多个对象之间存在循环的引用关系，在初始化的过程中，就会出现“先有蛋还是先有鸡”的问题。

@Lazy注解：解决构造方法造成的循环依赖问题。

@Autowired注解：对应空参构造



单例池

二级缓存：a: new A()

会保存new出来的不完整对象，这样当单例池中找不到依赖的属性时，就可以先从二级缓存中获取到不完整对象，完成对象创建，在后续的依赖注入过程中，将单例池中对象的引用关系调整完成。

三级缓存：b: Proxy(AOP)

想要拿到B的动态代理对象。保存动态代理的配置信息。发现有循环依赖，提前进行这个对象的AOP动态代理。

如果引用的对象配置了AOP，那在单例池中最终会需要注入动态代理对象，而不是原对象。而生成动态代理要在对象初始化完成之后才开始的。于是Spring增加三级缓存，保存所有对象的动态代理配置信息。在发现有循环依赖时，将这个对象的动态代理信息获取出来，提前进行AOP，进行动态代理。

**不完全**