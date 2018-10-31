## 依赖注入

> Dependency Injection 依赖注入.需要有IOC的环境,Spring创建这个类的过程中,Spring将类的依赖的属性设置进去.

### 注入字符串：

```java
public class UserServiceImpl implements UserService {

    private String name;

    public void setName(String name) {
        this.name = name;
    }

    public void sayHello() {
        System.out.println("Hello Spring!! " + name);
    }

    public void init() {
        // 编写任何代码
        System.out.println("初始化...");
    }

    public void destory() {
        System.out.println("销毁...");
    }
}
```

```java
!-- 使用bean标签 -->
<bean id="userService" class="com.zjipst.demo.UserServiceImpl">
    <property name="name" value="小凤" />
</bean>
```

---

### 注入对象：

```java
public class CustomerServiceImpl {

    // 提供成员属性，提供set方法
    private CustomerDaoImpl customerDao;

    public void setCustomerDao(CustomerDaoImpl customerDao) {
        this.customerDao = customerDao;
    }

    public void save() {
        System.out.println("我是业务层service....");
        // 原来编写方式
        // new CustomerDaoImpl().save();

        // Spring的方式
        customerDao.save();
    }

}
```

```java
<!-- 演示的依赖注入 -->
<bean id="customerDao" class="com.zjipst.demo.CustomerDaoImpl" />

<bean id="customerService" class="com.zjipst.demo.CustomerServiceImpl">
	<property name="customerDao" ref="customerDao" />
</bean>
```

依赖注入：

* Service需要Dao时，在Service中添加成员属性，然后提供set方法
* property 添加依赖

---

### 注入构造：

```java
public class Car1 {

    private String cname;
    private Double price;

    public Car1(String cname, Double price) {
        this.cname = cname;
        this.price = price;
    }

    @Override
    public String toString() {
        return "Car1 [cname=" + cname + ", price=" + price + "]";
    }

}
```

```java
public class Person {

    private String pname;
    private Car1 car1;

    public Person(String pname, Car1 car1) {
        this.pname = pname;
        this.car1 = car1;
    }

    @Override
    public String toString() {
        return "Person [pname=" + pname + ", car1=" + car1 + "]";
    }
}
```

```java
<!-- 演示的构造方法的注入的方式 -->
<bean id="car1" class="com.zjipst.demo.Car1">
    <!-- 
    <constructor-arg name="cname" value="奇瑞QQ"/> 
    <constructor-arg name="price" value="25000"/> 
    -->

    <constructor-arg index="0" value="囚车" />
    <constructor-arg index="1" value="545000" />
</bean>

<bean id="person" class="com.zjipst.demo.Person">
    <constructor-arg name="pname" value="美美" />
    <constructor-arg name="car1" ref="car1" />
</bean>
```

---

### 注入集合和配置文件

```java
public class User {

    private String[] arrs;

    public void setArrs(String[] arrs) {
        this.arrs = arrs;
    }

    private List<String> list;

    public void setList(List<String> list) {
        this.list = list;
    }

    private Map<String, String> map;

    public void setMap(Map<String, String> map) {
        this.map = map;
    }

    private Properties pro;

    public void setPro(Properties pro) {
        this.pro = pro;
    }

    @Override
    public String toString() {
        return "User [arrs=" + Arrays.toString(arrs) + ", list=" + list + ", map=" + map + ", pro=" + pro + "]";
    }
}
```

```java
<bean id="user" class="com.zjipst.demo.User">
    <property name="arrs">
        <list>
            <value>哈哈</value>
            <value>呵呵</value>
            <value>嘿嘿</value>
        </list>
    </property>

    <property name="list">
        <list>
            <value>美美</value>
            <value>小凤</value>
        </list>
    </property>

    <property name="map">
        <map>
            <entry key="aaa" value="小苍" />
            <entry key="bbb" value="小泽" />
        </map>
    </property>

    <property name="pro">
        <props>
            <prop key="username">root</prop>
            <prop key="password">1234</prop>
        </props>
    </property>
</bean>
```

---

### 引入其他的配置文件

```java
<import resource="applicationContext2.xml"/>
```



