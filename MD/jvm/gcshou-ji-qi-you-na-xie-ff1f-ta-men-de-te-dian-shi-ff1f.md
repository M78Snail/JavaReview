# GC收集器有哪些？它们的特点是？

常见的GC收集器如下图所示，连线代表可搭配使用：

![](https://github.com/M78Snail/JavaReview/blob/master/MD/jvm/assets/import2.6.1.png) 

### **1.Serial收集器（串行收集器）**

用于新生代的单线程收集器，收集时需要暂停所有工作线程（Stop the world）。优点在于：简单高效，单个CPU时没有线程交互的开销，堆较小时停顿时间不长。常与Serial Old 收集器一起使用，示意图如下所示：

![](https://github.com/M78Snail/JavaReview/blob/master/MD/jvm/assets/import2.6.2.png) 

### *2.ParNew收集器（parallel new 收集器，新生代并行收集器）*

Serial收集器多线程版本，除了使用多线程外和Serial收集器一模一样。常与Serial Old 收集器一起使用，示意图如下：

![](https://github.com/M78Snail/JavaReview/blob/master/MD/jvm/assets/import2.6.3.png) 

### *3.Parallel Scavenge收集器*

与ParNew收集器一样是一款多线程收集器，其特点在于关注点与别的GC收集器不同：一般的GC收集器关注于缩短工作线程暂停的时间，而该收集器关注于吞吐量，因此也被称为吞吐量优先收集器。（吞吐量 = 用户运行代码时间 / \(用户运行代码时间 + 垃圾回收时间\)）高吞吐量与停顿时间短相比主要强调任务快完成，因此常和Parallel Old 收集器一起使用（没有Parallel Old之前与Serial Old一起使用），示意图如下：

| ![](https://github.com/M78Snail/JavaReview/blob/master/MD/jvm/assets/import2.6.4.png) |
| :---: |

### *4.Serial Old收集器*

Serial收集器的年老代版本，不在赘述。

### *5.Parallel Old收集器*  

年老代的并行收集器，在JDK1.6开始使用。

### *6.CMS收集器（Concurrent Mark Sweep，并发标记清除收集器）*  

CMS收集器是一个年老代的收集器，是以最短回收停顿时间为目标的收集器，其示意图如下所示：

| ![](https://github.com/M78Snail/JavaReview/blob/master/MD/jvm/assets/import2.6.5.png) |
| :---: |


CMS收集器基于标记清除算法实现，主要分为4个步骤：  
  1. 初始标记，需要stop the world，标记GC Root能关联到的对象，速度快  

  2. 并发标记，对GC Root执行可达性算法  

  3. 重新标记，需要stop the world，修复并发标记时因用户线程运行而产生的标记变化，所需时间比初始标记长，但远比并发标记短  

  4. 并发清理  
    CMS收集器的缺点在于：  

         　1. 其对于CPU资源很敏感。在并发阶段，虽然CMS收集器不会暂停用户线程，但是会因为占用了一部分CPU资源而导致应用程序变慢，总吞吐量降低。其默认启动的回收线程数是（cpu数量+3）/4，当cpu数较少的时候，会分掉大部分的cpu去执行收集器线程  ；
         　2. 无法处理浮动垃圾，浮动垃圾即在并发清除阶段因为是并发执行，还会产生垃圾，这一部分垃圾即为浮动垃圾，要等下次收集  CMS收集器使用的是标记清除算法，GC后会产生碎片  ；

### *7.G1收集器（Garbage First收集器）*

相比CMS收集器，G1收集器主要有两处改进：

  1. 使用标记整理算法，确保GC后不会产生内存碎片  

 5. 可以精确控制停顿，允许指定消耗在垃圾回收上的时间

G1收集器可以实现在基本不牺牲吞吐量的前提下完成低停顿的内存回收，这是由于它能够极力地避免全区域的垃圾收集，之前的收集器进行收集的范围都是整个新生代或老年代，而G1将整个Java堆（包括新生代、老年代）划分为多个大小固定的独立区域（Region），并且跟踪这些区域里面的垃圾堆积程度，在后台维护一个优先列表，每次根据允许的收集时间，优先回收垃圾最多的区域（这就是Garbage First名称的来由）。区域划分及有优先级的区域回收，保证了G1收集器在有限的时间内可以获得最高的收集效率。

