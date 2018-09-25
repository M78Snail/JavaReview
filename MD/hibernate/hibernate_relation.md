## Hibernate关联关系映射

### **Hibernate的关联关系映射之一对多映射**

1. JavaWEB中一对多的设计及其建表原则

2. 先导入SQL的建表语句
    * 创建今天的数据库：create database hibernate_day03;
    * 在资料中找到客户和联系人的SQL脚本

3. 编写客户和联系人的JavaBean程序（注意一对多的编写规则）
    * 客户的JavaBean如下

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
        
            private Set<Linkman> linkmans = new HashSet<Linkman>();
        
        }
        ```

    * 联系人的JavaBean如下

        ```
        public class Linkman {
            private Long lkm_id;
            private String lkm_name;
            private String lkm_gender;
            private String lkm_phone;
            private String lkm_mobile;
            private String lkm_email;
            private String lkm_qq;
            private String lkm_position;
            private String lkm_memo;
        
            private Customer customer;
        
        }
        ```

4. 编写客户和联系人的映射配置文件（注意一对多的配置编写）
    * 客户的映射配置文件如下

        ```
        <class name="com.itheima.domain.Customer" table="cst_customer">
            <id name="cust_id" column="cust_id">
                <generator class="native"/>
            </id>
            <property name="cust_name" column="cust_name"/>
            <property name="cust_user_id" column="cust_user_id"/>
            <property name="cust_create_id" column="cust_create_id"/>
            <property name="cust_source" column="cust_source"/>
            <property name="cust_industry" column="cust_industry"/>
            <property name="cust_level" column="cust_level"/>
            <property name="cust_linkman" column="cust_linkman"/>
            <property name="cust_phone" column="cust_phone"/>
            <property name="cust_mobile" column="cust_mobile"/>
        
            <set name="linkmans">
                <key column="lkm_cust_id"/>
                <one-to-many class="com.itheima.domain.Linkman"/>
            </set>
        </class>
        ```

    * 联系人的映射配置文件如下

         ```
         <class name="com.itheima.domain.Linkman" table="cst_linkman">
             <id name="lkm_id" column="lkm_id">
                 <generator class="native"/>
             </id>
             <property name="lkm_name" column="lkm_name"/>
             <property name="lkm_gender" column="lkm_gender"/>
             <property name="lkm_phone" column="lkm_phone"/>
             <property name="lkm_mobile" column="lkm_mobile"/>
             <property name="lkm_email" column="lkm_email"/>
             <property name="lkm_qq" column="lkm_qq"/>
             <property name="lkm_position" column="lkm_position"/>
             <property name="lkm_memo" column="lkm_memo"/>
                 
             <many-to-one name="customer" class="com.itheima.domain.Customer" column="lkm_cust_id"/>
         </class>
         ```

------

### Hibernate的关联关系映射之多对多映射

1. 编写用户和角色的JavaBean

    - 用户的JavaBean代码如下

      ```
      public class User {
          private Long user_id;
          private String user_code;
          private String user_name;
          private String user_password;
          private String user_state;
      
          private Set<Role> roles = new HashSet<Role>();
      }
      ```

    - 角色的JavaBean代码如下

      ```
      public class Role {
          private Long role_id;
          private String role_name;
          private String role_memo;
      
          private Set<User> users = new HashSet<User>();
      }
      ```

2. 用户和角色的映射配置文件如下
    * 用户的映射配置文件如下

        ```
         <class name="com.itheima.domain.User" table="sys_user">
             <id name="user_id" column="user_id">
                 <generator class="native"/>
             </id>
             <property name="user_code" column="user_code"/>
             <property name="user_name" column="user_name"/>
             <property name="user_password" column="user_password"/>
             <property name="user_state" column="user_state"/>
        
             <set name="roles" table="sys_user_role">
                 <key column="user_id"/>
                 <many-to-many class="com.itheima.domain.Role" column="role_id"/>
             </set>
        </class>
        ```

    * 角色的映射配置文件如下

        ```
        <class name="com.itheima.domain.Role" table="sys_role">
            <id name="role_id" column="role_id">
                <generator class="native"/>
            </id>
            
            <property name="role_name" column="role_name"/>
            <property name="role_memo" column="role_memo"/>
        
            <set name="users" table="sys_user_role">
                <key column="role_id"/>
                <many-to-many class="com.itheima.domain.User" column="user_id"/>
            </set>
        </class>
        ```

3. 多对多进行双向关联的时候:必须有一方去放弃外键维护权