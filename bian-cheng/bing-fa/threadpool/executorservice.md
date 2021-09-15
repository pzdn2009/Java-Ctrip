# ExecutorService Overview

## 1. 体系

![](../../../.gitbook/assets/image%20%2822%29.png)

* **Executor**：一个接口，其定义了一个接收Runnable对象的方法executor，其方法签名为executor\(Runnable command\),
* **ExecutorService**：是一个比Executor使用更广泛的子类接口，其提供了**生命周期管理**的方法，以及可跟踪一个或多个异步任务执行状况返回Future的方法
* **AbstractExecutorService**：ExecutorService执行方法的默认实现
* **ScheduledExecutorService**：一个可定时调度任务的接口
* **ScheduledThreadPoolExecutor**：ScheduledExecutorService的实现，一个可定时调度任务的线程池
* **ThreadPoolExecutor**：线程池，可以通过调用Executors以下静态工厂方法来创建线程池并返回一个ExecutorService对象

## 2. ThreadPoolExecutor构造器

```java
/**
* Creates a new {@code ThreadPoolExecutor} with the given initial
* parameters.
*
* @param corePoolSize the number of threads to keep in the pool, even
* if they are idle, unless {@code allowCoreThreadTimeOut} is set
* @param maximumPoolSize the maximum number of threads to allow in the
* pool
* @param keepAliveTime when the number of threads is greater than
* the core, this is the maximum time that excess idle threads
* will wait for new tasks before terminating.
* @param unit the time unit for the {@code keepAliveTime} argument
* @param workQueue the queue to use for holding tasks before they are
* executed. This queue will hold only the {@code Runnable}
* tasks submitted by the {@code execute} method.
* @param threadFactory the factory to use when the executor
* creates a new thread
* @param handler the handler to use when execution is blocked
* because the thread bounds and queue capacities are reached
* @throws IllegalArgumentException if one of the following holds:<br>
* {@code corePoolSize < 0}<br>
* {@code keepAliveTime < 0}<br>
* {@code maximumPoolSize <= 0}<br>
* {@code maximumPoolSize < corePoolSize}
* @throws NullPointerException if {@code workQueue}
* or {@code threadFactory} or {@code handler} is null
*/
public ThreadPoolExecutor(int corePoolSize,
int maximumPoolSize,
long keepAliveTime,
TimeUnit unit,
BlockingQueue<Runnable> workQueue,
ThreadFactory threadFactory,
RejectedExecutionHandler handler) {
```

* corePoolSize - 线程池核心池的大小。
* maximumPoolSize - 线程池的最大线程数。
* keepAliveTime - 如果线程数多于corePoolSize,则这些多余的线程的空闲时间超过keepAliveTime时将被终止。
* unit - keepAliveTime 的时间单位。
* workQueue - 用来储存等待执行任务的队列。
* threadFactory - 线程工厂，默认使用**defaultThreadFactory**创建线程。
* handler - 拒绝策略。定义处理被拒绝任务的策略，默认使用ThreadPoolExecutor.**AbortPolicy**,任务被拒绝时将抛出RejectExecutorException

## 3. Executors

### 3.1 newSingleThreadExecutor

创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。 此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

```java
public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
    return new Executors.FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                    0L, TimeUnit.MILLISECONDS,
                    new LinkedBlockingQueue<Runnable>(),
                    threadFactory));
}
```

### 3.2 newFixedThreadPool

创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。 线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
            0L, TimeUnit.MILLISECONDS,
            new LinkedBlockingQueue<Runnable>());
}
```

### 3.3 newCachedThreadPool

创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程， 那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。 此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
            60L, TimeUnit.SECONDS,
            new SynchronousQueue<Runnable>());
}
```

### 3.4 newScheduledThreadPool

创建一个可延迟执行或定期执行的线程池

```java
public static ScheduledExecutorService newScheduledThreadPool(
        int corePoolSize, ThreadFactory threadFactory) {
    return new ScheduledThreadPoolExecutor(corePoolSize, threadFactory);
}
```

使用：

```java
public class HeartBeat {
    public static void main(String[] args) {
        ScheduledExecutorService executor = Executors.newScheduledThreadPool(5);
        Runnable task = new Runnable() {
            public void run() {
                System.out.println("HeartBeat.........................");
            }
        };
        executor.scheduleAtFixedRate(task,5,3, TimeUnit.SECONDS);   //5秒后第一次执行，之后每隔3秒执行一次
    }
}
```

### 3.5 ALI规范建议

【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

线程池有两个线程数的设置，一个为**核心池线程数**，一个为**最大线程数**。

在创建了线程池后，默认情况下，线程池中并没有任何线程，等到有任务来才创建线程去执行任务，除非调用了prestartAllCoreThreads\(\)或者prestartCoreThread\(\)方法。

当创建的线程数等于 corePoolSize 时，会加入设置的阻塞队列。当队列满时，会创建线程执行任务直到线程池中的数量等于maximumPoolSize。

说明：Executors 各个方法的弊端：

1）newFixedThreadPool 和 newSingleThreadExecutor: 主要问题是堆积的**请求处理队列**可能会耗费非常大的内存，甚至 OOM\(Out Of Memory\)。

2）newCachedThreadPool 和 newScheduledThreadPool: 主要问题是线程数最大数是 Integer.MAX\_VALUE，可能会**创建数量非常多的线程**，甚至 OOM。

## 4. ExecutorService 

### 4.1 生命周期管理

ExecutorService提供了管理Executor生命周期的方法，ExecutorService的生命周期包括了：**运行、关闭和终止**三种状态。

1. ExecutorService在初始化创建时处于运行状态。
2. `shutdown`方法等待提交的任务执行完成并不再接受新任务，在完成全部提交的任务后关闭
3. `shutdownNow`方法将强制终止所有运行中的任务并不再允许提交新任务

### 4.2  提交任务

```java
<T> Future<T> submit(Callable<T> task);
<T> Future<T> submit(Runnable task, T result);
Future<?> submit(Runnable task);
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
        throws InterruptedException;
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
                                  long timeout, TimeUnit unit)
        throws InterruptedException;
```

Eg: 

```java
public class CallableAndFuture {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        Future<String> future = executor.submit(new Callable<String>() {   //接受一上callable实例
            public String call() throws Exception {
                return "MOBIN";
            }
        });
        System.out.println("任务的执行结果："+future.get());
    }
}
```

