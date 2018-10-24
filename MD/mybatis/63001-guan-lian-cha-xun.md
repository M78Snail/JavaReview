## 6、关联查询

### 一对一关联

#### 自动映射（不推荐，偷懒）

```
public class Orders {
    private Integer id;

    private Integer userId;

    private String number;

    private Date createtime;

    private String note;

    private User user;

    ...
}
```

```java
public class CustomOrders extends Orders {

    private int uid;
    private String username;// 用户姓名
    private String sex;// 性别
    private Date birthday;// 生日
    private String address;// 地址

    ...
}
```

```java
<select id="findOrdersAndUser1" resultMap="CustomOrders">
    select a.*, b.id uid, username, birthday, sex, address
    from orders a, user b
    where a.user_id = b.id
</select>
```

#### 手动映射（推荐）

> resultMap

* id:resultMap的唯一标识

* type:将查询出的数据放入这个指定的对象中

* 注意:手动映射需要指定数据库中表的字段名与java中pojo类的属性名称的对应关系

> resultMap子标签

* id标签指定主键字段对应关系

* column:列,数据库中的字段名称

* property:属性,java中pojo中的属性名称

> association

* property:指定将数据放入Orders中的user属性中
* javaType:user属性的类型

```
public class Orders {
    private Integer id ;
    private Integer userId ;
    private String number ;
    private Date createtime ;
    private String note ;
    private User user ;
    ...
}
```

```java
<resultMap type="Orders" id="orderAndUserResultMap">
    <id column="id" property="id" />
    <result column="user_id" property="userId" />
    <result column="number" property="number" />
    <result column="createtime" property="createtime" />
    <result column="note" property="note" />

    <association property="user" javaType="com.zjipst.pojo.User">
        <id column="id" property="id" />
        <result column="username" property="username" />
        <result column="birthday" property="birthday" />
        <result column="sex" property="sex" />
        <result column="address" property="address" />
    </association>
</resultMap>

<select id="findOrdersAndUser2" resultMap="orderAndUserResultMap">
    select a.*, b.id uid, username, birthday, sex, address
    from orders a, user b
    where a.user_id = b.id
</select>
```

---

### 一对多关联

```java
public class User {
    private int id;
    private String username;// 用户姓名
    private String sex;// 性别
    private Date birthday;// 生日
    private String address;// 地址

    private List<Order> ordersList;

    ...
}
```

```java
<resultMap type="User" id="userAndOrdersResultMap">
    <id column="id" property="id" />
    <result column="username" property="username" />
    <result column="birthday" property="birthday" />
    <result column="sex" property="sex" />
    <result column="address" property="address" />

    <!-- 指定对应的集合对象关系映射 property:将数据放入User对象中的ordersList属性中 ofType:指定ordersList属性的泛型类型 -->
    <collection property="ordersList" ofType="com.zjipst.pojo.Orders">
        <id column="oid" property="id" />
        <result column="user_id" property="userId" />
        <result column="number" property="number" />
        <result column="createtime" property="createtime" />
    </collection>
</resultMap>
<select id="findUserAndOrders" resultMap="userAndOrdersResultMap">
    select a.*, b.id oid ,user_id, number, createtime
    from user a, orders b where a.id = b.user_id
</select>
```

> collection 指定对应的集合对象关系映射

* property:将数据放入User对象中的ordersList属性中

* ofType:指定ordersList属性的泛型类型



