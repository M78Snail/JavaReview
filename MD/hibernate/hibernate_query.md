## Hibernate框架的查询方式

#### **Query查询接口**

具体的查询代码如下

1. 查询所有记录

   ```
   Query query = session.createQuery("from Customer");
   List<Customer> list = query.list();
   System.out.println(list);
   ```

2. 条件查询:

   ```
   Query query = session.createQuery("from Customer where name = ?");
   query.setString(0, "李健");
   List<Customer> list = query.list();
   System.out.println(list);
   ```

3. 条件查询:

   ```
   Query query = session.createQuery("from Customer where name = :aaa and age = :bbb");
   query.setString("aaa", "李健");
   query.setInteger("bbb", 38);
   List<Customer> list = query.list();
   System.out.println(list);
   ```

#### **Criteria查询接口（做条件查询非常合适）**

具体的查询代码如下

1. 查询所有记录

   ```
   Criteria criteria = session.createCriteria(Customer.class);
   List<Customer> list = criteria.list();
   System.out.println(list);
   ```

2. 条件查询:

   ```
   Criteria criteria = session.createCriteria(Customer.class);
   criteria.add(Restrictions.eq("name", "李健"));
   List<Customer> list = criteria.list();
   System.out.println(list);
   ```

3. 条件查询:

   ```
   Criteria criteria = session.createCriteria(Customer.class);
   criteria.add(Restrictions.eq("name", "李健"));
   criteria.add(Restrictions.eq("age", 38));
   List<Customer> list = criteria.list();
   System.out.println(list);
   ```