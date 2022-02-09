## 5、动态sql

### IF

```java
<select id="findUserByUserNameAndSex" parameterType="user"
    resultType="user">
    select * from user where 1=1
    <if test="username != null and username != ''">
        and username like '%${username}%'
    </if>
    <if test="sex != null and sex !=''">
        and sex=#{sex}
    </if>
</select>
```

根据用户名和性别进行动态查询。

---

### WHERE

```java
<select id="findUserByUserNameAndSex" parameterType="user"
    resultType="user">
    select * from user
    <where>
        <if test="username != null and username != ''">
            and username like '%${username}%'
        </if>
        <if test="sex != null and sex !=''">
            and sex=#{sex}
        </if>
    </where>
</select>
```

where标签作用:

* 会自动向sql语句中添加where关键字

* 会去掉第一个条件的and关键字

---

### 封装sql条件

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.test.mapper.UserMapper">

    <sql id="user_Where">
        <where>
            <if test="username != null and username != ''">
                and username like '%${username}%'
            </if>
            <if test="sex != null and sex !=''">
                and sex=#{sex}
            </if>
        </where>
    </sql>


    <select id="findUserByUserNameAndSex" parameterType="user"
        resultType="user">
        select * from user
        <!-- 调用sql条件 -->
        <include refid="user_where"></include>
    </select>
</mapper>
```

---

### foreach

```java
<select id="findUserByIds" parameterType="QueryVo" resultType="User">
    select * from user
    <where>
        <if test="ids != null">
            <foreach collection="ids" item="id" open=" id in (" close=")"
                separator=",">
                #{id}
            </foreach>
        </if>
    </where>
</select>
```

* foreach:循环传入的集合参数

* collection:传入的集合的变量名称

* item:每次循环将循环出的数据放入这个变量中

* open:循环开始拼接的字符串

* close:循环结束拼接的字符串

* separator:循环中拼接的分隔符



