# Lombok



## 添加依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.4</version>
    <scope>provided</scope>
</dependency>
```



## 使用

```java
@Data
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
    private Long id;
    //姓名
    private String name;
    //手机号
    private String phone;
    //性别 0 女 1 男
    private String sex;
    //身份证号
    private String idNumber;
    //头像
    private String avatar;
    //状态 0:禁用，1:正常
    private Integer status;
}

```



## **常用的几个注解**

 @Data ： 注在类上，提供类的get、set、equals、hashCode、canEqual、toString方法
 @AllArgsConstructor ： 注在类上，提供类的全参构造
 @NoArgsConstructor ： 注在类上，提供类的无参构造
 @Setter ： 注在属性上，提供 set 方法
 @Getter ： 注在属性上，提供 get 方法
 @EqualsAndHashCode ：    注在类上，提供对应的 equals 和 hashCode 方法
 @Log4j/@Slf4j   ： 注在类上，提供对应的 Logger 对象，变量名为 log





## 优缺点

### **优点：**

- 能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
- 让代码变得简洁，不用过多的去关注相应的方法
- 属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

### **缺点：**

- 不支持多种参数构造器的重载
- 虽然省去了手动创建getter/setter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度



ref:  https://www.jianshu.com/p/c1ee7e4247bf