# 读书笔记


> `Java Core `：处于萌芽阶段的 Java 核心知识库。

| 📊 |⚔️ | 🖥 | 🚏 | 🏖  | 🌁| 📮 | 🔍 | 🚀 | 🌈 |💡
| :--------: | :---------: | :---------: | :---------: | :---------: | :---------:| :---------: | :-------: | :-------:| :------:|:------:|
| [集合](#常用集合) | [操作系统](#操作系统)|[JVM](#jvm) | [分布式](#分布式相关) |[SSM框架](#ssm框架) |[网络](#网络)| [数据库](#db-相关) |[数据结构](#数据结构与算法)|[大数据](#大数据)| [设计模式](#设计模式)|[环境配置](#环境配置) |



### 常用集合
- [HashMap源码分析及面试题解答](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/hashmapyuan-ma-fen-xi-ji-mian-shi-ti-jie-da.md)
- [HashSet源码分析](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/javaji-chu.md)
- [HashTable源码分析](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/hashtableyuan-ma-fen-xi.md)
- [ConcurrentHashMap源码分析](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/concurrenthashmapyuan-ma-fen-xi.md)
- [死锁](https://github.com/M78Snail/ReadReview/blob/master/MD/collection/si-suo.md)

### 操作系统
- [多线程中的常见问题](https://github.com/crossoverJie/JCSprout/blob/master/MD/Thread-common-problem.md)
- [synchronized 关键字原理](https://github.com/crossoverJie/JCSprout/blob/master/MD/Synchronize.md)
- [多线程的三大核心](https://github.com/crossoverJie/JCSprout/blob/master/MD/Threadcore.md)
- [对锁的一些认知](https://github.com/crossoverJie/JCSprout/blob/master/MD/Java-lock.md)
- [ReentrantLock 实现原理 ](https://github.com/crossoverJie/JCSprout/blob/master/MD/ReentrantLock.md)
- [ConcurrentHashMap 的实现原理](https://github.com/crossoverJie/JCSprout/blob/master/MD/ConcurrentHashMap.md)
- [如何优雅的使用和理解线程池](https://github.com/crossoverJie/JCSprout/blob/master/MD/ThreadPoolExecutor.md)
- [深入理解线程通信](https://github.com/crossoverJie/JCSprout/blob/master/MD/concurrent/thread-communication.md)
- [交替打印奇偶数](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/actual/TwoThread.java)

### JVM
- [Java 运行时内存划分](https://github.com/crossoverJie/JCSprout/blob/master/MD/MemoryAllocation.md)
-  [类加载机制](https://github.com/crossoverJie/JCSprout/blob/master/MD/ClassLoad.md)
-  [OOM 分析](https://github.com/crossoverJie/JCSprout/blob/master/MD/OOM-analysis.md)
- [垃圾回收](https://github.com/crossoverJie/JCSprout/blob/master/MD/GarbageCollection.md)
- [对象的创建与内存分配](https://github.com/crossoverJie/JCSprout/blob/master/MD/newObject.md)
- [你应该知道的 volatile 关键字](https://github.com/crossoverJie/JCSprout/blob/master/MD/concurrent/volatile.md)

### 分布式相关

- [Dubbo使用](https://github.com/M78Snail/ReadReview/blob/master/MD/distributed/dubbo.md)
- Solr
  - [Solr单机版](https://github.com/M78Snail/ReadReview/blob/master/MD/solr/solr.md)
  - [Solr集群](https://github.com/M78Snail/ReadReview/blob/master/MD/solr/solr_jiqun.md)
  - [ActiveMQ同步索引库](https://github.com/M78Snail/ReadReview/blob/master/MD/solr/activemq.md)
- [分布式缓存设计](https://github.com/crossoverJie/JCSprout/blob/master/MD/Cache-design.md)
- [分布式 ID 生成器](https://github.com/crossoverJie/JCSprout/blob/master/MD/ID-generator.md)

### SSM框架

- SpringMVC
  - [SpringMVC配置](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/springmvc.md)
  - [参数绑定](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/can_shu_bang_ding.md)
  - [高级参数绑定](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/gao-ji-can-shu-bang-ding.md)
  - [@RequestMapping注解的使用](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/requestmappingzhu-jie-de-shi-yong.md)
  - [Contriller方法返回值](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/controllerfang-fa-fan-hui-zhi.md)
  - [图片上传处理](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/tu-pian-shang-chuan-chu-li.md)
  - [JSON数据交互](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/jsonshu-ju-zhi-chi.md)
  - [SpringMVC实现Restful](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/springmvcshi-xian-restful.md)
  - [拦截器](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/lan-jie-qi.md)
  - [登录权限认证](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/deng-lu-quan-xian-ren-zheng.md)
  - [获取配置文件中的常量](https://github.com/M78Snail/ReadReview/blob/master/MD/springmvc/huo-qu-properties-wen-jian-zhong-ding-yi-de-chang-liang.md)
- Spring
  - [Spring框架中标签的配置](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/springkuang-jia-4e2d3c-bean-biao-qian-de-pei-zhi.md)
  - [依赖注入-- 配置文件方式](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/yi-lai-zhu-ru.md)
  - [依赖注入-- 注解方式](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/yi-lai-zhu-5165-zhu-jie-fang-shi.md)
  - [AOP（XML方式）](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/aop.md)
  - [AOP（注解方式）](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/aopff08-zhu-jie-fang-shi-ff09.md)
  - [JDBC](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/jdbc.md)
  - [事务管理](https://github.com/M78Snail/ReadReview/blob/master/MD/spring/shi-wu-guan-li.md)
- MyBatis
  - [MyBatis概述](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/mybatis.md)
  - [1、Mybatis的入门](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/1mybatisde-ru-men.md)
  - [2、Dao的开发方法](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/2daode-kai-fa-fang-fa.md)
  - [3、SqlMapConfig.xml文件说明](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/3sqlmapconfigxmlwen-jian-shuo-ming.md)
  - [4、输入映射和输出映射](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/43001-shu-ru-ying-she-he-shu-chu-ying-she.md)
  - [5、动态sql](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/53001-dong-tai-sql.md)
  - [6、关联查询](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/63001-guan-lian-cha-xun.md)
  - [7、Mybatis整合spring](https://github.com/M78Snail/ReadReview/blob/master/MD/mybatis/7mybatiszheng-he-spring.md)
- [整合SSM](https://github.com/M78Snail/ReadReview/blob/master/MD/ssm/zheng-he-ssm.md)


### 网络
- [秒杀系统设计](https://github.com/crossoverJie/JCSprout/blob/master/MD/Spike.md)
- [秒杀架构实践](http://crossoverjie.top/2018/05/07/ssm/SSM18-seconds-kill/)

### DB 相关

- [MySQL 索引原理](https://github.com/crossoverJie/JCSprout/blob/master/MD/MySQL-Index.md)
- [SQL 优化](https://github.com/crossoverJie/JCSprout/blob/master/MD/SQL-optimization.md)
- Redis
  - [什么是Redis](https://github.com/M78Snail/ReadReview/blob/master/MD/redis/what_is_redis.md)
  - [Redis安装与使用](https://github.com/M78Snail/ReadReview/blob/master/MD/redis/redis_install_start.md)

### 数据结构与算法
- [红包算法](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/red/RedPacket.java)
- [二叉树层序遍历](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/BinaryNode.java#L76-L101)
- [是否为快乐数字](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/HappyNum.java#L38-L55)
- [链表是否有环](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/LinkLoop.java#L32-L59)
- [从一个数组中返回两个值相加等于目标值的下标](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/TwoSum.java#L38-L59)
- [一致性 Hash 算法](https://github.com/crossoverJie/JCSprout/blob/master/MD/Consistent-Hash.md)
- [限流算法](https://github.com/crossoverJie/JCSprout/blob/master/MD/Limiting.md)
- [三种方式反向打印单向链表](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/ReverseNode.java)
- [合并两个排好序的链表](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/MergeTwoSortedLists.java)
- [两个栈实现队列](https://github.com/crossoverJie/JCSprout/blob/master/src/main/java/com/crossoverjie/algorithm/TwoStackQueue.java)
- [动手实现一个 LRU cache](http://crossoverjie.top/2018/04/07/algorithm/LRU-cache/)
- [链表排序](./src/main/java/com/crossoverjie/algorithm/LinkedListMergeSort.java)
- [数组右移 k 次](./src/main/java/com/crossoverjie/algorithm/ArrayKShift.java)

### 算法
- [SpringBoot 整合长连接心跳机制](https://crossoverjie.top/2018/05/24/netty/Netty(1)TCP-Heartbeat/)
- [从线程模型的角度看 Netty 为什么是高性能的？](https://crossoverjie.top/2018/07/04/netty/Netty(2)Thread-model/)

### 设计模式

- [TCP/IP 协议](https://github.com/crossoverJie/JCSprout/blob/master/MD/TCP-IP.md)
- [一个学渣的阿里之路](https://crossoverjie.top/2018/06/21/personal/Interview-experience/)
- [如何成为一位「不那么差」的程序员](https://crossoverjie.top/2018/08/12/personal/how-to-be-developer/)
- [如何高效的使用 Git](https://github.com/crossoverJie/JCSprout/blob/master/MD/additional-skills/how-to-use-git-efficiently.md)


### 环境配置

- [Linux开发环境](https://github.com/M78Snail/ReadReview/blob/master/MD/setting/linux.md)