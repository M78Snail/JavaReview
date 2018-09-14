**1.Eden区**  
Eden区位于Java堆的年轻代，是新对象分配内存的地方，由于堆是所有线程共享的，因此在堆上分配内存需要加锁。而Sun JDK为提升效率，会为每个新建的线程在Eden上分配一块独立的空间由该线程独享，这块空间称为TLAB（Thread Local Allocation Buffer）。在TLAB上分配内存不需要加锁，因此JVM在给线程中的对象分配内存时会尽量在TLAB上分配。如果对象过大或TLAB用完，则仍然在堆上进行分配。如果Eden区内存也用完了，则会进行一次Minor GC（young GC）。

**2.Survival from to**  
Survival区与Eden区相同都在Java堆的年轻代。Survival区有两块，一块称为from区，另一块为to区，这两个区是相对的，在发生一次Minor GC后，from区就会和to区互换。在发生Minor GC时，Eden区和Survival from区会把一些仍然存活的对象复制进Survival to区，并清除内存。Survival to区会把一些存活得足够旧的对象移至年老代。

**3.年老代**  
年老代里存放的都是存活时间较久的，大小较大的对象，因此年老代使用标记整理算法。当年老代容量满的时候，会触发一次Major GC（full GC），回收年老代和年轻代中不再被使用的对象资源。

