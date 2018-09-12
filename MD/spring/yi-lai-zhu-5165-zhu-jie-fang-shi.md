## 依赖注入--注解方式

### 添加@Component注解

```java
@Component(value="userService")
// @Scope(value="prototype")
public class UserServiceImpl implements UserService {
    ...
}
```

### 开启注解的扫描

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->

    <!-- 开启注解的扫描 -->
    <context:component-scan base-package="com.zjipst"/>

</beans>
```

---

## 常用注解

* @Controller   --作用在web层

* @Service            -- 作用在业务层

* @Repository        -- 作用在持久层

> 说明：这三个注解是为了让标注类本身的用途清晰，Spring在后续版本会对其增强

---

## 属性注入的注解

* 如果是注入的普通类型，可以使用value注解

  * @Value            -- 用于注入普通类型

* 如果注入的是对象类型，使用如下注解

  * @Autowired        -- 默认按类型进行自动装配

  * 如果想按名称注入

    * @Qualifier    -- 强制使用名称注入

* @Resource                -- 相当于@Autowired和@Qualifier一起使用

  * 强调：Java提供的注解

  * 属性使用name属性

```java
@Component(value="userService")
// @Scope(value="prototype")
public class UserServiceImpl implements UserService {

    // 给name属性注入美美的字符串，setName方法还可以省略不写
    @Value(value="美美")
    private String name;
    /*public void setName(String name) {
        this.name = name;
    }*/

    // @Autowired 按类型自动装配
    // @Autowired
    // @Qualifier(value="userDao")        // 按名称注入

    // 是Java的注解，Spring框架支持该注解
    @Resource(name="userDao")
    private UserDao userDao;

    public void sayHell() {
        System.out.println("hello Spring!!"+name);
        userDao.save();
    }

    @PostConstruct
    public void init(){
        System.out.println("初始化...");
    }

}
```

---

## **Bean的作用范围和生命周期的注解**

1. Bean的作用范围注解

   * 注解为@Scope\(value="prototype"\)，作用在类上。值如下：
     * singleton        -- 单例，默认值
     * prototype        -- 多例

2. Bean的生命周期的配置（了解）

   * 注解如下：
     * @PostConstruct    -- 相当于init-method
     * @PreDestroy        -- 相当于destroy-method



