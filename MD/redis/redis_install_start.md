## 单机启动

```
./redis-server redis.conf
```

## 集群启动

**/usr/local/redis-cluster**

```java
./redis-trib.rb create --replicas 1 
 192.168.25.128:7001 
 192.168.25.128:7002 
 192.168.25.128:7003
 192.168.25.128:7004 
 192.168.25.128:7005 
 192.168.25.128:7006
```

## 集群连接：

```
redis01/redis-cli -p 7006 -c
```

## JedisCluster Java连接

```java
// 创建一个JedisCluster对象，构造参数Set类型，集合中每个元素是HostAndPort类型
Set<HostAndPort> nodes = new HashSet<>();
// 向集合中添加节点
nodes.add(new HostAndPort("192.168.25.128", 7001));
nodes.add(new HostAndPort("192.168.25.128", 7002));
nodes.add(new HostAndPort("192.168.25.128", 7003));
nodes.add(new HostAndPort("192.168.25.128", 7004));
nodes.add(new HostAndPort("192.168.25.128", 7005));
nodes.add(new HostAndPort("192.168.25.128", 7006));
JedisCluster jdCluster = new JedisCluster(nodes);
// 直接使用JedisCluster操作redis，自带连接池。
jdCluster.set("cluster-test", "hello jedis cluster");

System.out.println(jdCluster.get("cluster"));
// 系统关闭前关闭JedisCluster
jdCluster.close();
```

## 单机版和集群版配置

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
    http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">
    <context:annotation-config />
    <!-- redis单机版 -->
    <bean id="jedisPool" class="redis.clients.jedis.JedisPool">
        <constructor-arg name="host" value="192.168.25.128" />
        <constructor-arg name="port" value="6379" />
    </bean>
    <bean id="jedisClientPool" class="com.taotao.jedis.JedisClientPool" />


    <!-- redis集群 -->
    <bean id="jedisCluster" class="redis.clients.jedis.JedisCluster">
        <constructor-arg>
            <set>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg name="host" value="192.168.25.128" />
                    <constructor-arg name="port" value="7001" />
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg name="host" value="192.168.25.128" />
                    <constructor-arg name="port" value="7002" />
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg name="host" value="192.168.25.128" />
                    <constructor-arg name="port" value="7003" />
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg name="host" value="192.168.25.128" />
                    <constructor-arg name="port" value="7004" />
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg name="host" value="192.168.25.128" />
                    <constructor-arg name="port" value="7005" />
                </bean>
                <bean class="redis.clients.jedis.HostAndPort">
                    <constructor-arg name="host" value="192.168.25.128" />
                    <constructor-arg name="port" value="7006" />
                </bean>
            </set>
        </constructor-arg>
    </bean>
    <bean id="jedisClientCluster" class="com.taotao.jedis.JedisClientCluster" />
</beans>
```



