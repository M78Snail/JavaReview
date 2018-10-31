# 阿里巴巴的最新面试题目：基础+高级+架构

## **一面**

### 1. 自我介绍

### 2. Java中多态是怎么实现的

### 3. Java中的几种锁

### 4. 数据库隔离级别 脏读 幻读 ACID mysql的隔离级别

[数据库隔离级别 脏读 幻读 ACID mysql的隔离级别](https://github.com/M78Snail/ReadReview/blob/master/MD/db/mysql/isolation.md)

### 5. MySQL索引实现，如何解决慢查询

[MySQL索引实现](https://github.com/M78Snail/ReadReview/blob/master/MD/db/mysql/mysql_index.md)

[谈谈SQL慢查询的解决思路](https://github.com/M78Snail/ReadReview/blob/master/MD/db/mysql/slow_query.md)

### 6. 数据库锁是怎么实现的

### 7. 死锁的条件，进程和线程区别

[死锁](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/si-suo.md)

[进程和线程的区别](https://github.com/M78Snail/ReadReview/blob/master/MD/system/jin-cheng-he-xian-cheng-de-qu-bie.md)

### 8. tcp/ip模型,TCP和UDP的区别？

[OSI与TCP/IP各层的结构与功能](https://github.com/M78Snail/ReadReview/blob/master/MD/net/osiyu-tcp-ip-ge-ceng-de-jie-gou-yu-gong-neng-ff0c-du-you-na-xie-xie-yi.md)

[TCP和UDP的区别](https://github.com/M78Snail/ReadReview/blob/master/MD/net/tcpyuudp-de-qu-bie.md)

### 9. Linux查看网络 内存 日志命令

[Linux查看网络 内存 日志命令](https://github.com/M78Snail/ReadReview/blob/master/MD/system/linux_ping.md)

### 10. Spring中有哪些模块

[Spring框架分为哪七大模块](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/spring7.md)

### 11. HashMap、HashTable、ConcurrentHashMap的区别

- [HashMap源码分析及面试题解答](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/hashmapyuan-ma-fen-xi-ji-mian-shi-ti-jie-da.md)
- [HashTable源码分析](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/hashtableyuan-ma-fen-xi.md)
- [ConcurrentHashMap源码分析](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/concurrenthashmapyuan-ma-fen-xi.md)

|                          HashTable                           |                           HashMap                            |                      ConcurrentHashMap                       |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|                         方法是同步的                         |                        方法是非同步的                        |                         方法是同步的                         |
|                       基于Dictionary类                       |       基于AbstractMap，而AbstractMap基于Map接口的实现        |             基于双数组和链表的Map接口的同步实现              |
| key和value都不允许为null，遇到null，直接返回 NullPointerException | key和value都允许为null，遇到key为null的时候，调用putForNullKey方法进行处理，而对value没有处理 |                   不允许使用null值和null键                   |
|           hash数组默认大小是11，扩充方式是old*2+1            |          hash数组的默认大小是16，而且一定是2的指数           | 数据结构为一个Segment数组，Segment的数据结构为HashEntry的数组，而HashEntry存的是我们的键值对，可以构成链表。可以简单的理解为数组里装的是HashMap |

虽然三个集合类在多线程并发应用中都是线程安全的，但是他们有一个重大的差别，就是他们各自实现线程安全的方式。`Hashtable`是jdk1的一个遗弃的类，它把所有方法都加上`synchronized`关键字来实现线程安全。所有的方法都同步这样造成多个线程访问效率特别低。`Synchronized Map`与`HashTable`差别不大，也是在并发中作类似的操作，两者的唯一区别就是`Synchronized Map`没被遗弃，它可以通过使用`Collections.synchronizedMap()`来包装`Map`作为同步容器使用。

另一方面，`ConcurrentHashMap`的设计有点特别，表现在多个线程操作上。它不用做外的同步的情况下默认同时允许16个线程读和写这个Map容器。因为其内部的实现剥夺了锁，使它有很好的扩展性。不像`HashTable`和`Synchronized Map`，`ConcurrentHashMap`不需要锁整个Map，相反它划分了多个段(segments)，要操作哪一段才上锁那段数据。

### 12. CAS的底层实现

### 13. 谈Java GC

### 14. 栈和队列

### 15. 10万个URL去重

### 16. TCP的状态？TIME_WAIT

[TCP三次握手过程](https://github.com/M78Snail/JavaReview/blob/master/MD/net/tcpde-san-ci-wo-shou-yu-si-ci-huishou-guo-cheng-ff0c-ge-ge-zhuang-tai-ming-cheng-yu-han-yi-ff0c-timewait-de-zuo-yong.md)

[是不是所有执行主动关闭的socket都会进入TIME_WAIT状态呢？](https://github.com/M78Snail/JavaReview/blob/master/MD/net/tcpde-san-ci-wo-shou-yu-si-ci-huishou-guo-cheng-ff0c-ge-ge-zhuang-tai-ming-cheng-yu-han-yi-ff0c-timewait-de-zuo-yong.md#是不是所有执行主动关闭的socket都会进入time_wait状态呢)

## **二面**

1. Volatile关键字
2. JVM的垃圾回收算法有哪些
3. 讲讲JVM内存模型
4. 启动线程有哪几种方式，线程池有哪几种？
5. springIOC原理？自己实现IOC要怎么做，哪些步骤？
6. MongoDB是行存储还是列存储
7. Hbase，Redis，Nginx？
8. 用过mysql吗？为啥加索引会变快？聚簇型索引和非聚簇型索引的区别？

## **三面**

1. JVM调优，程序挂起后如何排查原因？
2. redis如何实现分布式锁
3. 聊到dubbo,zookeeper了解吗？netty实战过吗？
4. 你参与的项目，画出集群部署图，以及服务之间的关联，设计的核心点。