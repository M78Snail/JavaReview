# Java线程池分析

### 1. 概述

本文首先阐述Java线程池的运行机制、参数配置，随后指出其存在何种“缺陷”，最后基于现有机制做策略优化，定制一种更合理、有效的线程池。

### 2. Java线程池机制

Java可以通过Executors提供的工厂方法创建线程池，也可以通过ThreadPoolExecutor直接创建，本质上二者并无差别。

ThreadPoolExecutor最完整的构造函数如下:

```
ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, 
                   TimeUnit unit, BlockingQueue<Runnable> workQueue, 
                   ThreadFactory threadFactory, RejectedExecutionHandler handler)
```

下面逐一来看各参数含义。

#### 2.1 corePoolSize\maximumPoolSize

-  **corePoolSize**：线程池中核心线程数量。
-  **maximumPoolSize**：线程池同时允许存在的最大线程数量。

内部处理逻辑如下：

1. 当线程池中工作线程数小于corePoolSize，创建新的工作线程来执行该任务，不管线程池中是否存在空闲线程。
2. 如果线程池中工作线程数达到corePoolSize，新任务尝试放入队列，入队成功的任务将等待工作线程空闲时调度。
    2.1 如果队列满并且线程数小于maximumPoolSize，创建新的线程执行该任务(注意：队列中的任务继续排序)。
    2.2 如果队列满且线程数超过maximumPoolSize，拒绝该任务。

#### 2.2 keepAliveTime

当线程池中工作线程数大于corePoolSize，并且线程空闲时间超过keepAliveTime，则这些线程将被终止。同样，可以将这种策略应用到核心线程，通过调用allowCoreThreadTimeout来实现。

#### 2.3 BlockingQueue

任务等待队列，用于缓存暂时无法执行的任务。分为如下三种堵塞队列：

-  **直接递交**。如SynchronousQueue，该策略直接将任务直接交给工作线程。如果当前没有空闲工作线程，创建新线程。这种策略最好是配合unbounded线程数来使用，从而避免任务被拒绝。但当任务生产速度大于消费速度，将导致线程数不断的增加。
-  **无界队列**。如LinkedBlockingQueue，当工作的线程数达到核心线程数时，新的任务被放在队列上。因此，永远不会有大于corePoolSize的线程被创建，maximumPoolSize参数失效。这种策略比较适合所有的任务都不相互依赖，独立执行。但是当任务处理速度小于任务进入速度的时候会引起队列的无限膨胀。
-  **有界队列**。如ArrayBlockingQueue，按前面描述的corePoolSize、maximumPoolSize、BlockingQueue处理逻辑处理。队列长度和maximumPoolSize两个值会相互影响：

> - **长队列 + 小maximumPoolSize**。会减少CPU的使用、操作系统资源、上下文切换的消耗，但是会降低吞吐量，如果任务被频繁的阻塞如IO线程，系统其实可以调度更多的线程。
> -  **短队列 + 大maximumPoolSize**。CPU更忙，但会增加线程调度的消耗.

> 总结一下，IO密集型可以考虑多些线程来平衡CPU的使用，CPU密集型可以考虑少些线程减少线程调度的消耗。

#### 2.4 ThreadFactory

线程池中的工作线程通过ThreadFactory来创建的，如果没有指定，默认为Executors#defaultThreadFactory。这个时候创建的线程将都属于同一个线程组，拥有同样的优先级和daemon状态。采用自定义的ThreadFactory，可以配置线程的名字、线程组合daemon状态。如果调用ThreadFactory#createThread的时候失败，将返回null，executor将不会执行任何任务。

#### 2.5 RejectedExecutionHandler

当新的任务无法进入等待队列且线程数已达maximumPoolSize上线时，需要定制拒绝策略，拒绝该任务。ThreadPoolExecutor提供了如下可选策略：

- ThreadPoolExecutor#AbortPolicy：这个策略直接抛出RejectedExecutionException异常。
- ThreadPoolExecutor#CallerRunsPolicy：这个策略将会使用Caller线程来执行这个任务，这是一种feedback策略，可以降低任务提交的速度。
- ThreadPoolExecutor#DiscardPolicy：这个策略将会直接丢弃任务。
- ThreadPoolExecutor#DiscardOldestPolicy：这个策略将会把任务队列头部的任务丢弃，然后重新尝试执行，如果还是失败则继续实施策略。