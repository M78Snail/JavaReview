启动

/usr/local/apache-activemq-5.12.0/bin 下

```
./activemq start
```

修改hosts

```
cat /etc/sysconfig/network  获取到HOSTNAME
vim /etc/hosts    添加HOSTNAME名称
```

重启ActiveMQ服务

```
./activemq restart
```

---

队列生产者

```java
//1.创建一个连接工厂对象ConnectionFactory对象。需要指定mq服务的ip及端口
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
//2.使用ConnectionFactory创建一个连接Connection对象
Connection connection = connectionFactory.createConnection();
//3.开启连接。调用Connection对象的start方法
connection.start();
//4.使用Connection对象创建一个Session对象
//第一个参数是是否开启事务，一般不使用事务。保证数据的最终一致，可以使用消息队列实现。
//如果第一个参数为true，第二个参数自动忽略。如果不开启事务false，第二个参数为消息的应答模式。一般自动应答就可以。        
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
//5.使用Session对象创建一个Destination对象，两种形式queue、topic。现在应该使用queue
//参数就是消息队列的名称
Queue queue = session.createQueue("test-queue");
//6.使用Session对象创建一个Producer对象
MessageProducer producer = session.createProducer(queue);
//7.创建一个TextMessage对象
/*TextMessage textMessage = new ActiveMQTextMessage();
textMessage.setText("hello activemq");*/
TextMessage textMessage = session.createTextMessage("hello activemq1111");
//8.发送消息
producer.send(textMessage);
//9.关闭资源
producer.close();
session.close();
connection.close();
```

队列消费者

```java
//创建一个连接工厂对象
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
//使用连接工厂对象创建一个连接
Connection connection = connectionFactory.createConnection();
//开启连接
connection.start();
//使用连接对象创建一个Session对象
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
//使用Session创建一个Destination，Destination应该和消息的发送端一致。
Queue queue = session.createQueue("test-queue");
//使用Session创建一个Consumer对象
MessageConsumer consumer = session.createConsumer(queue);
//向Consumer对象中设置一个MessageListener对象，用来接收消息
consumer.setMessageListener(new MessageListener() {

    @Override
    public void onMessage(Message message) {
         //取消息的内容
         if (message instanceof TextMessage) {
             TextMessage textMessage = (TextMessage) message;
             try {
                 String text = textMessage.getText();
                 //打印消息内容
                 System.out.println(text);
             } catch (JMSException e) {
                 e.printStackTrace();
             }
          }

     }
});

//系统等待接收消息
System.in.read();
//关闭资源
consumer.close();
session.close();
connection.close();
```

---

主题生产者

```java
//1.创建一个连接工厂对象ConnectionFactory对象。需要指定mq服务的ip及端口
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
//2.使用ConnectionFactory创建一个连接Connection对象
Connection connection = connectionFactory.createConnection();
//3.开启连接。调用Connection对象的start方法
connection.start();
//4.使用Connection对象创建一个Session对象
//第一个参数是是否开启事务，一般不使用事务。保证数据的最终一致，可以使用消息队列实现。
//如果第一个参数为true，第二个参数自动忽略。如果不开启事务false，第二个参数为消息的应答模式。一般自动应答就可以。        
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
//创建Destination，应该使用topic
Topic topic = session.createTopic("test-topic");
//创建一个Producer对象
MessageProducer producer = session.createProducer(topic);
//7.创建一个TextMessage对象
/*TextMessage textMessage = new ActiveMQTextMessage();
textMessage.setText("hello activemq");*/
TextMessage textMessage = session.createTextMessage("hello activemq1111");
//8.发送消息
producer.send(textMessage);
//9.关闭资源
producer.close();
session.close();
connection.close();
```

主题消费者

```java
//创建一个连接工厂对象
ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.130:61616");
//使用连接工厂对象创建一个连接
Connection connection = connectionFactory.createConnection();
//开启连接
connection.start();
//使用连接对象创建一个Session对象
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
//使用Session创建一个Destination，Destination应该和消息的发送端一致。
Topic topic = session.createTopic("test-topic");
//使用Session创建一个Consumer对象
MessageConsumer consumer = session.createConsumer(topic);
//向Consumer对象中设置一个MessageListener对象，用来接收消息
consumer.setMessageListener(new MessageListener() {

    @Override
    public void onMessage(Message message) {
         //取消息的内容
         if (message instanceof TextMessage) {
             TextMessage textMessage = (TextMessage) message;
             try {
                 String text = textMessage.getText();
                 //打印消息内容
                 System.out.println(text);
             } catch (JMSException e) {
                 e.printStackTrace();
             }
          }

     }
});
//系统等待接收消息
System.in.read();
//关闭资源
consumer.close();
session.close();
connection.close();
```

* Topic不会在服务端缓存，没有消费者接收就再也接收不到了
* Queue会在服务端缓存

---

Spring管理ActiveMQ

生产者

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
    http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">

    <!-- JMS服务厂商提供的ConnectionFactory -->
    <bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <constructor-arg name="brokerURL" value="tcp://192.168.25.130:61616"/>
    </bean>
    <!-- spring对象ConnectionFactory的封装 -->
    <bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
        <property name="targetConnectionFactory" ref="targetConnectionFactory"></property>
    </bean>
    <!-- 配置JMSTemplate -->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="connectionFactory"/>
    </bean>
    <!-- 配置消息的Destination对象 -->
    <bean id="test-queue" class="org.apache.activemq.command.ActiveMQQueue">
        <constructor-arg name="name" value="test-queue"></constructor-arg>
    </bean>
    <bean id="itemAddtopic" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg name="name" value="item-add-topic"></constructor-arg>
    </bean>
</beans>
```

```java
//初始化spring容器
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:spring/ApplicationContext-activemq.xml");
//从容器中获得JmsTemplate对象
JmsTemplate jmsTemplate = applicationContext.getBean(JmsTemplate.class);
//从容器中获得Destination对象
Destination destination = (Destination) applicationContext.getBean("test-queue");
//发送消息
jmsTemplate.send(destination, new MessageCreator() {

    @Override
    public Message createMessage(Session session) throws JMSException {
     TextMessage message = session.createTextMessage("spring activemq send queue message");
     return message;
    }
});
```

消费者

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
    http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">

    <!-- JMS服务厂商提供的ConnectionFactory -->
    <bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <constructor-arg name="brokerURL" value="tcp://192.168.25.130:61616"/>
    </bean>
    <!-- spring对象ConnectionFactory的封装 -->
    <bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
        <property name="targetConnectionFactory" ref="targetConnectionFactory"></property>
    </bean>
    <!-- 配置消息的Destination对象 -->
    <bean id="test-queue" class="org.apache.activemq.command.ActiveMQQueue">
        <constructor-arg name="name" value="test-queue"></constructor-arg>
    </bean>
    <bean id="itemAddTopic" class="org.apache.activemq.command.ActiveMQTopic">
        <constructor-arg name="name" value="item-add-topic"></constructor-arg>
    </bean>
    <!-- 配置消息的接收者 -->
    <bean id="myMessageListener" class="com.taotao.search.listener.MyMessageListener"/>
    <bean class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="destination" ref="test-queue" />
        <property name="messageListener" ref="myMessageListener" />
    </bean>
    <bean id="itemAddMessageListener" class="com.taotao.search.listener.ItemAddMessageListener"/>
    <bean class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="destination" ref="itemAddTopic" />
        <property name="messageListener" ref="itemAddMessageListener" />
    </bean>
</beans>
```

```java
public class MyMessageListener implements MessageListener {

    @Override
    public void onMessage(Message message) {
        try {
            //接收到消息
            TextMessage textMessage = (TextMessage) message;
            String text = textMessage.getText();
            System.out.println(text);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```



