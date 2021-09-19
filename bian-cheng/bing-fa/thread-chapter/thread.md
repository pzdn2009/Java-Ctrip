# Thread

## 线程中断

外部对目标线程发起一个协作式中断信号，目标线程可以处理这个信号，也可以忽略，当读取这个信号的时候，会清除中断标记。

```java
//1. 发送中断请求信号
t.interrupt();

//2. 获取中断信号并清除
t.isInterrupted();

//3. 处理中断异常
InterruptedException

if (Thread.interrupted())  // Clears interrupted status!
    throw new InterruptedException();
```



PS: 

1. 调用join\(\)和sleep\(\)方法，sleep\(\)时间结束或被打断，join\(\)中断,IO完成都会回到Runnable状态，等待JVM的调度。 

2. 调用wait\(\)，使该线程处于等待池\(wait blocked pool\),直到notify\(\)/notifyAll\(\)，线程被唤醒被放到锁定池\(lock blocked pool \)，释放同步锁使线程回到可运行状态（Runnable） 

3. 对Running状态的线程加同步锁\(Synchronized\)使其进入\(lock blocked pool \),同步锁被释放进入可运行状态\(Runnable\)。

## 每个对象都有的方法

![](http://incdn1.b0.upaiyun.com/2016/08/3ff01545fb674bcdeb7f4d616f85f8f4.png)

monitor

他们是应用于同步问题的人工线程调度工具。讲其本质，首先就要明确monitor的概念，Java中的每个对象都有一个监视器，来监测并发代码的重入。在非多线程编码时该监视器不发挥作用，反之如果在synchronized 范围内，监视器发挥作用。

wait/notify必须存在于synchronized块中。并且，这三个关键字针对的是同一个监视器（某对象的监视器）。这意味着wait之后，其他线程可以进入同步块执行。

synchronized, wait, notify结合:典型场景生产者消费者问题

```java
/**
   * 生产者生产出来的产品交给店员
   */
  public synchronized void produce()
  {
      if(this.product >= MAX_PRODUCT)
      {
          try
          {
              wait();  
              System.out.println("产品已满,请稍候再生产");
          }
          catch(InterruptedException e)
          {
              e.printStackTrace();
          }
          return;
      }

      this.product++;
      System.out.println("生产者生产第" + this.product + "个产品.");
      notifyAll();   //通知等待区的消费者可以取出产品了
  }

  /**
   * 消费者从店员取产品
   */
  public synchronized void consume()
  {
      if(this.product <= MIN_PRODUCT)
      {
          try
          {
              wait(); 
              System.out.println("缺货,稍候再取");
          } 
          catch (InterruptedException e) 
          {
              e.printStackTrace();
          }
          return;
      }

      System.out.println("消费者取走了第" + this.product + "个产品.");
      this.product--;
      notifyAll();   //通知等待去的生产者可以生产产品了
  }
```

## 创建一个线程的两种方法

* 继承Thread，并实现run，即可调用start方法
* 实现Runnable接口，传递给Thread构造器

## 实践

1. 指定线程名, Thread.name；
2. Thread.currentThread\(\);
3. Thread.sleep\(\)  
4. 捕获线程异常：thread.setUncaughtExceptionHandler
5. Thread.yield\(\) //当前线程可转让cpu控制权，让别的就绪状态线程运行（切换），例如for迭代时可以使用
6. Thread.join\(\) //等待该线程终止

