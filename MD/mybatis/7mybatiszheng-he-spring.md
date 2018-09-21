## 7、Mybatis整合spring

### 1.Spring包头：

```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

### 2.配置数据库相关参数properties的属性：${url}

```java
<context:property-placeholder location="classpath:jdbc.properties" />
```

### 3. 数据库连接池

```java
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <!-- 配置连接池属性 -->
    <property name="driverClass" value="${jdbc.driver}" />
    <property name="jdbcUrl" value="${jdbc.url}" />
    <property name="user" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />

    <!-- c3p0连接池的私有属性 -->
    <property name="maxPoolSize" value="30" />
    <property name="minPoolSize" value="10" />
    <!-- 关闭连接后不自动commit -->
    <property name="autoCommitOnClose" value="false" />
    <!-- 获取连接超时时间 -->
    <property name="checkoutTimeout" value="10000" />
    <!-- 当获取连接失败重试次数 -->
    <property name="acquireRetryAttempts" value="2" />
</bean>
```

### 4.配置SqlSessionFactory对象

```java
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!-- 注入数据库连接池 -->
    <property name="dataSource" ref="dataSource" />
    <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
    <property name="configLocation" value="classpath:SqlMapConfig.xml" />
    <!-- 扫描entity包 使用别名 -->
    <property name="typeAliasesPackage" value="com.zjipst.pojo" />
    <!-- 扫描sql配置文件:mapper需要的xml文件 -->
    <property name="mapperLocations" value="classpath:mapper/mc/*.xml" />
</bean>
```

看情况是否使用

> &lt;!-- 扫描sql配置文件:mapper需要的xml文件 --&gt;
>
> &lt;property name="mapperLocations" value="classpath:mapper/mc/\*.xml" /&gt;

### 5.配置扫描Dao接口包，动态实现Dao接口，注入到spring容器中

```java
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- 注入sqlSessionFactory -->
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    <!-- 给出需要扫描Dao接口包 -->
    <property name="basePackage" value="com.zjipst.mapper" />
</bean>
```

### Test:

```java
public class UserMapperTest {
    private ApplicationContext applicatonContext;

    @Before
    public void setUp() throws Exception{
        String configLocation = "classpath:ApplicationContext.xml";
        applicatonContext = new ClassPathXmlApplicationContext(configLocation);
    }


    @Test
    public void testFindUserById() throws Exception{
        UserMapper userMapper = (UserMapper)applicatonContext.getBean("userMapper");

        User user = userMapper.selectByPrimaryKey(1);
        System.out.println(user);
    }

    @Test
    public void testFindUserAndSex() throws Exception{
        UserMapper userMapper = (UserMapper)applicatonContext.getBean("userMapper");

        //创建UserExample对象
        UserExample userExample = new UserExample();
        //通过UserExample对象创建查询条件封装对象(Criteria中是封装的查询条件)
        Criteria createCriteria = userExample.createCriteria();

        //加入查询条件
        createCriteria.andUsernameLike("%王%");
        createCriteria.andSexEqualTo("1");

        List<User> list = userMapper.selectByExample(userExample);
        System.out.println(list);
    }
}
```



