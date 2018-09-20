## Jedis的Java连接
需要把jedis依赖的jar包添加到工程中。Maven工程中需要把jedis的坐标添加到依赖。推荐添加到服务层。

## 1.单机版

### 1.1 连接单机版

第一步：创建一个Jedis对象。需要指定服务端的ip及端口。

第二步：使用Jedis对象操作数据库，每个redis命令对应一个方法。

第三步：打印结果。

第四步：关闭Jedis

```
@Test
public void testJedis() throws Exception {
	// 第一步：创建一个Jedis对象。需要指定服务端的ip及端口。
	Jedis jedis = new Jedis("192.168.25.153", 6379);
	// 第二步：使用Jedis对象操作数据库，每个redis命令对应一个方法。
	String result = jedis.get("hello");
	// 第三步：打印结果。
	System.out.println(result);
	// 第四步：关闭Jedis
	jedis.close();
}
```

### 1.2 连接单机版使用连接池

第一步：创建一个JedisPool对象。需要指定服务端的ip及端口。

第二步：从JedisPool中获得Jedis对象。

第三步：使用Jedis操作redis服务器。

第四步：操作完毕后关闭jedis对象，连接池回收资源。

第五步：关闭JedisPool对象。

```
@Test
public void testJedisPool() throws Exception {
	// 第一步：创建一个JedisPool对象。需要指定服务端的ip及端口。
	JedisPool jedisPool = new JedisPool("192.168.25.153", 6379);
	// 第二步：从JedisPool中获得Jedis对象。
	Jedis jedis = jedisPool.getResource();
	// 第三步：使用Jedis操作redis服务器。
	jedis.set("jedis", "test");
	String result = jedis.get("jedis");
	System.out.println(result);
	// 第四步：操作完毕后关闭jedis对象，连接池回收资源。
	jedis.close();
	// 第五步：关闭JedisPool对象。
	jedisPool.close();
}
```

## 2.集群版

第一步：使用JedisCluster对象。需要一个Set<HostAndPort>参数。Redis节点的列表。
第二步：直接使用JedisCluster对象操作redis。在系统中单例存在。
第三步：打印结果
第四步：系统关闭前，关闭JedisCluster对象。

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

## 3.接口封装

常用的操作redis的方法提取出一个接口，分别对应单机版和集群版创建两个实现类。

#### 3.1 接口定义

```
public interface JedisClient {
	String set(String key, String value);
	String get(String key);
	Boolean exists(String key);
	Long expire(String key, int seconds);
	Long ttl(String key);
	Long incr(String key);
	Long hset(String key, String field, String value);
	String hget(String key, String field);
	Long hdel(String key, String... field);
}
```

## 4. 实现类

### 4.1单机版实现类

```
public class JedisClientPool implements JedisClient {
	
	@Autowired
	private JedisPool jedisPool;

	@Override
	public String set(String key, String value) {
		Jedis jedis = jedisPool.getResource();
		String result = jedis.set(key, value);
		jedis.close();
		return result;
	}

	@Override
	public String get(String key) {
		Jedis jedis = jedisPool.getResource();
		String result = jedis.get(key);
		jedis.close();
		return result;
	}

	@Override
	public Boolean exists(String key) {
		Jedis jedis = jedisPool.getResource();
		Boolean result = jedis.exists(key);
		jedis.close();
		return result;
	}

	@Override
	public Long expire(String key, int seconds) {
		Jedis jedis = jedisPool.getResource();
		Long result = jedis.expire(key, seconds);
		jedis.close();
		return result;
	}

	@Override
	public Long ttl(String key) {
		Jedis jedis = jedisPool.getResource();
		Long result = jedis.ttl(key);
		jedis.close();
		return result;
	}

	@Override
	public Long incr(String key) {
		Jedis jedis = jedisPool.getResource();
		Long result = jedis.incr(key);
		jedis.close();
		return result;
	}

	@Override
	public Long hset(String key, String field, String value) {
		Jedis jedis = jedisPool.getResource();
		Long result = jedis.hset(key, field, value);
		jedis.close();
		return result;
	}

	@Override
	public String hget(String key, String field) {
		Jedis jedis = jedisPool.getResource();
		String result = jedis.hget(key, field);
		jedis.close();
		return result;
	}

	@Override
	public Long hdel(String key, String... field) {
		Jedis jedis = jedisPool.getResource();
		Long result = jedis.hdel(key, field);
		jedis.close();
		return result;
	}

}
```

#### 配置文件：applicationContext-redis.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans4.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context4.2.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx4.2.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util4.2.xsd">

	<!-- 配置单机版的连接 -->
	<bean id="jedisPool" class="redis.clients.jedis.JedisPool">
		<constructor-arg name="host" value="192.168.25.153"></constructor-arg>
		<constructor-arg name="port" value="6379"></constructor-arg>
	</bean>
	<bean id="jedisClientPool" class="com.taotao.jedis.JedisClientPool"/>
	
</beans>

```

### 4.2 集群版实现类

```
package com.taotao.jedis;

import org.springframework.beans.factory.annotation.Autowired;

import redis.clients.jedis.JedisCluster;

public class JedisClientCluster implements JedisClient {
	
	@Autowired
	private JedisCluster jedisCluster;

	@Override
	public String set(String key, String value) {
		return jedisCluster.set(key, value);
	}

	@Override
	public String get(String key) {
		return jedisCluster.get(key);
	}

	@Override
	public Boolean exists(String key) {
		return jedisCluster.exists(key);
	}

	@Override
	public Long expire(String key, int seconds) {
		return jedisCluster.expire(key, seconds);
	}

	@Override
	public Long ttl(String key) {
		return jedisCluster.ttl(key);
	}

	@Override
	public Long incr(String key) {
		return jedisCluster.incr(key);
	}

	@Override
	public Long hset(String key, String field, String value) {
		return jedisCluster.hset(key, field, value);
	}

	@Override
	public String hget(String key, String field) {
		return jedisCluster.hget(key, field);
	}

	@Override
	public Long hdel(String key, String... field) {
		return jedisCluster.hdel(key, field);
	}

}

```

#### 配置文件

```
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
	<
	<bean id="jedisClientCluster" class="com.taotao.jedis.JedisClientCluster" />
</beans>

```



**注意：单机版和集群版不能共存，使用单机版时注释集群版的配置。使用集群版，把单机版注释。**