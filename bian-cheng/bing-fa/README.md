# 并发

概念：共享、可变、同步、线程安全、无状态、竞争条件（Race Condition，交替执行时序）、Check-Then-Act、读取-修改-写入、原子、复合操作、同步代码块、可见性、锁对象、内置锁、监视锁、重入、活跃性。

## 1. 基础

* [synchronized](synchronized.md)
* [volatile](volatile.md)
* [AtomicLong](https://github.com/pzdn2009/java-ctrip/tree/cb6ae6ffdaa074a601054f4d94cd7f0a8fe36649/AtomicLong/README.md)

显示锁、原子变量、AtomicReference

## 2. 对象的共享

临界区、内存可见性、重排序ReOrdering、失效数据、发布publish、Escape、线程封闭、栈封闭、不可变对象、安全发布、安全发布的模式、事实不可变对象、

* [ThreadLocal](threadlocal.md)
* [final](final.md)
* [@Immutable、@ThreadSafe](xiang-guan-zhu-jie.md)
* [安全发布](an-quan-fa-bu.md)

## 3. 对象的组合、构建基础模块

线程安全的类、不变性条件、后验条件、先验条件、实例封闭、监视器模式、状态变量、Put-If-Absent、客户端加锁、组合、闭锁、信号量、互斥体、

* [CopyOnWriteArrayList](copyonwritearraylist.md)
* [Runnable](runnable.md)
* [ConcurrentHashMap](concurrenthashmap.md)
* [BlockingQueue](blockingqueue.md)
* [Thread](thread.md)
* [CountDownLatch](countdownlatch.md)
* [Callable](callable.md)
* [FutureTask](futuretask.md)
* static

## 4. 任务执行

Executor框架、线程池、任务、工作队列、工作线程、生命周期、延迟任务、周期任务、可取消的、中断、

* [ExecutorService\(Executors\)](executorservice.md)
* [ThreadPoolExecutor](threadpoolexecutor.md)
* [CompletionService](completionservice.md)

## 5. 显示锁

* [ReentrantLock](reentrantlock.md)
* [ReadWriteLock](readwritelock.md)

