# Spring框架中标签的配置

### <bean>

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

1. id属性和name属性的区别

2. id    -- Bean起个名字，在约束中采用ID的约束，唯一

   ```
   取值要求：必须以字母开始，可以使用字母、数字、连字符、下划线、句话、冒号 id:不能出现特殊字符
   ```

3. name        -- Bean起个名字，没有采用ID的约束（了解）

   ```
   取值要求：name:出现特殊字符.如果&lt;bean&gt;没有id的话 , name可以当做id使用
   
   Spring框架在整合Struts1的框架的时候，Struts1的框架的访问路径是以/开头的，例如：/bookAction
   ```

4. class属性            -- Bean对象的全路径

5. scope属性            -- scope属性代表Bean的作用范围

6. singleton          -- 单例（默认值）

   * prototype         -- 多例，在Spring框架整合Struts2框架的时候，Action类也需要交给Spring做管理，配置把Action类配置成多例！！

   * request            -- 应用在Web项目中,每次HTTP请求都会创建一个新的Bean

   * session            -- 应用在Web项目中,同一个HTTP Session 共享一个Bean

   * globalsession        -- 应用在Web项目中,多服务器间的session

7. Bean对象的创建和销毁的两个属性配置（了解）

   * 说明：Spring初始化bean或销毁bean时，有时需要作一些处理工作，因此spring可以在创建和拆卸bean的时候调用bean的两个生命周期方法

   * init-method        -- 当bean被载入到容器的时候调用init-method属性指定的方法

   * destroy-method    -- 当bean从容器中删除的时候调用destroy-method属性指定的方法

     想查看destroy-method的效果，有如下条件

     scope= singleton有效，web容器中会自动调用，但是main函数或测试用例需要手动调用（需要使用ClassPathXmlApplicationContext的close\(\)方法）

    



