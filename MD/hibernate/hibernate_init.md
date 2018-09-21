## Hibernate框架的快速入门

#### 第一步：下载Hibernate5的运行环境

1. 下载相应的jar包等

    * http://sourceforge.net/projects/hibernate/files/hibernate-orm/5.0.7.Final/hibernate-release-5.0.7.Final.zip/download  

2. 解压后对目录结构有一定的了解

#### 第二步：创建表结构

建表语句如下

```
Create database hibernate_day01;
Use hibernate_day01;
CREATE TABLE `cst_customer` (
  `cust_id` bigint(32) NOT NULL AUTO_INCREMENT COMMENT '客户编号(主键)',
  `cust_name` varchar(32) NOT NULL COMMENT '客户名称(公司名称)',
  `cust_user_id` bigint(32) DEFAULT NULL COMMENT '负责人id',
  `cust_create_id` bigint(32) DEFAULT NULL COMMENT '创建人id',
  `cust_source` varchar(32) DEFAULT NULL COMMENT '客户信息来源',
  `cust_industry` varchar(32) DEFAULT NULL COMMENT '客户所属行业',
  `cust_level` varchar(32) DEFAULT NULL COMMENT '客户级别',
  `cust_linkman` varchar(64) DEFAULT NULL COMMENT '联系人',
  `cust_phone` varchar(64) DEFAULT NULL COMMENT '固定电话',
  `cust_mobile` varchar(16) DEFAULT NULL COMMENT '移动电话',
  PRIMARY KEY (`cust_id`)
) ENGINE=InnoDB AUTO_INCREMENT=94 DEFAULT CHARSET=utf8;
```

#### 第三步：搭建Hibernate的开发环境

创建WEB工程，引入Hibernate开发所需要的jar包

* MySQL的驱动jar包

* Hibernate开发需要的jar包（资料/hibernate-release-5.0.7.Final/lib/required/所有jar包）

* 日志jar包（资料/jar包/log4j/所有jar包）

#### 第四步：编写JavaBean实体类

Customer类的代码如下：

```
public class Customer {
    private Long cust_id;
    private String cust_name;
    private Long cust_user_id;
    private Long cust_create_id;
    private String cust_source;
    private String cust_industry;
    private String cust_level;
    private String cust_linkman;
    private String cust_phone;
    private String cust_mobile;
    // 省略get和set方法
}
```

#### 第五步：创建类与表结构的映射

1. 在JavaBean所在的包下创建映射的配置文件
    * 默认的命名规则为：实体类名.hbm.xml

    * 在xml配置文件中引入约束（引入的是hibernate3.0的dtd约束，不要引入4的约束）
        ```
        <!DOCTYPE hibernate-mapping PUBLIC 
            "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
            "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
        ```

2. 如果不能上网，编写配置文件是没有提示的，需要自己来配置

    * 先复制http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd --> window --> preferences --> 搜索xml --> 选择xml catalog --> 点击add --> 现在URI --> 粘贴复制的地址 --> 选择location，选择本地的DTD的路径

3. 编写映射的配置文件

    ```
    <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE hibernate-mapping PUBLIC 
            "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
            "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
    
    <hibernate-mapping>
    	<class name="com.itheima.domain.Customer" table="cst_customer">
    		<id name="cust_id" column="cust_id">
    			<generator class="native" />
    		</id>
    		<property name="cust_name" column="cust_name" />
    		<property name="cust_user_id" column="cust_user_id" />
    		<property name="cust_create_id" column="cust_create_id" />
    		<property name="cust_source" column="cust_source" />
    		<property name="cust_industry" column="cust_industry" />
    		<property name="cust_level" column="cust_level" />
    		<property name="cust_linkman" column="cust_linkman" />
    		<property name="cust_phone" column="cust_phone" />
    		<property name="cust_mobile" column="cust_mobile" />
    	</class>
    </hibernate-mapping>
    ```

#### 第六步：编写Hibernate核心的配置文件

1. 在src目录下，创建名称为hibernate.cfg.xml的配置文件

2. 在XML中引入DTD约束

   ```
   <!DOCTYPE hibernate-configuration PUBLIC
       "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
       "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
   ```

3. 打开：资料/hibernate-release-5.0.7.Final/project/etc/hibernate.properties，可以查看具体的配置信息  

        * 必须配置的4大参数                 
           * hibernate.connection.driver_class com.mysql.jdbc.Driver
           * hibernate.connection.url jdbc:mysql:///test
           * hibernate.connection.username gavin
           * hibernate.connection.password
        * 数据库的方言（必须配置的）
            * hibernate.dialect org.hibernate.dialect.MySQLDialect
        * 可选的配置
              * hibernate.show_sql  true
              * hibernate.format_sql  true
              * hibernate.hbm2ddl.auto  update
        * 引入映射配置文件（一定要注意，要引入映射文件，框架需要加载映射文件）
               * <mapping resource="com/itheima/domain/Customer.hbm.xml"/>

4. 具体的配置如下

    ```
    <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE hibernate-configuration PUBLIC
            "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
            "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
    
    <hibernate-configuration>
    	<session-factory>
    		<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
    		<property name="hibernate.connection.url">jdbc:mysql:///hibernate_day01</property>
    		<property name="hibernate.connection.username">root</property>
    		<property name="hibernate.connection.password">root</property>
    		<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
    
    		<mapping resource="com/itheima/domain/Customer.hbm.xml" />
    	</session-factory>
    </hibernate-configuration>
    ```

#### 第七步：编写Hibernate入门代码

具体的代码如下

```
/**
  *	测试保存客户
  */
  @Test
  public void testSave(){
   // 先加载配置文件
   Configuration config = new Configuration();
   // 默认加载src目录下的配置文件
   config.configure();
   // 创建SessionFactory对象
   SessionFactory factory = config.buildSessionFactory();
   // 创建session对象
   Session session = factory.openSession();
   // 开启事务
   Transaction tr = session.beginTransaction();
   // 编写保存代码
   Customer c = new Customer();
   // c.setCust_id(cust_id);   已经自动递增
   c.setCust_name("测试名称");
   c.setCust_mobile("110");
   // 保存客户
   session.save(c);
   // 提交事务
   tr.commit();
   // 释放资源
   session.close();
   factory.close();
  }
```

------

#### 回忆：快速入门

1. 下载Hibernate框架的开发包
2. 编写数据库和表结构
3. 创建WEB的项目，导入了开发的jar包
      - MySQL驱动包、Hibernate开发的必须要有的jar包、日志的jar包
4. 编写JavaBean，以后不使用基本数据类型，使用包装类
5. 编写映射的配置文件（核心），先导入开发的约束，里面正常配置标签
6. 编写hibernate的核心的配置文件，里面的内容是固定的
7. 编写代码，使用的类和方法