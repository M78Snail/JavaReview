## Hibernate查询功能优化

### Ⅰ.**延迟加载**

1. 延迟加载先获取到代理对象，当真正使用到该对象中的属性的时候，才会发送SQL语句，是Hibernate框架提升性能的方式
2. 类级别的延迟加载
    * Session对象的load方法默认就是延迟加载
    * Customer c = session.load(Customer.class, 1L);没有发送SQL语句，当使用该对象的属性时，才发送SQL语句

    * 使类级别的延迟加载失效
        * 在<class>标签上配置lazy=”false”
        * Hibernate.initialize(Object proxy);

3. 关联级别的延迟加载（查询某个客户，当查看该客户下的所有联系人是是否是延迟加载）
    * 默认是延迟加载

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        Customer c = session.get(Customer.class, 1L);
        System.out.println("=============");
        System.out.println(c.getLinkmans().size());
        tr.commit();
        ```

### Ⅱ. 查询语句的形式

#### 在set标签上配置策略：

1. 在<set>标签上使用fetch和lazy属性
    * fetch的取值              -- 控制SQL语句生成的格式
        * select                -- 默认值.发送查询语句
        * join                  -- 连接查询.发送的是一条迫切左外连接!!!配置了join.lazy就失效了
        * subselect             -- 子查询.发送一条子查询查询其关联对象.(需要使用list()方法进行测试)

    * lazy的取值               -- 查找关联对象的时候是否采用延迟!
        * true                  -- 默认.延迟
        * false                 -- 不延迟
        * extra                 -- 及其懒惰

2. set标签上的默认值是fetch="select"和lazy="true"

3. 总结：Hibernate框架都采用了默认值，开发中基本上使用的都是默认值。特殊的情况。

#### **在man-to-one标签上配置策略：**

1. 在<many-to-one>标签上使用fetch和lazy属性

  - fetch的取值      -- 控制SQL的格式.

       * select        -- 默认。发送基本select语句查询
            * join          -- 发送迫切左外连接查询

   * lazy的取值       -- 控制加载关联对象是否采用延迟.
       * false         -- 不采用延迟加载.
       * proxy         -- 默认值.代理.现在是否采用延迟.
           * 由另一端的<class>上的lazy确定.如果这端的class上的lazy=”true”.proxy的值就是true(延迟加载).
           * 如果class上lazy=”false”.proxy的值就是false(不采用延迟.)

2. 在<many-to-one>标签上的默认值是fetch="select"和proxy

