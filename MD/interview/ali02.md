## 蚂蚁中间件团队面试题：Netty+Redis+Kafka+MongoDB+分布式

## **一面**

### 1. 自我介绍

### 2. JVM垃圾回收算法和垃圾回收器有哪些，最新的JDK采用什么算法。

- [GC的四种收集方法](https://github.com/M78Snail/ReadReview/blob/master/MD/jvm/gcde-san-zhong-shou-ji-fang-fa-ff1a-biao-ji-qing-chu-3001-biao-ji-zheng-li-3001-fu-zhi-suan-fa-de-yuan-li-yu-te-dian-ff0c-fen-bie-yong-zai-shi-yao-di-fang.md)
- [GC收集器有哪些？它们的特点是？](https://github.com/M78Snail/ReadReview/blob/master/MD/jvm/gcshou-ji-qi-you-na-xie-ff1f-ta-men-de-te-dian-shi-ff1f.md)

### 3. 新生代和老年代的回收机制。

- [GC的四种收集方法](https://github.com/M78Snail/ReadReview/blob/master/MD/jvm/gcde-san-zhong-shou-ji-fang-fa-ff1a-biao-ji-qing-chu-3001-biao-ji-zheng-li-3001-fu-zhi-suan-fa-de-yuan-li-yu-te-dian-ff0c-fen-bie-yong-zai-shi-yao-di-fang.md)

### 4. 讲一下ArrayList和linkedlist的区别，ArrayList与HashMap的扩容方式。

### 5. Concurrenthashmap1.8后的改动。

- [ConcurrentHashMap1.7和1.8的底层不同实现](https://my.oschina.net/zupengliu/blog/1930025)


### 6. Java中的多线程，以及线程池的增长策略和拒绝策略了解么。

- [Java线程池分析](https://github.com/M78Snail/ReadReview/blob/master/MD/concurrent/executor_queue.md)

### 7. Tomcat的类加载器了解么

- [Tomcat 类加载器之为何违背双亲委派模型](https://github.com/M78Snail/ReadReview/blob/master/MD/jvm/tomcat_class_load.md)


### 8. Spring的ioc和aop，Springmvc的基本架构，请求流程。

### 9. HTTP协议与Tcp有什么区别，http1.0和2.0的区别。

- [Linux查看网络 内存 日志命令](https://github.com/M78Snail/ReadReview/blob/master/MD/system/linux_ping.md)

### 10. Java的网络编程，讲讲NIO的实现方式，与BIO的区别，以及介绍常用的NIO框架。

### 11. 索引什么时候会失效变成全表扫描

### 12. 介绍下分布式的paxos和raft算法

## **二面**

### 1. 你在项目中怎么用到并发的。

### 2. 消息队列的使用场景，谈谈Kafka

### 3. 你说了解分布式服务，那么你怎么理解分布式服务。

### 4. Dubbo和Spring Clound的区别，以及使用场景。

### 5. 讲一下docker的实现原理，以及与JVM的区别。

### 6. MongoDB、Redis和Memcached的应用场景，各自优势

### 7. MongoDB有事务吗

### 8. Redis说一下sorted set底层原理

### 9. 讲讲Netty为什么并发高，相关的核心组件有哪些

## **三面**

### 1. 完整的画一个分布式集群部署图，从负载均衡到后端数据库集群。
### 2. 分布式锁的方案，Redis和Zookeeper那个好，如果是集群部署，高并发情况下哪个性能更好。
### 3. 分布式系统的全局id如何实现。
###  4. 数据库万级变成亿级，你如何来解决。
### 5. 常见的服务器雪崩是由什么引起的，如何来防范。

### 6.异地容灾怎么实现

### 7.常用的高并发技术解决方案有哪些，以及对应的解决步骤。

