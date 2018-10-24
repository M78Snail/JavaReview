# 4、输入映射和输出映射

### 输入映射

传入Vo类型，一般不使用HashMap

```java
public class QueryVo {

    private User user;

    private List<Integer> ids;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    public List<Integer> getIds() {
        return ids;
    }

    public void setIds(List<Integer> ids) {
        this.ids = ids;
    }

}
```

```java
<select id="findUserbyVo" parameterType="QueryVo" resultType="User">
    select * from user where username like '%${user.username}%' and
    sex=#{user.sex}
</select>
```

### 输出映射

> 只有返回结果为一行一列的时候,那么返回值类型才可以指定成基本类型

```java
<select id="findUserCount" resultType="java.lang.Integer">
    select count(*) from user
</select>
```



