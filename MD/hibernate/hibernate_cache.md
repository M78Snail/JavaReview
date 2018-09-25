## Hibernate的一级缓存

#### **Session对象的一级缓存（重点）**

1. 什么是缓存？
    * 其实就是一块内存空间,将数据源（数据库或者文件）中的数据存放到缓存中.再次获取的时候 ,直接从缓存中获取.可以提升程序的性能！

2. Hibernate框架提供了两种缓存
    * 一级缓存  -- 自带的不可卸载的.一级缓存的生命周期与session一致.一级缓存称为session级别的缓存.
    * 二级缓存  -- 默认没有开启，需要手动配置才可以使用的.二级缓存可以在多个session中共享数据,二级缓存称为是sessionFactory级别的缓存.

3. Session对象的缓存概述
    * Session接口中,有一系列的java的集合,这些java集合构成了Session级别的缓存(一级缓存).将对象存入到一级缓存中,session没有结束生命周期,那么对象在session中存放着
    * 内存中包含Session实例 --> Session的缓存（一些集合） --> 集合中包含的是缓存对象！

4. 证明一级缓存的存在，编写查询的代码即可证明
    * 在同一个Session对象中两次查询，可以证明使用了缓存

5. Hibernate框架是如何做到数据发生变化时进行同步操作的呢？
    * 使用get方法查询User对象
    * 然后设置User对象的一个属性，注意：没有做update操作。发现，数据库中的记录也改变了。
    * 利用快照机制来完成的（SnapShot）

![](https://github.com/M78Snail/JavaReview/blob/master/MD/hibernate/assets/cache.png)

