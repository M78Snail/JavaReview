# Executors类中创建线程池的几种方法的分析

1、newFixedThreadPool：创建固定大小的线程池。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
/*
 * 创建具一个可重用的，有固定数量的线程池
 * 每次提交一个任务就提交一个线程，直到线程达到线城池大小，就不会创建新线程了
 * 线程池的大小达到最大后达到稳定不变，如果一个线程异常终止，则会创建新的线程
 */
public class newThreadpool {
	private static int pool = 2;
	public static void main(String[] args){
		ExecutorService ex = Executors.newFixedThreadPool(pool);
		for(int i=0; i<5; i++){
			runnable rn = new runnable();
			ex.execute(rn);
		}
		ex.shutdown();
	}
}
```

2、newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。

在Executors类中此方法的代码如下：

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
/* 
 * 具有缓冲功能的线程池，系统根据需要创建线程，线程会被缓冲到线程池中
 * 如果线程池大小超过了处理任务所需要的线程线程池就会回收空闲的线程池，
 * 当处理任务增加时，线程池可以增加线程来处理任务线程池不会对线程的大
 * 小进行限制线程池的大小依赖于操作系统
 */
public class Cached {
	public static void main(String[] args){
		ExecutorService ex = Executors.newCachedThreadPool();
		for(int i=0; i<5; i++){
			runnable rn = new runnable();
			ex.execute(rn);
		}
		ex.shutdown();
	}
}

```

3、newSingleThreadExecutor：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
/*
 * 创建只有一个线程的线程池
 */
public class Single {
	public static void main(String[] args){
		ExecutorService ex = Executors.newSingleThreadExecutor();
		for(int i=0; i<5; i++){
			runnable rn = new runnable();
			ex.execute(rn);
		}
		ex.shutdown();
	}
}
class runnable implements Runnable{
	public void run(){
		System.out.println(Thread.currentThread().getName());
	}
}

```

4、newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。

```
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;
/*
 * 创建一个线程池，大小可以设置，此线程支持定时以及周期性的执行任务定时任务
 */
public class Scheduled {
	private static int pool = 2;
	public static void main(String[] args){
		ScheduledExecutorService ex = Executors.newScheduledThreadPool(pool);
		runnable rn = new runnable();
		//参数1：目标对象   参数2：隔多长时间开始执行线程，    参数3：执行周期       参数4：时间单位
        ex.scheduleAtFixedRate(rn, 3, 1, TimeUnit.MILLISECONDS);
	}
}

```

5、newSingleThreadScheduledExecutor：创建一个单线程的线程池。此线程池支持定时以及周期性执行任务的需求。