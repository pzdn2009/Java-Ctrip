# CountDownLatch

A synchronization aid that allows one or more threads to wait until a set of operations **being performed** in other threads completes.

使用方式：

```java
private static CountDownLatch latch = new CountDownLatch(1);  

//...
      new Thread()  
        {  
            public void run()  
            {  
                //
                latch.countDown();  
            };  
        }.start();  

latch.await();
```

Latch闭锁的意思，是一种同步的工具类。

类似于一扇门：在闭锁到达结束状态之前，这扇门一直是关闭着的，不允许任何线程通过，当到达结束状态时，这扇门会打开并允许所有的线程通过。且当门打开了，就**永远保持打开状态**。

CountDowmLatch是一种灵活的闭锁实现，包含一个计数器，该计算器初始化为一个正数，表示需要等待事件的数量。

countDown方法递减计数器，表示有一个事件发生，而await方法等待计数器到达0，表示所有需要等待的事情都已经完成。

Ref:[https://blog.csdn.net/lmj623565791/article/details/26626391](https://blog.csdn.net/lmj623565791/article/details/26626391)

[http://www.importnew.com/15731.html](http://www.importnew.com/15731.html) 实现了使用CountDownLatch的一个闭锁服务健康检查例子。

