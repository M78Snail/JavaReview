死锁是两个甚至多个线程被永久阻塞时的一种运行局面，这种局面的生成伴随着至少两个线程和两个或者多个资源。在这里我已写好一个简单的程序，它将会引起死锁方案然后我们就会明白如何分析它。

# Java死锁范例

```
package com.journaldev.threads;
 
public class ThreadDeadlock {
 
    public static void main(String[] args) throws InterruptedException {
        Object obj1 = new Object();
        Object obj2 = new Object();
        Object obj3 = new Object();
 
        Thread t1 = new Thread(new SyncThread(obj1, obj2), "t1");
        Thread t2 = new Thread(new SyncThread(obj2, obj3), "t2");
        Thread t3 = new Thread(new SyncThread(obj3, obj1), "t3");
 
        t1.start();
        Thread.sleep(5000);
        t2.start();
        Thread.sleep(5000);
        t3.start();
 
    }
 
}
 
class SyncThread implements Runnable{
    private Object obj1;
    private Object obj2;
 
    public SyncThread(Object o1, Object o2){
        this.obj1=o1;
        this.obj2=o2;
    }
    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println(name + " acquiring lock on "+obj1);
        synchronized (obj1) {
         System.out.println(name + " acquired lock on "+obj1);
         work();
         System.out.println(name + " acquiring lock on "+obj2);
         synchronized (obj2) {
            System.out.println(name + " acquired lock on "+obj2);
            work();
        }
         System.out.println(name + " released lock on "+obj2);
        }
        System.out.println(name + " released lock on "+obj1);
        System.out.println(name + " finished execution.");
    }
    private void work() {
        try {
            Thread.sleep(30000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

在上面的程序中同步线程正完成Runnable的接口，它工作的是两个对象，这两个对象向对方寻求死锁而且都在使用同步阻塞。

在主函数中，我使用了三个为同步线程运行的线程，而且在其中每个线程中都有一个可共享的资源。

这些线程以向第一个对象获取封锁这种方式运行。但是当它试着像第二个对象获取封锁时，它就会进入等待状态，因为它已经被另一个线程封锁住了。这样，在线程引起死锁的过程中，就形成了一个依赖于资源的循环。

当我执行上面的程序时，就产生了输出，但是程序却因为死锁无法停止。

```
t1 acquiring lock on java.lang.Object@6d9dd520
t1 acquired lock on java.lang.Object@6d9dd520
t2 acquiring lock on java.lang.Object@22aed3a5
t2 acquired lock on java.lang.Object@22aed3a5
t3 acquiring lock on java.lang.Object@218c2661
t3 acquired lock on java.lang.Object@218c2661
t1 acquiring lock on java.lang.Object@22aed3a5
t2 acquiring lock on java.lang.Object@218c2661
t3 acquiring lock on java.lang.Object@6d9dd520
```

# 避免死锁

有很多方针可供我们使用来避免死锁的局面。

* **避免嵌套封锁：**
  这是死锁最主要的原因的，如果你已经有一个资源了就要避免封锁另一个资源。如果你运行时只有一个对象封锁，那是几乎不可能出现一个死锁局面的。例如，这里是另一个运行中没有嵌套封锁的run\(\)方法，而且程序运行没有死锁局面，运行得很成功。

```
public void run() {
    String name = Thread.currentThread().getName();
    System.out.println(name + " acquiring lock on " + obj1);
    synchronized (obj1) {
        System.out.println(name + " acquired lock on " + obj1);
        work();
    }
    System.out.println(name + " released lock on " + obj1);
    System.out.println(name + " acquiring lock on " + obj2);
    synchronized (obj2) {
        System.out.println(name + " acquired lock on " + obj2);
        work();
    }
    System.out.println(name + " released lock on " + obj2);
 
    System.out.println(name + " finished execution.");
}
```

* **只对有请求的进行封锁：**你应当只想你要运行的资源获取封锁，比如在上述程序中我在封锁的完全的对象资源。但是如果我们只对它所属领域中的一个感兴趣，那我们应当封锁住那个特殊的领域而并非完全的对象。

* **避免无限期的等待：**
  如果两个线程正在等待对象结束，无限期的使用线程加入，如果你的线程必须要等待另一个线程的结束，若是等待进程的结束加入最好准备最长时间。

