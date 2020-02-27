# ThreadPoolExecutor

【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

线程池有两个线程数的设置，一个为**核心池线程数**，一个为**最大线程数**。

在创建了线程池后，默认情况下，线程池中并没有任何线程，等到有任务来才创建线程去执行任务，除非调用了prestartAllCoreThreads\(\)或者prestartCoreThread\(\)方法。

当创建的线程数等于 corePoolSize 时，会加入设置的阻塞队列。当队列满时，会创建线程执行任务直到线程池中的数量等于maximumPoolSize。

说明：Executors 各个方法的弊端：

1）newFixedThreadPool 和 newSingleThreadExecutor: 主要问题是堆积的**请求处理队列**可能会耗费非常大的内存，甚至 OOM\(Out Of Memory\)。

2）newCachedThreadPool 和 newScheduledThreadPool: 主要问题是线程数最大数是 Integer.MAX\_VALUE，可能会**创建数量非常多的线程**，甚至 OOM。

