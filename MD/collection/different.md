# HashMap、HashTable、ConcurrentHashMap的区别

|                          HashTable                           |                           HashMap                            |                      ConcurrentHashMap                       |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|                         方法是同步的                         |                        方法是非同步的                        |                         方法是同步的                         |
|                       基于Dictionary类                       |       基于AbstractMap，而AbstractMap基于Map接口的实现        |             基于双数组和链表的Map接口的同步实现              |
| key和value都不允许为null，遇到null，直接返回 NullPointerException | key和value都允许为null，遇到key为null的时候，调用putForNullKey方法进行处理，而对value没有处理 |                   不允许使用null值和null键                   |
|           hash数组默认大小是11，扩充方式是old*2+1            |          hash数组的默认大小是16，而且一定是2的指数           | 数据结构为一个Segment数组，Segment的数据结构为HashEntry的数组，而HashEntry存的是我们的键值对，可以构成链表。可以简单的理解为数组里装的是HashMap |

虽然三个集合类在多线程并发应用中都是线程安全的，但是他们有一个重大的差别，就是他们各自实现线程安全的方式。`Hashtable`是jdk1的一个遗弃的类，它把所有方法都加上`synchronized`关键字来实现线程安全。所有的方法都同步这样造成多个线程访问效率特别低。`Synchronized Map`与`HashTable`差别不大，也是在并发中作类似的操作，两者的唯一区别就是`Synchronized Map`没被遗弃，它可以通过使用`Collections.synchronizedMap()`来包装`Map`作为同步容器使用。

另一方面，`ConcurrentHashMap`的设计有点特别，表现在多个线程操作上。它不用做外的同步的情况下默认同时允许16个线程读和写这个Map容器。因为其内部的实现剥夺了锁，使它有很好的扩展性。不像`HashTable`和`Synchronized Map`，`ConcurrentHashMap`不需要锁整个Map，相反它划分了多个段(segments)，要操作哪一段才上锁那段数据。