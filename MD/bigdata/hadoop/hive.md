## Hive内嵌模式

### 下载

```
http://archive.apache.org/dist/hive/hive-0.11.0/
```

### 设置环境变量

```
export HIVE_HOME=/home/duxiaoming/hive-0.11.0-bin
export PATH=$PATH:$HIVE_HOME/bin
export CLASSPATH=$CLASSPATH:$HIVE_HOME/lib
```

### 配置文件

1. 复制配置文件** hive-env.sh.template **并改名为 **hive-env.sh**
   ```
   cd conf/
   cp hive-env.sh.template hive-env.sh
   ```
2. 修改** hive-env.sh **文件
   ```
   HADOOP_HOME=/home/duxiaoming/hadoop-1.1.2
   export HIVE_CONF_DIR=/home/duxiaoming/hive-0.11.0-bin/conf
   ```
3. 复制配置文件 **hive-default.xml.template** 并改名为 **hive-site.xml**

### 常见错误

#### 运行hive 出错

```
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/thrift/TException
        at java.lang.Class.forName0(Native Method)
        at java.lang.Class.forName(Class.java:270)
        at org.apache.hadoop.util.RunJar.main(RunJar.java:149)
Caused by: java.lang.ClassNotFoundException: org.apache.thrift.TException
        at java.net.URLClassLoader$1.run(URLClassLoader.java:366)
        at java.net.URLClassLoader$1.run(URLClassLoader.java:355)
        at java.security.AccessController.doPrivileged(Native Method)
        at java.net.URLClassLoader.findClass(URLClassLoader.java:354)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:425)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:358)
        ... 3 more
```

修改 /home/duxiaoming/hadoop-1.1.2/conf 下的 **hadoop-env.sh** 文件

```
export HADOOP_CLASSPATH=/home/duxiaoming/hadoop-1.1.2/myclass         //原写法
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:/home/duxiaoming/hadoop-1.1.2/myclass  //修改后的写法
```

### 启动

```
hive
```

---

## Hive远程模式的安装和配置

### 准备工作 {#准备工作}

* 1.搭建好的Hadoop分布式系统
* 2.apache-hive-1.2.1-bin.tar.gz和mysql-connerctor-java-5.1.43-bin.jar

### 在mysql数据库上创建hive数据库用于保存hive元数据 {#在mysql数据库上创建hive数据库用于保存hive元数据}

```
#mysql -u root -p
>输入密码

mysql>create database hive;
mysql>alter database hive character set latin1;
```

### 安装 {#安装}

* 解压apache-hive-1.2.1

### 配置 {#配置}

* 1.由于hive需要访问mysql数据库上的元数据，所以需要导入mysql-connerctor-java-5.1.43-bin.jar到hive-1.2.1/lib目录下。

* 2.编辑配置文件

```
vim conf/hive-site.xml
```

**添加如下记录**

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://123.207.101.174:3306/hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>用户名</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>密码</value>
    </property>
</configuration>
```

### 配置简单查询的Fetch Task功能 {#配置简单查询的fetch-task功能}

简单查询：没有排序，没有函数的查询称为简单查询。  
Fetch Task功能：使用该功能执行简单查询，查询语句将不会被转化为MapReduce任务，而是直接在HDFS文件中查询数据，提高了简单查询的效率。

配置Fetch Task功能的方式：  
- hive&gt;set hive.fetch.task.conversion=more;  
或  
- \#hive –hiveconf hive.fetch.task.conversion=more  
或  
- 修改配置文件hive-site.xml 添加如下记录：

```
<property>
    <name>hive.fetch.task.conversion</name>
    <value>more</value>
</property>
```

### 验证 {#验证}

在linux下输入hive进入命令行模式

```
hive
hive> create
```

之前，我们在mysql数据库上创建了一个用于保存hive元数据的hive数据库，此时，当看到如上显示的时候，我们就可以去检查一下hive数据库中的元数据表是否创建成功，如果创建成功则会有如下显示：

![](/assets/importhive1.png)

