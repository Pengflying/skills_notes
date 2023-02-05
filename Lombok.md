# Lombok

[TOC]

lombok 是一款在java开发中简洁化代码十分有用的插件工具，这篇博客对较为常用的几种注解进行记录，分享学习心得。
使用lombok注解，目的和作用就在于不用再去写经常反复去写的（如Getter，Setter，Constructor等）一些代码了。
首先，用到的几个注解：

## @Data

使用这个注解，就不用再去手写Getter,Setter,equals,canEqual,hasCode,toString等方法了，注解后在编译时会自动加进去。

## @AllArgsConstructor

使用后添加一个构造函数，该构造函数含有所有已声明字段属性参数

## @NoArgsConstructor

使用后创建一个无参构造函数

## @Builder

关于Builder较为复杂一些，Builder的作用之一是为了解决在某个类有很多构造函数的情况，也省去写很多构造函数的麻烦，在设计
模式中的思想是：用一个内部类去实例化一个对象，避免一个类出现过多构造函数，



## @Accessors(chain = true)

Accessor的中文含义是存取器，@Accessors用于配置getter和setter方法的生成结果，下面介绍三个属性

### fluent

fluent的中文含义是流畅的，设置为true，则getter和setter方法的方法名都是基础属性名，且setter方法返回当前对象。如下

```
@Data
@Accessors(fluent = true)
public class User {
    private Long id;
    private String name;
    
    // 生成的getter和setter方法如下，方法体略
    public Long id() {}
    public User id(Long id) {}
    public String name() {}
    public User name(String name) {}
}
```

### chain

chain的中文含义是链式的，设置为true，则setter方法返回当前对象。如下

```
@Data
@Accessors(chain = true)
public class User {
    private Long id;
    private String name;
    
    // 生成的setter方法如下，方法体略
    public User setId(Long id) {}
    public User setName(String name) {}
}
```

### prefix

prefix的中文含义是前缀，用于生成getter和setter方法的字段名会忽视指定前缀（遵守驼峰命名）。如下

```
@Data
@Accessors(prefix = "p")
class User {
	private Long pId;
	private String pName;
	
	// 生成的getter和setter方法如下，方法体略
	public Long getId() {}
	public void setId(Long id) {}
	public String getName() {}
	public void setName(String name) {}
}

```

