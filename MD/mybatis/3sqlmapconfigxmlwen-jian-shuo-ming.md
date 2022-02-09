# 3、SqlMapConfig.xml文件说明

## properties

```java
<properties resource="db.properties"></properties>
```

```java
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/springmvc?characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
```

## typeAliases

> 使用包扫描的方式批量定义别名 定以后别名等于类名,不区分大小写,
>
> 但是建议按照java命名规则来,首字母小写,以后每个单词的首字母大写

```
<typeAliases>
    <!-- 使用包扫描的方式批量定义别名 定以后别名等于类名,不区分大小写,但是建议按照java命名规则来,首字母小写,以后每个单词的首字母大写 -->
    <package name="com.test.pojo" />
</typeAliases>
```

或者定义单个pojo类别名 （不推荐）

* type:类的全路劲名称
* alias:别名

```
<typeAliases>
    <!-- 定义单个pojo类别名 type:类的全路劲名称 alias:别名 -->
    <typeAlias type="cn.itheima.pojo.User" alias="user" />

</typeAliases>
```

### 使用包扫描的方式批量引入Mapper接口

使用规则:

1. 接口的名称和映射文件名称除扩展名外要完全相同
2. 接口和映射文件要放在同一个目录下

```java
<mappers>
    <package name="com.test.mapper" />
</mappers>
```



