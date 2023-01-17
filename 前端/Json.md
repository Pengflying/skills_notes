# JSON

[TOC]

概念：JavaScript Object Notation。JavaScript对象表示法

由于语法简单，层次鲜明，现多用于作为数据载体，在网络中进行数据传输。

JavaScript对象

```javascript
{
    name:"zhangsan",
    age:23,
    city:"北京"
}
```

JSON对象

```javascript
{
     "name:"zhangsan",
    "age":23,
    "city:"北京"
}
```

value的数据类型为

- 数字（整数或浮点数）
- 字符串（在双引号中）
- 逻辑值（true或false）
- 数组（在方括号中）
- 对象（在花括号中）
- null



## JSON数据和Java对象转换

请求数据：JSON字符串转为Java对象

响应数据：Java对象转为JSON字符串

### Fastjson

Fastjson是阿里巴巴提供的一个Java语句编写的高性能功能完善的JSON库，是目前Java语言中最快的JSON库，可以实现Java对象和JSON字符串的相互转化。

导入依赖坐标

```xml
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>fastjson</artifactId>
<version>1.2.76</version>
</dependency>
```

```java
//Java对象转JSON
String jsonStr = JSON.toJSONString(obj);

//JSON字符串转Java对象
User user = JSON.parseObject(jsonStr, User.class);
```

