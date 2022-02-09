## 入门

**步骤一：创建JavaWEB项目，引入具体的开发的jar包**

* 先引入Spring框架开发的基本开发包

* 再引入Spring框架的AOP的开发包

* spring的传统AOP的开发的包

* spring-aop-4.2.4.RELEASE.jar

* com.springsource.org.aopalliance-1.0.0.jar

* aspectJ的开发包

* com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar

* spring-aspects-4.2.4.RELEASE.jar

**步骤二：创建Spring的配置文件，引入具体的AOP的schema约束**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/tx 
    http://www.springframework.org/schema/tx/spring-tx.xsd">    

</beans>
```

**步骤三：创建包结构，编写具体的接口和实现类**

CustomerDao -- 接口

CustomerDaoImpl -- 实现类

**步骤四：将目标类配置到Spring中**

```java
<!-- 配置目标对象 -->
<bean id="customerDao" class="com.test.demo2.CustomerDaoImpl" />
```

**步骤五：定义切面类**

添加切面和通知的注解

* @Aspect -- 定义**切面类的注解**

**通知类型**（注解的参数是切入点的表达式）

* @Before -- 前置通知

* @AfterReturing -- 后置通知

* @Around -- 环绕通知

* @After -- 最终通知

* @AfterThrowing -- 异常抛出通知

**具体的代码如下**

```java
@Aspect
public class MyAspectAnno {

    @Before(value = "execution(public void com.itheima.demo1.CustomerDaoImpl.save())")

    public void log() {

        System.out.println("记录日志...");

    }

}
```

**步骤六：在配置文件中定义切面类**

```java
<bean id="myAspectAnno" class="com.itheima.demo1.MyAspectAnno" />
```

**步骤七：在配置文件中开启自动代理**

```java
<!-- 开启自动代理 -->
<aop:aspectj-autoproxy />
```

---

## 自动定义切入点

```java
@Pointcut(value="execution(public void com.test.demo2.CustomerDaoImpl.update())")
public void fn(){}

/**
 * 通知类型：@Before前置通知（切入点的表达式）
 */
@Before(value = "MyAspectAnno.fn()")
public void log() {
	System.out.println("记录日志...");
}
```



