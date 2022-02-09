## 技术分析之事务的回顾

事务：指的是逻辑上一组操作，组成这个事务的各个执行单元，要么一起成功,要么一起失败！

> 事务的特性

* 原子性

* 一致性

* 隔离性

* 持久性

> 如果不考虑隔离性,引发安全性问题

* 读问题:

  * 脏读:
  * 不可重复读:
  * 虚读:

* 写问题:

  * 丢失更新:

* 如何解决安全性问题

> 读问题解决，设置数据库隔离级别
>
> 写问题解决可以使用 悲观锁和乐观锁的方式解决

---

## 事务管理相关的类和API

1. PlatformTransactionManager接口        -- 平台事务管理器.\(真正管理事务的类\)。该接口有具体的实现类，根据不同的持久层框架，需要选择不同的实现类！

2. TransactionDefinition接口            -- 事务定义信息.\(事务的隔离级别,传播行为,超时,只读\)

3. TransactionStatus接口                -- 事务的状态

4. 总结：上述对象之间的关系：平台事务管理器真正管理事务对象.根据事务定义的信息TransactionDefinition 进行事务管理，在管理事务中产生一些状态.将状态记录到TransactionStatus中

5. PlatformTransactionManager接口中实现类和常用的方法

   1. 接口的实现类
      1. **如果使用的Spring的JDBC模板或者MyBatis框架，需要选择DataSourceTransactionManager实现类**
      2. **如果使用的是Hibernate的框架，需要选择HibernateTransactionManager实现类**
   2. 该接口的常用方法

      * void commit\(TransactionStatus status\)

      * TransactionStatus getTransaction\(TransactionDefinition definition\)

      * void rollback\(TransactionStatus status\)

6. TransactionDefinition

   1. 事务隔离级别的常量

      * static int ISOLATION\_DEFAULT                     -- 采用数据库的默认隔离级别

      * static int ISOLATION\_READ\_UNCOMMITTED

      * static int ISOLATION\_READ\_COMMITTED

      * static int ISOLATION\_REPEATABLE\_READ

      * static int ISOLATION\_SERIALIZABLE

   2. **事务的传播行为常量（不用设置，使用默认值）**

      * 先解释什么是事务的传播行为：解决的是业务层之间的方法调用！！

      * PROPAGATION\_REQUIRED（默认值）    -- A中有事务,使用A中的事务.如果没有，B就会开启一个新的事务,将A包含进来.\(保证A,B在同一个事务中\)，默认值！！

      * PROPAGATION\_SUPPORTS            -- A中有事务,使用A中的事务.如果A中没有事务.那么B也不使用事务.

      * PROPAGATION\_MANDATORY            -- A中有事务,使用A中的事务.如果A没有事务.抛出异常.

      * PROPAGATION\_REQUIRES\_NEW（记）-- A中有事务,将A中的事务挂起.B创建一个新的事务.\(保证A,B没有在一个事务中\)

      * PROPAGATION\_NOT\_SUPPORTED        -- A中有事务,将A中的事务挂起.

      * PROPAGATION\_NEVER             -- A中有事务,抛出异常.

      * PROPAGATION\_NESTED（记）        -- 嵌套事务.当A执行之后,就会在这个位置设置一个保存点.如果B没有问题.执行通过.如果B出现异常,运行客户根据需求回滚\(选择回滚到保存点或者是最初始状态\)

---

## 编程式的事务管理（了解）

1. 说明：Spring为了简化事务管理的代码:提供了模板类 TransactionTemplate，所以手动编程的方式来管理事务，只需要使用该模板类即可！！
2. 手动编程方式的具体步骤如下：

   1. 步骤一:配置一个事务管理器，Spring使用PlatformTransactionManager接口来管理事务，所以咱们需要使用到他的实现类！！

      ```java
      <!-- 配置事务管理器 -->
      <bean id="transactionManager"
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
      </bean>
      ```

   2. 步骤二:配置事务管理的模板

      ```java
      <!-- 配置事务管理的模板 -->
      <bean id="transactionTemplate"
          class="org.springframework.transaction.support.TransactionTemplate">
          <property name="transactionManager" ref="transactionManager" />
      </bean>
      ```

   3. 步骤三:在需要进行事务管理的类中,注入事务管理的模板.

      ```java
      <bean id="accountService" class="com.test.demo1.AccountServiceImpl">
          <property name="accountDao" ref="accountDao" />
          <property name="transactionTemplate" ref="transactionTemplate" />
      </bean>
      ```

   4. 步骤四:在业务层使用模板管理事务:

      ```java
      // 注入事务的模板类
      private TransactionTemplate transactionTemplate;

      public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
              this.transactionTemplate = transactionTemplate;
      }

      /**
       * 转账的方法
       */
      public void pay(final String out, final String in, final double money) {
          transactionTemplate.execute(new TransactionCallbackWithoutResult() {
              // 事务的执行，如果没有问题，提交。如果出现了异常，回滚
              protected void doInTransactionWithoutResult(TransactionStatus arg0) {
                  // 先扣钱
                  accountDao.outMoney(out, money);
                  // 模拟异常
                  int a = 10 / 0;
                  // 加钱
                  accountDao.inMoney(in, money);
              }
          });
      }
      ```

---

## 声明式的事务管理（配置文件）【重点掌握】

1. 步骤一:配置一个事务管理器，Spring使用PlatformTransactionManager接口来管理事务，所以咱们需要使用到他的实现类！！
   ```java
   <!-- 配置事务管理器 -->
   <bean id="transactionManager"
     class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
     <property name="dataSource" ref="dataSource" />
   </bean>
   ```
2. 步骤二:配置事务增强
   ```java
   <!-- 配置事务增强 -->
   <tx:advice id="txAdvice" transaction-manager="transactionManager">
       <tx:attributes>
           <!-- name ：绑定事务的方法名，可以使用通配符，可以配置多个。 
                propagation ：传播行为 
                isolation ：隔离级别 
                read-only ：是否只读 
                timeout ：超时信息 
                rollback-for：发生哪些异常回滚. 
                no-rollback-for：发生哪些异常不回滚. -->
           <!-- 哪些方法加事务 -->
           <tx:method name="pay" propagation="REQUIRED" />
       </tx:attributes>
   </tx:advice>
   ```
3. 步骤三:配置AOP的切面

   ```java
   <!-- 配置AOP切面产生代理 -->
   <aop:config>
       <aop:advisor advice-ref="myAdvice"
           pointcut="execution(* com.test.demo2.AccountServiceImpl.pay(..))" />
   </aop:config>
   ```

   * 注意：如果是自己编写的切面，使用&lt;aop:aspect&gt;标签，如果是系统制作的，使用&lt;aop:advisor&gt;标签。

---

## 声明式的事务管理（注解方式）【重点掌握】【最简单】

1. 步骤一:配置一个事务管理器，Spring使用PlatformTransactionManager接口来管理事务，所以咱们需要使用到他的实现类！！
   ```java
   <!-- 配置事务管理器 -->
   <bean id="transactionManager"
     class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
     <property name="dataSource" ref="dataSource" />
   </bean>
   ```
2. 步骤二:开启注解事务
   ```java
   <!-- 开启注解事务 -->
   <tx:annotation-driven transaction-manager="transactionManager" />
   ```
3. 步骤三:在业务层上添加一个注解:@Transactional

   ```java
   类上面代表类里所有方法
   @Transactional
   public class AccountServiceImpl implements AccountService {

   }
   ```

   ```java
   方法上面代表单个方法
   @Transactional
   public void pay(final String out, final String in, final double money) {
       // 先扣钱
       accountDao.outMoney(out, money);
       // 模拟异常
       int a = 10/0;
       // 加钱
       accountDao.inMoney(in, money);
   }
   ```



