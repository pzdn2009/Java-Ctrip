# BlockingQueue

Ref：[https://blog.csdn.net/smuedward/article/details/54574938](https://blog.csdn.net/smuedward/article/details/54574938)

BlockingQueue即阻塞队列。阻塞时机：隊列滿（针对进），隊列空（针对出）都會被阻塞。

典型的生产者-消费者。

## 1. 操作列表

| - | Throws Exception | Special Value | Blocks | Times Out |
| :--- | :--- | :--- | :--- | :--- |
| Insert | add\(o\) | offer\(o\) | put\(o\) | offer\(o, timeout, timeunit\) |
| Remove | remove\(o\) | poll\(\) | take\(\) | poll\(timeout, timeunit\) |
| Examine | element\(\) | peek\(\) |  |  |

PS： 1. ThrowsException：如果操作不能马上进行，则抛出异常 2. SpecialValue：如果操作不能马上进行，将会返回一个特殊的值，一般是true或者false 3. Blocks:如果操作不能马上进行，操作会被阻塞 4. TimesOut:如果操作不能马上进行，操作会被阻塞指定的时间，如果指定时间没执行，则返回一个特殊值，一般是true或者false 5. 需要注意的是，我们不能向BlockingQueue中插入null，否则会报NullPointerException。 6. **有界队列**是构建高可靠应用的手段。

## 2. BlockingQueue的实现类

1. ArrayBlockingQueue。类似ArrayList，具有良好的并发性。
2. DelayQueue。
3. LinkedBlockingQueue。链式。
4. PriorityBlockingQueue。优先队列，支持自定义比较器。
5. SynchronousQueue。维护一组线程的队列

## 3. 源码

### 3.1 put & offer

```java
/**
     * Inserts the specified element at the tail of this queue, waiting
     * for space to become available if the queue is full.
     *
     * @throws InterruptedException {@inheritDoc}
     * @throws NullPointerException {@inheritDoc}
     */
    public void put(E e) throws InterruptedException {
        checkNotNull(e);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length)
                notFull.await();
            enqueue(e);
        } finally {
            lock.unlock();
        }
    }

    /**
     * Inserts the specified element at the tail of this queue, waiting
     * up to the specified wait time for space to become available if
     * the queue is full.
     *
     * @throws InterruptedException {@inheritDoc}
     * @throws NullPointerException {@inheritDoc}
     */
    public boolean offer(E e, long timeout, TimeUnit unit)
        throws InterruptedException {

        checkNotNull(e);
        long nanos = unit.toNanos(timeout);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length) {
                if (nanos <= 0)
                    return false;
                nanos = notFull.awaitNanos(nanos);
            }
            enqueue(e);
            return true;
        } finally {
            lock.unlock();
        }
    }
```

通过notFull来阻塞。

### 3.2 take & poll

```java
public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0)
                notEmpty.await();
            return dequeue();
        } finally {
            lock.unlock();
        }
    }

    public E poll(long timeout, TimeUnit unit) throws InterruptedException {
        long nanos = unit.toNanos(timeout);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0) {
                if (nanos <= 0)
                    return null;
                nanos = notEmpty.awaitNanos(nanos);
            }
            return dequeue();
        } finally {
            lock.unlock();
        }
    }
```

## 4. SynchronousQueue

Ref:[http://shift-alt-ctrl.iteye.com/blog/1840385](http://shift-alt-ctrl.iteye.com/blog/1840385)

SynchronousQueue:同步Queue,属于线程安全的BlockingQueue的一种,此队列设计的理念类似于"单工模式",对于每个put/offer操作,必须等待一个take/poll操作,类似于我们的现实生活中的"火把传递":一个火把传递地他人,需要2个人"触手可及"才行. 因为这种策略,最终导致队列中并没有一个真正的元素;这是一种pipleline思路的基于queue的**"操作传递"**.

* void put\(E o\):向队列提交一个元素,阻塞直到其他线程take或者poll此元素.
* boolean offer\(E o\):向队列中提交一个元素,如果此时有其他线程正在被take阻塞\(即其他线程已准备接收\)或者"碰巧"有poll操作,那么将返回true,否则返回false.
* E take\(\):获取并删除一个元素,阻塞直到有其他线程offer/put.
* boolean poll\(\):获取并删除一个元素,如果此时有其他线程正在被put阻塞\(即其他线程提交元素正等待被接收\)或者"碰巧"有offer操作,那么将返回true,否则返回false.
* E peek\(\):总会返回null,硬编码.

这个队列中,对我们有意义的操作时**put/take**,以及**put/offer + take**或者**put/take + poll**,对于无法进入队列的元素,需要有额外的"拒绝"策略支持.

SynchronousQueue经常用来,一端或者双端严格遵守"单工"\(**单工作者**\)模式的场景,队列的两个操作端分别是productor和consumer。

常用于一个productor多个consumer的场景。每次只有一个消费者获取一条数据。

Executors.newCachedThreadPool\(\)就使用了SynchronousQueue，这个线程池根据需要（新任务到来时）创建新的线程，如果有空闲线程则会重复使用，线程空闲了60秒后会被回收。如果现有线程无法接收任务\(offer失败\),将会创建新的线程来执行。

SynchronousQueue 队列中最多只有一个元素。

