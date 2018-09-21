## 2、Dao的开发方法

## 原生Dao的开发方法

* 不怎么用

## 动态代理方式

mapper接口代理实现编写规则:

1. **映射文件中namespace要等于接口的全路径名称**

2. **映射文件中sql语句id要等于接口的方法名称**

3. **映射文件中传入参数类型要等于接口方法的传入参数类型**

4. **映射文件中返回结果集类型要等于接口方法的返回值类型**

**UserMapper.java**

```java
public interface UserMapper {

    public User findUserById(Integer id);

    //动态代理形势中,如果返回结果集问List,那么mybatis会在生成实现类的使用会自动调用selectList方法
    public List<User> findUserByUserName(String userName);

    public void insertUser(User user);
}
```

**UserMapper.xml**

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.zjipst.mapper.UserMapper">

    <select id="findUserById" parameterType="int" resultType="com.zjipst.pojo.User">
        select * from user where id=#{id}
    </select>

    <select id="findUserByUserName" parameterType="string" resultType="user">
        select * from user where username like '%${value}%'
    </select>

    <insert id="insertUser" parameterType="com.zjipst.pojo.User" >
        <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
            select LAST_INSERT_ID()
        </selectKey>
        insert into user (username,birthday,sex,address) values(#{username},#{birthday},#{sex},#{address})
    </insert>
</mapper>
```

**通过getMapper方法来实例化接口**

> **UserMapper mapper = openSession.getMapper\(UserMapper.class\);**

```java
public class UserMapperTest {
    private SqlSessionFactory factory;

    //作用:在测试方法前执行这个方法
    @Before
    public void setUp() throws Exception{
        String resource = "SqlMapConfig.xml";
        //通过流将核心配置文件读取进来
        InputStream inputStream = Resources.getResourceAsStream(resource);
        //通过核心配置文件输入流来创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(inputStream);
    }

    @Test
    public void testFindUserById() throws Exception{
        SqlSession openSession = factory.openSession();
        //通过getMapper方法来实例化接口
        UserMapper mapper = openSession.getMapper(UserMapper.class);

        User user = mapper.findUserById(1);
        System.out.println(user);
    }

    @Test
    public void testFindUserByUserName() throws Exception{
        SqlSession openSession = factory.openSession();
        //通过getMapper方法来实例化接口
        UserMapper mapper = openSession.getMapper(UserMapper.class);

        List<User> list = mapper.findUserByUserName("王");

        System.out.println(list);
    }

    @Test
    public void testInsertUser() throws Exception{
        SqlSession openSession = factory.openSession();
        //通过getMapper方法来实例化接口
        UserMapper mapper = openSession.getMapper(UserMapper.class);

        User user = new User();
        user.setUsername("老王");
        user.setSex("1");
        user.setBirthday(new Date());
        user.setAddress("北京昌平");

        mapper.insertUser(user);

        openSession.commit();
    }
}
```



