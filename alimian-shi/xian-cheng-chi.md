# 线程池

* 为什么需要线程池？线程池的作用？
* 几个比较重要的类？
* new Thread的弊端？
* 线程池的核心参数？
* 四种线程池以及特点，哪些会发生OOM？
* 线程池的四种拒绝策略？
* 阿里规范怎么建议创建线程池？为什么？
* 线程池的图示？
* 初始化线程池时线程数怎么选择？
* 线程池都有哪几种工作队列？

## 线程池的作用

创建线程和销毁线程的资源损耗。因为线程其实也是一个对象，创建一个对象，需要经过类加载过程，销毁一个对象，需要走GC垃圾回收流程，都是需要资源开销的。

提高响应速度。

重复利用。

## 比较重要的类

* Executor，任务接口
* ExecutorService，线程池接口，带生命周期管理
* ThreadPoolExecutor，线程池默认实现
* ScheduledThreadPoolExecutor，带任务计划的线程池

## new Thread的弊端

1.每次new Thread新建对象性能差。 2.线程缺乏统一管理，可能无限制新建线程，相互之间竞争，及可能占用过多系统资源导致死机或oom。 3.缺乏更多功能，如定时执行、定期执行、线程中断。 相比new Thread，Java提供的四种线程池的好处在于： 1.重用存在的线程，减少对象创建、消亡的开销，性能佳。 2.可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞。 3.提供定时执行、定期执行、单线程、并发数控制等功能。

## 线程池的核心参数

* corePoolSize，保持在pool内的线程数，尽管是空闲的，除非设置了allowCoreThreadTimeOut。
* maxPoolSize，池内最大的线程数。
* keepAliveTime，如果max&gt;=core，等待任务的最大时间。终止前多余的空闲线程等待新任务的最长时间
* unit，时间的单位
* workQueue，存储task。
* threadFactory，线程工厂。默认实现：DefaultThreadFactory。用于创建线程
* handler，拒绝策略。

## 线程池的图示

![](http://p3.pstatp.com/large/pgc-image/7e371e7d64cf4bc9b28e10f48b2353c3)

![](http://p1.pstatp.com/large/pgc-image/8d424ce2e44b4adcbbc35840e926be1d)

当产品提个需求，正式员工（核心线程）先接需求（执行任务） 如果正式员工都有需求在做，即核心线程数已满），产品就把需求先放需求池（阻塞队列）。 如果需求池\(阻塞队列\)也满了，但是这时候产品继续提需求,怎么办呢？那就请外包（非核心线程）来做。 如果所有员工（最大线程数也满了）都有需求在做了，那就执行拒绝策略。 如果外包员工把需求做完了，它经过一段（keepAliveTime）空闲时间，就离开公司了。

## 四种线程池以及特点，哪些会发生OOM

* newSingleThreadExecutor。会发生OOM。

  创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

  ```java
  public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
  ```

* newFixedThreadPool。会发生OOM。创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。

  线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

  ```java
  public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
  ```

* newCachedThreadPool。创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。 此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。
* newScheduledThreadPool。创建一个定长线程池，支持定时及周期性任务执行。

```java
public static ScheduledExecutorService newScheduledThreadPool(
            int corePoolSize, ThreadFactory threadFactory) {
        return new ScheduledThreadPoolExecutor(corePoolSize, threadFactory);
    }

public ScheduledThreadPoolExecutor(int corePoolSize,
                                       ThreadFactory threadFactory) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue(), threadFactory);
    }
```

## 线程池的四种拒绝策略

当请求任务不断的过来，而系统此时又处理不过来的时候，我们需要采取的策略是拒绝服务。RejectedExecutionHandler接口提供了拒绝任务处理的自定义方法的机会。在ThreadPoolExecutor中已经包含四种处理策略。

* AbortPolicy策略：该策略会直接抛出异常，阻止系统正常工作。
* CallerRunsPolicy 策略：只要线程池未关闭，该策略直接在调用者线程中，运行当前的被丢弃的任务。
* DiscardOleddestPolicy策略： 该策略将丢弃最老的一个请求，也就是即将被执行的任务，并尝试再次提交当前任务。
* DiscardPolicy策略：该策略默默的丢弃无法处理的任务，不予任何处理。

## 线程池都有哪几种工作队列

1、ArrayBlockingQueue

是一个基于数组结构的有界阻塞队列，此队列按 FIFO（先进先出）原则对元素进行排序。

2、LinkedBlockingQueue

一个基于链表结构的阻塞队列，此队列按FIFO （先进先出） 排序元素，吞吐量通常要高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool\(\)使用了这个队列

3、SynchronousQueue

一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQueue，静态工厂方法Executors.newCachedThreadPool使用了这个队列。

4、PriorityBlockingQueue

一个具有优先级的无限阻塞队列。

## 初始化线程池时线程数的选择

如果任务是IO密集型，一般线程数需要设置2倍CPU数以上，以此来尽量利用CPU资源。

如果任务是CPU密集型，一般线程数量只需要设置CPU数加1即可，更多的线程数也只能增加上下文切换，不能增加CPU利用率。

Ref: [https://blog.csdn.net/qq\_39662660/article/details/95983382](https://blog.csdn.net/qq_39662660/article/details/95983382)

