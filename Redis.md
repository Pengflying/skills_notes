# Redis

[TOC]

Redis是一个基于内存的k-v结构数据库，可以用作：数据库、缓存和消息中间件

- 基于内存存储，读写性能高
- 适合存储热点数据（热点商品、资讯、新闻）
- 企业应用广泛

NoSql,不仅仅是SQL,泛指非关系数据库。NoSql数据库并不是要取代关系型数据库，而是关系型数据库的补充。

## Redis应用场景

- 缓存
- 任务队列
- 消息队列
- 分布式锁



## 五种常见的数据结构

- 字符串 string
  - 普通字符串，常用 k-v
- 哈希 hash
  - 适合存储对象
  - key-field-value
- 列表 list
  - list按照插入顺序排序，可以重复元素
- 集合 set
  - set无序集合，没有重复元素
- 有序集合 sorted set
  - 有序集合，没有重复元素



### 字符串类型的常用命令

- SET key value ：设置指定key的值

- GET key：获取指定key的值
- SETEX key seconds value：设置指定key的值，并将key的过期时间设为seconds
- SETNX key value：只有在key不存在时设置key的值



### 哈希hash操作命令

Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象

- HSET key field value：将哈希表key中的字段field的值设为value
- HGET key field：获取存储在哈希表中指定字段的值
- HDEL key field：删除存储在哈希表中的指定字段
- HKEYS key：获取哈希表中所有字段
- HVALS key：获取哈希表中所有值
- HGETALL key：获取在哈希表中指定key的所有字段和值



### 列表list操作命令

Redis列表是将简单的字符串列表，按照插入顺序，常用命令：

- LPUSH key value1 [value2]：将一个或多个值插入到列表头部
- LRANGE key start stop：获取列表指定范围内的元素
- RPOP key：移除并获取列表最后一个元素
- LLEN key：获取列表长度
- BRPOP key1 [key2] timeout：移除并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。



### 集合set操作命令

Redis set是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据

- SADD key member1 [member2]：向集合添加一个或多个成员
- SMEMBER key：返回集合中的所有成员
- SCARD key：获取集合的成员数
- SINTER key1 [key2]：返回给定所有集合的交集
- SUNION key1 [key2]：返回所有给定集合的并集
- SDIFF key1 [key2]：返回给定所有集合的差距
- SREM key member1 [member2]：移除集合中一个或多个成员



### 有序集合sorted set操作命令

Redis sorted set有序集合是string类型元素的集合，且不允许重复的成员。每个元素都会关联一个double类型的分数（score）。redis正是通过分数来为集合中的成员进行从小到大排序。有序集合的成员是唯一的，但分数却可以重复。

- ZADD key score1 member [score2 member2]：像有序集合添加一个或多个成员，或者更新已存在成员的分数
- ZRANGE key start stop [WITHSCORES]：通过索引区间返回有序集合中指定区间内的成员
- ZINCRBY key increment member：有序集合中对指定成员的分量加上增量increment
- ZREM key member [member ...]：移除有序集合中的一个或多个成员



### 其他操作命令

- KEYS pattern：查找所有符合给定模式（pattern）的key
- EXISTS key：检查给定key是否存在
- TYPE key：返回key所存储的值的类型
- TTL key：返回给定key的剩余生存时间（TTL, time to live），以秒为单位
- DEL key：该命令用于在key存在时删除key



## 在Spring中使用Redis

Spring Data Redis中提供了一个高度封装的类：RedisTemplate,针对jedis客户端中大量api进行了归类封装，将同一类型操作封装为operation接口，具体分类如下

ValueOperations：简单的K-V操作

SetOperations：set类型数据操作

ZSetOperations：zset类型数据操作

HashOpeartions：针对map类型的数据操作

ListOperations：针对list类型的数据操作



```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```yml
spring:
  application:
    name: ruiji-delivery
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/ruiji?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8
      username: root
      password: 123456
# 设置redis
  redis:
    host: 192.168.147.128
    port: 6379
    password: 123456
    database: 0
  cache:
    redis:
      time-to-live: 1800000  # 设置缓存有效期
```

key的序列化方式(可配可不配)

```java
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        return redisTemplate;
    }
}
```



用户数量多，系统访问量大。

频繁访问数据库，系统性能下降，用户体验差。