# Synchronization

在Java 中，关键字 synchronized可以保证在**同一个时刻**，**只有一个线程**可以执行**某个方法**或者**某个代码块**\(主要是对方法或者代码块中存在共享数据的操作\)，同时我们还应该注意到synchronized另外一个重要的作用，synchronized可保证一个线程的变化\(主要是共享数据的变化\)被其他线程所看到（**保证可见性**，完全可以替代Volatile功能，即刷新到主存）。



Ref：[https://blog.csdn.net/javazejian/article/details/72828483](https://blog.csdn.net/javazejian/article/details/72828483) \[深入理解Java并发之synchronized实现原理\]



## 3. 关键点

1. synchronized可重入；
2. 线程中断不影响同步。

