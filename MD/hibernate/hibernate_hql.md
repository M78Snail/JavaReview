## Hibernate框架的查询方式

1. HQL的介绍
    * HQL(Hibernate Query Language) 是面向对象的查询语言, 它和 SQL 查询语言有些相似
    * 在 Hibernate 提供的各种检索方式中, HQL 是使用最广的一种检索方式

2. HQL与SQL的关系
    * HQL 查询语句是面向对象的,Hibernate负责解析HQL查询语句, 然后根据对象-关系映射文件中的映射信息, 把 HQL 查询语句翻译成相应的 SQL 语句. 
    * HQL 查询语句中的主体是域模型中的类及类的属性
    * SQL 查询语句是与关系数据库绑定在一起的. SQL查询语句中的主体是数据库表及表的字段

### **HQL的查询演示**

1. HQL基本的查询格式
    * 支持方法链的编程，即直接调用list()方法
    * 简单的代码如下
        * session.createQuery("from Customer").list();

2. 使用别名的方式
    * 可以使用别名的方式
        * session.createQuery("from Customer c").list();
        * session.createQuery("select c from Customer c").list();

3. 排序查询
    * 排序查询和SQL语句中的排序的语法是一样的
        * 升序
            * session.createQuery("from Customer order by cust_id").list();

        * 降序
            * session.createQuery("from Customer order by cust_id desc").list();

4. 分页查询
    * Hibernate框架提供了分页的方法，咱们可以调用方法来完成分页
    * 两个方法如下
        * setFirstResult(a)     -- 从哪条记录开始，如果查询是从第一条开启，值是0
        * setMaxResults(b)      -- 每页查询的记录条数

    * 演示代码如下
        * List<LinkMan> list = session.createQuery("from LinkMan").setFirstResult(0).setMaxResults().list();

5. 带条件的查询
    * setParameter("?号的位置，默认从0开始","参数的值"); 不用考虑参数的具体类型

    * 按位置绑定参数的条件查询（指定下标值，默认从0开始）

    * 按名称绑定参数的条件查询（HQL语句中的 ? 号换成 :名称 的方式）

    * 例如代码如下

        ```
        Query query = session.createQuery("from Linkman where lkm_name like ? order by lkm_id desc");
        query.setFirstResult(0).setMaxResults(3);
        query.setParameter(0, "%熊%");
        List<Linkman> list = query.list();
        for (Linkman linkman : list) {
            System.out.println(linkman);
        }
        ```

6. 投影查询

    1. 投影查询就是想查询某一字段的值或者某几个字段的值
    2. 投影查询的案例
        * 如果查询多个字段，例如下面这种方式

          ```
          List<Object[]> list = session.createQuery("select c.cust_name,c.cust_level from Customer c").list();
          for (Object[] objects : list) {
              System.out.println(Arrays.toString(objects));
          }
          ```
        * 如果查询两个字段，也可以把这两个字段封装到对象中
            * 先在持久化类中提供对应字段的构造方法

            * 使用下面这种HQL语句的方式

                ```
                List<Customer> list = session.createQuery("select new Customer(c.cust_name,c.cust_level) from Customer c").list();
                for (Customer customer : list) {
                    System.out.println(customer);
                }
                ```

7. 聚合函数查询

    1. 获取总的记录数

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        List<Number> list = session.createQuery("select count(c) from Customer c").list();
        Long count = list.get(0).longValue();
        System.out.println(count);
        tr.commit();
        ```

    2. 获取某一列数据的和

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        List<Number> list = session.createQuery("select sum(c.cust_id) from Customer c").list();
        Long count = list.get(0).longValue();
        System.out.println(count);
        tr.commit();
        ```

### **HQL多表查询**

1. 多表的查询进来使用HQL语句进行查询，HQL语句和SQL语句的查询语法比较类似。
    * 内连接查询
        * 显示内连接
            * select * from customers c inner join orders o on c.cid = o.cno;
        * 隐式内连接
            * select * from customers c,orders o where c.cid = o.cno;

    * 外连接查询
        * 左外连接
            * select * from customers c left join orders o on c.cid = o.cno;
        * 右外连接
            * select * from customers c right join orders o on c.cid = o.cno;

2. HQL的多表查询
    * 迫切和非迫切：
        * 非迫切返回结果是Object[]
        * 迫切连接返回的结果是对象，把客户的信息封装到客户的对象中，把订单的信息封装到客户的Set集合中。

3. 内连接查询
    * 内连接使用 inner join ，默认返回的是Object数组

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        List<Object[]> list = session.createQuery("from Customer c inner join c.linkmans").list();
        for (Object[] objects : list) {
            System.out.println(Arrays.toString(objects));
        }
        tr.commit();
        ```

    * 迫切内连接:inner join fetch ，返回的是实体对象

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        List<Customer> list = session.createQuery("from Customer c inner join fetch c.linkmans").list();
        Set<Customer> set = new HashSet<Customer>(list);
        for (Customer customer : set) {
            System.out.println(customer);
        }
        tr.commit();
        ```

4. 左外连接查询
    * 左外连接: 封装成List<Object[]>

    * 迫切左外连接

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        List<Customer> list = session.createQuery("from Customer c left join fetch c.linkmans").list();
        Set<Customer> set = new HashSet<Customer>(list);
        for (Customer customer : set) {
            System.out.println(customer);
        }
        tr.commit();
        ```
