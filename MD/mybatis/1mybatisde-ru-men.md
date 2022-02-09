# 1、Mybatis的入门

## Ⅰ. SqlMapConfig.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 和spring整合后 environments配置将废除 -->
    <environments default="development">
        <environment id="development">
            <!-- 使用jdbc事务管理 -->
            <transactionManager type="JDBC" />
            <!-- 数据库连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url"
                    value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>

</configuration>
```

SqlMapConfig.xml是mybatis核心配置文件，上边文件的配置内容为数据源、事务管理。

---

## Ⅱ. PoJo类

Po类作为mybatis进行sql映射使用，po类通常与数据库表对应，User.java如下：

```java
public class User {
    private int id;
    private String username;// 用户姓名
    private String sex;// 性别
    private Date birthday;// 生日
    private String address;// 地址

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }
.....

}
```

---

## Ⅲ. sql映射文件

在Classpath下的Sqlmap目录下创建Sql映射文件Users.xml：

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.test.mapper.User">

    <select id="findUserById" parameterType="int" resultType="com.test.pojo.User">
        select * from user where id=#{id}
    </select>

     <select id="findUserByUserName" parameterType="string" resultType="com.test.pojo.User">
         select * from user where username like '%${value}%' 
     </select>
     
</mapper>
```

1. id:sql语句唯一标识 parameterType:指定传入参数类型 resultType:返回结果集类型

2. \#{}占位符:起到占位作用,如果传入的是基本类型\(string,long,double,int,boolean,float等\),那么\#{}中的变量名称可以随意写;

3. 如果返回结果为集合,可以调用selectList方法,这个方法返回的结果就是一个集合,所以映射文件中应该配置成集合泛型的类型

4. ${}拼接符:字符串原样拼接,如果传入的参数是基本类型\(string,long,double,int,boolean,float等\),那么${}中的变量名称**必须是value**注意:拼接符有sql注入的风险,所以慎重使用

---

## Ⅳ. 加载映射文件

mybatis框架需要加载映射文件，将Users.xml添加在SqlMapConfig.xml，如下：

```java
<mappers>
    <mapper resource="com/test/mapper/User.xml" />
</mappers>
```

---

## Ⅴ. 测试程序

### 查询

```java
public class UserTest {
    // 会话工厂
    private SqlSessionFactory sqlSessionFactory;

    @Before
    public void createSqlSessionFactory() throws IOException {
        // 配置文件
        String resource = "SqlMapConfig.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);

        // 使用SqlSessionFactoryBuilder从xml配置文件中创建SqlSessionFactory
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    }

    // 根据 id查询用户信息
    @Test
    public void testFindUserById() {
        // 数据库会话实例
        SqlSession sqlSession = null;
        try {
            // 创建数据库会话实例sqlSession
            sqlSession = sqlSessionFactory.openSession();
            // 查询单个记录，根据用户id查询用户信息
            User user = sqlSession.selectOne("com.test.mapper.User.findUserById", 10);
            // 输出用户信息
            System.out.println(user);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (sqlSession != null) {
                sqlSession.close();
            }
        }

    }

}
```

---

### 增加：

1. \#{}:如果传入的是pojo类型,那么\#{}中的变量名称必须是pojo中对应的属性.属性.属性.....

2. 如果要返回数据库自增主键:可以使用select LAST\_INSERT\_ID\(\)

   1. keyProperty:将返回的主键放入传入参数的Id中保存.

   2. order:当前函数相对于insert语句的执行顺序,在insert前执行是before,在insert后执行是AFTER

   3. resultType:id的类型,也就是keyproperties中属性的类型

```java
<insert id="insertUser" parameterType="com.test.pojo.User">
    <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
        select
        LAST_INSERT_ID()
    </selectKey>
    insert into user (username,birthday,sex,address)
    values(#{username},#{birthday},#{sex},#{address})
</insert>
```

```java
@Test
public void testInsertUser() throws Exception{
    String resource = "SqlMapConfig.xml";
    //通过流将核心配置文件读取进来
    InputStream inputStream = Resources.getResourceAsStream(resource);
    //通过核心配置文件输入流来创建会话工厂
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
    //通过工厂创建会话
    SqlSession openSession = factory.openSession();

    User user = new User();
    user.setUsername("赵四");
    user.setBirthday(new Date());
    user.setSex("1");
    user.setAddress("北京昌平");
    System.out.println("====" + user.getId());

    openSession.insert("com.test.mapper.User.insertUser", user);
    //提交事务(mybatis会自动开启事务,但是它不知道何时提交,所以需要手动提交事务)
    openSession.commit();

    System.out.println("====" + user.getId());
}
```

可以通过user获取到id。

---

### 删除：

```java
<delete id="delUserById" parameterType="int">
    delete from user where
    id=#{id}
</delete>
```

```java
@Test
public void testDelUserById()throws Exception{
    String resource = "SqlMapConfig.xml";
    //通过流将核心配置文件读取进来
    InputStream inputStream = Resources.getResourceAsStream(resource);
    //通过核心配置文件输入流来创建会话工厂
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
    //通过工厂创建会话
    SqlSession openSession = factory.openSession();

    openSession.delete("com.test.mapper.User.delUserById", 28);
    //提交
    openSession.commit();
}
```

---

### 更新：

注意更新操作传入的类型

```java
<update id="updateUserById" parameterType="com.test.pojo.User">
    update user set
    username=#{username} where id=#{id}
</update>
```

```java
@Test
public void testUpdateUserById() throws Exception {
    String resource = "SqlMapConfig.xml";
    // 通过流将核心配置文件读取进来
    InputStream inputStream = Resources.getResourceAsStream(resource);
    // 通过核心配置文件输入流来创建会话工厂
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
    // 通过工厂创建会话
    SqlSession openSession = factory.openSession();

    User user = new User();
    user.setId(28);
    user.setUsername("王麻子");
    openSession.update("com.test.mapper.User.updateUserById", user);

    // 提交
    openSession.commit();
}
```



