# MySQL索引类型详解

MySQL索引类型包括：

#### **（1）普通索引**

这是最基本的索引，它没有任何限制。它有以下几种创建方式：

- 创建索引

```
CREATE INDEX indexName ON mytable(username(length)); 
```

如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length，下同。

- 修改表结构

```
ALTER mytable ADD INDEX [indexName] ON (username(length)) 
```

- 创建表的时候直接指定

```
CREATE TABLE mytable( ID INT NOT NULL, username VARCHAR(16) NOT NULL,   INDEX [indexName] (username(length))); 
```

-  删除索引的语法：

DROP INDEX [indexName] ON mytable;

#### **（2）唯一索引**

它与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。它有以下几种创建方式：

- 创建索引

```
CREATE UNIQUE INDEX indexName ON mytable(username(length)) 
```

- 修改表结构

```
ALTER mytable ADD UNIQUE [indexName] ON (username(length)) 
```

- 创建表的时候直接指定

```
CREATE TABLE mytable(   ID INT NOT NULL,    username VARCHAR(16) NOT NULL,   UNIQUE [indexName] (username(length))); 
```

#### **（3）主键索引**

它是一种特殊的唯一索引，不允许有空值。一般是在建表的时候同时创建主键索引：

```
CREATE TABLE mytable(   ID INT NOT NULL,    username VARCHAR(16) NOT NULL,   PRIMARY KEY(ID)   );  
```

当然也可以用 ALTER 命令。记住：一个表只能有一个主键。

#### **（4）组合索引**

为了形象地对比单列索引和组合索引，为表添加多个字段：

```
CREATE TABLE mytable(   ID INT NOT NULL,    username VARCHAR(16) NOT NULL,   city VARCHAR(50) NOT NULL,   age INT NOT NULL  );  
```

为了进一步榨取MySQL的效率，就要考虑建立组合索引。就是将 name, city, age建到一个索引里：

```
ALTER TABLE mytable ADD INDEX name_city_age (name(10),city,age); 
```

建表时，usernname长度为 16，这里用 10。这是因为一般情况下名字的长度不会超过10，这样会加速索引查询速度，还会减少索引文件的大小，提高INSERT的更新速度。

如果分别在 usernname，city，age上建立单列索引，让该表有3个单列索引，查询时和上述的组合索引效率也会大不一样，远远低于我们的组合索引。虽然此时有了三个索引，但MySQL只能用到其中的那个它认为似乎是最有效率的单列索引。

#### 最左前缀原则

建立这样的组合索引，其实是相当于分别建立了下面三组组合索引：

```
usernname,city,age   usernname,city   usernname  
```

为什么没有 city，age这样的组合索引呢？这是因为MySQL组合索引“最左前缀”的结果。简单的理解就是只从最左面的开始组合。并不是只要包含这三列的查询都会用到该组合索引，下面的几个SQL就会用到这个组合索引：

```
SELECT * FROM mytable WHREE username="admin" AND city="郑州"  
SELECT * FROM mytable WHREE username="admin" 
```

而下面几个则不会用到：

```
SELECT * FROM mytable WHREE age=20 AND city="郑州"  
SELECT * FROM mytable WHREE city="郑州"
```

