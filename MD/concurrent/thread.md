# 启动线程有哪几种方式

## 线程创建的几种方式

> 在并发编程中，最基本的就是创建线程了，那么一般的创建姿势是怎样的，又都有些什么区别

一般来讲线程创建有四种方式:

1. 继承Thread
2. 实现Runnable接口
3. 实现Callable接口，结合 FutureTask使用
4. 利用该线程池ExecutorService、Callable、Future来实现

所以本篇博文从布局来讲，分为两部分

1. 实例演示四种使用方式
2. 对比分析四种使用方式的异同，以及适合的应用场景

## I. 实例演示

**目标: 创建两个线程并发实现从1-1000的累加**

### 1. 继承Thread实现线程创建

实现逻辑如下

```
public class AddThread extends Thread {

    private int start, end;

    private int sum = 0;

    public AddThread(String name, int start, int end) {
        super(name);
        this.start = start;
        this.end = end;
    }
    public void run() {
        System.out.println("Thread-" + getName() + " 开始执行!");
        for (int i = start; i <= end; i ++) {
            sum += i;
        }
        System.out.println("Thread-" + getName() + " 执行完毕! sum=" + sum);
    }

    public static void main(String[] args) throws InterruptedException {
        int start = 0, mid = 500, end = 1000;

        AddThread thread1 = new AddThread("线程1", start, mid);
        AddThread thread2 = new AddThread("线程2", mid + 1, end);

        thread1.start();
        thread2.start();

        // 确保两个线程执行完毕
        thread1.join();
        thread2.join();

        int sum = thread1.sum + thread2.sum;
        System.out.println("ans: " + sum);
    }
}
```

输出结果

```
Thread-线程1 开始执行!
Thread-线程2 开始执行!
Thread-线程1 执行完毕! sum=125250
Thread-线程2 执行完毕! sum=375250
ans: 500500
```

一般实现步骤:

- 继承 `Thread` 类
- 覆盖 `run()` 方法
- 直接调用 `Thread#start()` 执行

逻辑比较清晰，只需要注意覆盖的是run方法，而不是start方法

### 2. 实现Runnable接口方式创建线程

```
public class AddRun implements Runnable {

    private int start, end;
    private int sum = 0;

    public AddRun(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " 开始执行!");
        for(int i = start; i <= end; i++) {
            sum += i;
        }
        System.out.println(Thread.currentThread().getName() + " 执行完毕! sum=" + sum);
    }


    public static void main(String[] args) throws InterruptedException {
        int start = 0, mid = 500, end = 1000;
        AddRun run1 = new AddRun(start, mid);
        AddRun run2 = new AddRun(mid + 1, end);
        Thread thread1 = new Thread(run1, "线程1");
        Thread thread2 = new Thread(run2, "线程2");

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();
        int sum = run1.sum + run2.sum;
        System.out.println("ans: " + sum);
    }
}
```

输出结果

```
线程2 开始执行!
线程1 开始执行!
线程2 执行完毕! sum=375250
线程1 执行完毕! sum=125250
ans: 500500
```

一般实现步骤:

- 实现`Runnable`接口
- 获取实现Runnable接口的实例，作为参数，创建Thread实例
- 执行 `Thread#start()` 启动线程

**说明**

相比于继承Thread，这里是实现一个接口，最终依然是借助 `Thread#start()`来启动线程

**然后就有个疑问：**

两者是否有本质上的区别，在实际项目中如何抉择？

### 3. 实现Callable接口，结合FutureTask创建线程

> Callable接口相比于Runnable接口而言，会有个返回值，那么如何利用这个返回值呢?

demo如下

```
public class AddCall implements Callable<Integer> {

    private int start, end;

    public AddCall(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    public Integer call() throws Exception {
        int sum = 0;
        System.out.println(Thread.currentThread().getName() + " 开始执行!");
        for (int i = start; i <= end; i++) {
            sum += i;
        }
        System.out.println(Thread.currentThread().getName() + " 执行完毕! sum=" + sum);
        return sum;
    }


    public static void main(String[] args) throws ExecutionException, InterruptedException {
        int start = 0, mid = 500, end = 1000;
        FutureTask<Integer> future1 = new FutureTask<>(new AddCall(start, mid));
        FutureTask<Integer> future2 = new FutureTask<>(new AddCall(mid + 1, end));

        Thread thread1 = new Thread(future1, "线程1");
        Thread thread2 = new Thread(future2, "线程2");

        thread1.start();
        thread2.start();

        int sum1 = future1.get();
        int sum2 = future2.get();
        System.out.println("ans: " + (sum1 + sum2));
    }
}
```

输出结果

```
线程2 开始执行!
线程1 开始执行!
线程2 执行完毕! sum=375250
线程1 执行完毕! sum=125250
ans: 500500
```

一般实现步骤：

- 实现`Callable`接口
- 以`Callable`的实现类为参数，创建`FutureTask`实例
- 将`FutureTask`作为Thread的参数，创建Thread实例
- 通过 `Thread#start` 启动线程
- 通过 `FutreTask#get()` 阻塞获取线程的返回值

**说明**

Callable接口相比Runnable而言，会有结果返回，因此会由FutrueTask进行封装，以期待获取线程执行后的结果；

最终线程的启动都是依赖`Thread#start`

### 4. 线程池方式创建

demo如下，创建固定大小的线程池，提交Callable任务，利用Future获取返回的值

```
public class AddPool implements Callable<Integer> {
    private int start, end;

    public AddPool(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    public Integer call() throws Exception {
        int sum = 0;
        System.out.println(Thread.currentThread().getName() + " 开始执行!");
        for (int i = start; i <= end; i++) {
            sum += i;
        }
        System.out.println(Thread.currentThread().getName() + " 执行完毕! sum=" + sum);
        return sum;
    }

    public static void main(String[] arg) throws ExecutionException, InterruptedException {
        int start=0, mid=500, end=1000;
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        Future<Integer> future1 = executorService.submit(new AddPool(start, mid));
        Future<Integer> future2 = executorService.submit(new AddPool(mid+1, end));

        int sum = future1.get() + future2.get();
        System.out.println("sum: " + sum);
    }
}
```

输出

```
pool-1-thread-1 开始执行!
pool-1-thread-2 开始执行!
pool-1-thread-1 执行完毕! sum=125250
pool-1-thread-2 执行完毕! sum=375250
sum: 500500
```

