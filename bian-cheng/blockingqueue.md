# BlockingQueue

BlockingQueue即阻塞队列。

隊列滿，隊列空都會被阻塞。

| - | Throws Exception | Special Value | Blocks | Times Out |
| :--- | :--- | :--- | :--- | :--- |
| Insert | add\(o\) | offer\(o\) | put\(o\) | offer\(o, timeout, timeunit\) |
| Remove | remove\(o\) | poll\(\) | take\(\) | poll\(timeout, timeunit\) |
| Examine | element\(\) | peek\(\) |  |  |

特點：

1. ThrowsException：如果操作不能马上进行，则抛出异常
2. SpecialValue：如果操作不能马上进行，将会返回一个特殊的值，一般是true或者false
3. Blocks:如果操作不能马上进行，操作会被阻塞
4. TimesOut:如果操作不能马上进行，操作会被阻塞指定的时间，如果指定时间没执行，则返回一个特殊值，一般是true或者false

需要注意的是，我们不能向BlockingQueue中插入null，否则会报NullPointerException。

實現類：

1. ArrayBlockingQueue
2. DelayQueue
3. LinkedBlockingQueue
4. PriorityBlockingQueue
5. SynchronousQueue

## SynchronousQueue

Ref:[http://shift-alt-ctrl.iteye.com/blog/1840385](http://shift-alt-ctrl.iteye.com/blog/1840385)

SynchronousQueue:同步Queue,属于线程安全的BlockingQueue的一种,此队列设计的理念类似于"单工模式",对于每个put/offer操作,必须等待一个take/poll操作,类似于我们的现实生活中的"火把传递":一个火把传递地他人,需要2个人"触手可及"才行. 因为这种策略,最终导致队列中并没有一个真正的元素;这是一种pipleline思路的基于queue的**"操作传递"**.

* void put\(E o\):向队列提交一个元素,阻塞直到其他线程take或者poll此元素.
* boolean offer\(E o\):向队列中提交一个元素,如果此时有其他线程正在被take阻塞\(即其他线程已准备接收\)或者"碰巧"有poll操作,那么将返回true,否则返回false.
* E take\(\):获取并删除一个元素,阻塞直到有其他线程offer/put.
* boolean poll\(\):获取并删除一个元素,如果此时有其他线程正在被put阻塞\(即其他线程提交元素正等待被接收\)或者"碰巧"有offer操作,那么将返回true,否则返回false.
* E peek\(\):总会返回null,硬编码.

这个队列中,对我们有意义的操作时**put/take**,以及**put/offer + take**或者**put/take + poll**,对于无法进入队列的元素,需要有额外的"拒绝"策略支持.

SynchronousQueue经常用来,一端或者双端严格遵守"单工"\(**单工作者**\)模式的场景,队列的两个操作端分别是productor和consumer。

常用于一个productor多个consumer的场景。

在ThreadPoolExecutor中,通过Executors创建的cachedThreadPool就是使用此类型队列.已确保,如果现有线程无法接收任务\(offer失败\),将会创建新的线程来执行。

