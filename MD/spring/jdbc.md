## 配置连接池和模版类

```java
<!-- 配置C3P0的连接池 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver" />
    <property name="jdbcUrl" value="jdbc:mysql:///spring_day03" />
    <property name="user" value="root" />
    <property name="password" value="root" />
</bean>

<!-- 配置JDBC的模板类 -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource" />
</bean>
```

有个比较恶心的地方，就是mysql如果你的字符集没有设置，或者没有设置好的话，默认是latin1，那么你的utf-8直接传过来还是会出问题。

因此mysql的url要这样写：

```java
//useUnicode表示允许使用自定义的Unicode,  
//characterEncoding是给定自定义的Unicode是什么  
String url="jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=utf8"
```

我之前遇到中文在数据库中显示？？？（问号）的问题，就是因为没有拼接上这两个参数。因此mysql解析从后台代码传过去的字符串的时候，通过编码类型去解析，所以就显示不出正确的中文！

## mysql字符集的设置

其实应该先分清楚有哪些字符集可以设置？

**1）mysql的整个服务的字符集**

* 可以通过**show variables like "%chara%";**这个命令先查看下自己的字符集都是什么

* **character\_set\_client**为客户端编码方式；

* **character\_set\_connection**为建立连接使用的编码方式；

* **character\_set\_database**为数据库的编码方式;

* **character\_set\_results**是结果集的编码方式；

* **character\_set\_server**为数据库服务器的编码方式。![](/assets/importmysql1.1.png)

只要保证以上采用的编码方式一样，就不会出现乱码问题。

可以用如下的命令去修改：

**set character\_set\_database="utf8"**

以此类推，改成如上图所示（或者符合自己的要求即可）。

然后可能会出现重启完mysql修改的值就失效。可以在my.ini的文件中的\[mysqld\]标签中设置：

> **default-character-set=utf8**（但是这个是老版本的设置方式，具体什么版本，没测过实在抱歉）
>
> 据说新版本是：**character\_set\_server=utf8**这个来代替之前的设置（大家可以自己尝试下哪个生效）

设置完之后，mysql重启下，应该字符编码就没问题了

**2）mysql当中，用户所使用的某个数据库的字符集**

这个就是创建数据库的时候设置的字符编码

**CREATE DATABASE \`test\` DEFAULT CHARACTER SET utf8 COLLATE utf8\_general\_ci**

utf8\_general\_ci是一种校对规则，有兴趣可以自己研究下。

也可以在连接工具上直接编辑数据库，来修改数据库的字符集！

**3）某个具体数据库中的某张表的字符集**

这个就是创建某张表的时候指定字符编码

```
CREATE TABLE mytable(
     id varchar(40) NOT NULL default '', 
     userId varchar(40) NOT NULL default ''
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

也可以在连接工具上直接改变表，来修改表的字符集！

### 转义

在xml文件中有以下几类字符要进行转义替换：

| 小于号 | &lt; | &lt; |
| :---: | :---: | :---: |
| 大于号 | &gt; | &gt; |
| 和 | & | &amp; |
| 单引号 | ' | &apos; |
| 双引号 | ‘’ | &quot; |



