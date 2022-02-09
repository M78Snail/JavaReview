### 什么是AOP的技术？

> 在软件业，AOP为Aspect Oriented Programming的缩写，意为：面向切面编程。

AOP是一种编程范式，隶属于软工范畴，指导开发者如何组织程序结构  
AOP最早由AOP联盟的组织提出的,制定了一套规范.Spring将AOP思想引入到框架中,必须遵守AOP联盟的规范  
   通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术  
   AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型  
   利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率

AOP:面向切面编程.\(思想.---解决OOP遇到一些问题\)

AOP采取横向抽取机制，取代了传统纵向继承体系重复性代码（性能监视、事务管理、安全检查、缓存）

为什么要学习AOP？

可以在不修改源代码的前提下，对程序进行增强！！

---

### AOP配置和编写

**CustomerDaoImpl**

```java
public class CustomerDaoImpl implements CustomerDao {

    public void save() {
        // 模拟异常
        // int a = 10/0;    
        System.out.println("保存客户...");
    }

    public void update() {
        System.out.println("修改客户...");
    }

}
```

切面类：**MyAspectXml**

```java
public class MyAspectXml {

    /**
     * 通知（具体的增强）
     */
    public void log(){
        System.out.println("记录日志...");
    }
}
```

配置文件：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- bean definitions here -->

    <!-- 配置客户的dao -->
    <bean id="customerDao" class="com.test.demo.CustomerDaoImpl" />

    <!-- 配置切面类 -->
    <bean id="myAspectXml" class="com.test.demo.MyAspectXml" />

    <!-- 配置AOP -->
    <aop:config>
        <!-- 配置切面类：切入点 + 通知（类型） -->
        <aop:aspect ref="myAspectXml">
            <aop:before method="log" pointcut="execution(public void com.test.demo.CustomerDaoImpl.save())"/>
        </aop:aspect>
    </aop:config>
</beans>
```

* 配置客户的dao
* 配置切面类
* 配置AOP
  * 配置切面类：切入点
  * 通知（类型）

---

### **切入点的表达式**

再配置切入点的时候，需要定义表达式，重点的格式如下：execution\(public \* \*\(..\)\)，具体展开如下：

* 切入点表达式的格式如下：

  * > execution\(\[修饰符\] 返回值类型 包名.类名.方法名\(参数\)\)

* 修饰符可以省略不写，不是必须要出现的。

* 返回值类型是不能省略不写的，根据你的方法来编写返回值。可以使用 \* 代替。

* 包名例如：com.test.demo.BookDaoImpl

  * 首先com是不能省略不写的，但是可以使用 \* 代替
  * 中间的包名可以使用 \* 号代替
  * 如果想省略中间的包名可以使用 .. 

* 类名也可以使用 \* 号代替，也有类似的写法：\*DaoImpl

* 方法也可以使用 \* 号代替

* 参数如果是一个参数可以使用 \* 号代替，如果想代表任意参数使用 ..

---

### **AOP的通知类型**

### 1. 前置通知

* 在目标类的方法执行之前执行。

* 配置文件信息：&lt;aop:after method="before" pointcut-ref="myPointcut3"/&gt;

* 应用：可以对方法的参数来做校验

### 2. 最终通知

* 在目标类的方法执行之后执行，如果程序出现了异常，最终通知也会执行。

* 在配置文件中编写具体的配置：&lt;aop:after method="after" pointcut-ref="myPointcut3"/&gt;

* 应用：例如像释放资源

### 3. 后置通知

* 方法正常执行后的通知。

* 在配置文件中编写具体的配置：&lt;aop:after-returning method="afterReturning" pointcut-ref="myPointcut2"/&gt;

* 应用：可以修改方法的返回值

### 4. 异常抛出通知

* 在抛出异常后通知

* 在配置文件中编写具体的配置：&lt;aop:after-throwing method="afterThorwing" pointcut-ref="myPointcut3"/&gt;

* 应用：包装异常的信息

### 5. 环绕通知\[事务的管理\]

* 方法的执行前后执行。

* 在配置文件中编写具体的配置：&lt;aop:around method="around" pointcut-ref="myPointcut2"/&gt;

* 要注意：目标的方法默认不执行，需要使用ProceedingJoinPoint对来让目标对象的方法执行。

```java
public void around(ProceedingJoinPoint joinPoint){
    System.out.println("环绕通知1...");
    try {
        // 手动让目标对象的方法去执行
        joinPoint.proceed();
    } catch (Throwable e) {
        e.printStackTrace();
    }
    System.out.println("环绕通知2...");
}
```



