## Hibernate框架的查询方式

0. QBC：Query By Criteria  按条件进行查询

1. 简单查询，使用的是Criteria接口

    ```
    List<Customer> list = session.createCriteria(Customer.class).list();
    for (Customer customer : list) {
        System.out.println(customer);
    }
    ```

2. 排序查询
    * 需要使用addOrder()的方法来设置参数，参数使用org.hibernate.criterion.Order对象

    * 具体代码如下：

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        Criteria criteria = session.createCriteria(Linkman.class);
        // 设置排序
        criteria.addOrder(Order.desc("lkm_id"));
        List<Linkman> list = criteria.list();
        for (Linkman linkman : list) {
            System.out.println(linkman);
        }
        tr.commit();
        ```

3. 分页查询
    * QBC的分页查询也是使用两个方法
        * setFirstResult();
        * setMaxResults();

    * 代码如下;

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        Criteria criteria = session.createCriteria(Linkman.class);
        // 设置排序
        criteria.addOrder(Order.desc("lkm_id"));
        criteria.setFirstResult(0);
        criteria.setMaxResults(3);
        List<Linkman> list = criteria.list();
        for (Linkman linkman : list) {
            System.out.println(linkman);
        }
        tr.commit();
        ```

4. 条件查询（Criterion是查询条件的接口，Restrictions类是Hibernate框架提供的工具类，使用该工具类来设置查询条件）
    * 条件查询使用Criteria接口的add方法，用来传入条件。

    * 使用Restrictions的添加条件的方法，来添加条件，例如：
        * Restrictions.eq           -- 相等
        * Restrictions.gt           -- 大于号
        * Restrictions.ge           -- 大于等于
        * Restrictions.lt           -- 小于
        * Restrictions.le           -- 小于等于
        * Restrictions.between      -- 在之间
        * Restrictions.like         -- 模糊查询
        * Restrictions.in           -- 范围
        * Restrictions.and          -- 并且
        * Restrictions.or           -- 或者

    * 测试代码如下

        ```
        Session session = HibernateUtils.getCurrentSession();
        Transaction tr = session.beginTransaction();
        Criteria criteria = session.createCriteria(Linkman.class);
        // 设置排序
        criteria.addOrder(Order.desc("lkm_id"));
        // 设置查询条件
        criteria.add(Restrictions.or(Restrictions.eq("lkm_gender", "男"), Restrictions.gt("lkm_id", 3L)));
        List<Linkman> list = criteria.list();
        for (Linkman linkman : list) {
             System.out.println(linkman);
        }
        tr.commit();
        ```

5. 聚合函数查询（Projection的聚合函数的接口，而Projections是Hibernate提供的工具类，使用该工具类设置聚合函数查询）
          * 使用QBC的聚合函数查询，需要使用criteria.setProjection()方法

          * 具体的代码如下

               ```
               Session session = HibernateUtils.getCurrentSession();
               Transaction tr = session.beginTransaction();
               Criteria criteria = session.createCriteria(Linkman.class);
               criteria.setProjection(Projections.rowCount());
               List<Number> list = criteria.list();
               Long count = list.get(0).longValue();
               System.out.println(count);
               tr.commit();
               ```
