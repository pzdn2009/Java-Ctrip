# AQS souce code

## Node



* 链表结构.
* CLH锁是一个自旋锁，能确保无饥饿性，提供先来先服务的公平性。
* 包含：独占模式 & 共享模式



```java
//The wait queue is a variant of a "CLH" (Craig, Landin, and  Hagersten) lock queue.
static final class Node {
    // 标记一个结点（对应的线程）在共享模式下等待
    static final Node SHARED = new Node(); 

    // 标记一个结点（对应的线程）在独占模式下等待
    static final Node EXCLUSIVE = null; 

    // waitStatus的值，表示该结点（对应的线程）已被取消
    static final int CANCELLED = 1;
    // waitStatus的值，表示后继结点（对应的线程）需要被唤醒 
    static final int SIGNAL = -1; 
    // waitStatus的值，表示该结点（对应的线程）在等待某一条件
    static final int CONDITION = -2; 
    // waitStatus的值，表示有资源可用，新head结点需要继续唤醒后继结点（共享模式下，多线程并发释放资源，而head唤醒其后继结点后，需要把多出来的资源留给后面的结点；设置新的head结点时，会继续唤醒其后继结点）
    static final int PROPAGATE = -3; 

    // 等待状态，取值范围，-3，-2，-1，0，1
    volatile int waitStatus; 
    volatile Node prev; // 前驱结点
    volatile Node next; // 后继结点
    volatile Thread thread; // 结点对应的线程

    // 等待队列里下一个等待条件的结点
    Node nextWaiter; 

    final boolean isShared() {
        return nextWaiter == SHARED;
    }

    // 前驱结点
    final Node predecessor() throws NullPointerException { 
        Node p = prev;
        if (p == null)
            throw new NullPointerException();
        else
            return p;
    }

    Node() { // 初始化head或share标记结点
    }

    Node(Thread thread, Node mode) { // 同步队列
        this.nextWaiter = mode;
        this.thread = thread;
    }

    Node(Thread thread, int waitStatus) { // 等待队列
        this.waitStatus = waitStatus;
        this.thread = thread;
    }
}
```

## FairSync



```java
/**
 * Sync object for fair locks
 */
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        acquire(1);
    }

    /**
     * Fair version of tryAcquire.  Don't grant access unless
     * recursive call or no waiters or is first.
     */
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        //读取同步状态
        int c = getState();
        if (c == 0) { //未同步
            //如果不需要入队，则CAS修改同步状态
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                //设置独占拥有线程
                setExclusiveOwnerThread(current);
                //获取成功
                return true;
            }
        }
        //已同步，判断是否独占拥有线程
        else if (current == getExclusiveOwnerThread()) {
            //判断是否继续重入
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }

        return false;
    }
}
```

```java
//aqs
public final boolean hasQueuedPredecessors() {
    // The correctness of this depends on head being initialized
    // before tail and on head.next being accurate if the current
    // thread is first in queue.
    Node t = tail; // Read fields in reverse initialization order
    Node h = head;
    Node s;
    return h != t &&
        ((s = h.next) == null || s.thread != Thread.currentThread());
}
```

* 队头和队尾为空，还不存在，返回false；
* 队头不等与队尾，说明有节点存在，继续：

  * 如果只有一个节点，即头结点，需要入队，返回true；
  * 如果有多个节点，头节点的下一个节点，不是自己，需要入队，返回true。



## 独占锁核心源码分析

![](../../../.gitbook/assets/image%20%2821%29.png)

## 1. acquire—获取入口

```java
public final void acquire(int arg) {
    //获取锁成功，则返回
    //反之，加入队列，等待竞争
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

### 1.1 tryAcquire——尝试获取

```java
/**
 * Fair version of tryAcquire.  Don't grant access unless
 * recursive call or no waiters or is first.
 */
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {//未同步时，
        //如果不需要入队
        if (!hasQueuedPredecessors() &&
            compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    //同步状态被占用时，判断是否可重入
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

### 1.2 addWaiter——生成节点

```java
/**
 * Creates and enqueues node for current thread and given mode.
 *
 * @param mode Node.EXCLUSIVE for exclusive, Node.SHARED for shared
 * @return the new node
 */
private Node addWaiter(Node mode) {

    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure

    //尾指针
    Node pred = tail;
    if (pred != null) {
        //新节点的前驱
        node.prev = pred;
        //CAS更新尾指针（注，这个地方不一定会成功）
        if (compareAndSetTail(pred, node)) {
            //连接后继
            pred.next = node;
            return node;
        }
    }

    //尾指针为空，或者CAS失败--》则创建等待队列
    enq(node);

    return node;
}
```

### 1.3. enq——节点入队尾

* spin
* 第一次创建链表头结点
* 第二次更新节点。

```java
/**
 * Inserts node into queue, initializing if necessary. See picture above.
 * @param node the node to insert
 * @return node's predecessor
 */
private Node enq(final Node node) {
    for (;;) { //自旋
        Node t = tail;
        if (t == null) { // Must initialize
            // 新建一个头结点，头指针和尾指针一样
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            node.prev = t;
            //设置尾指针，CAS成功，则返回t
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

### 1.4 acquireQueued——等待队列中自旋获取

```java
/**
 * Acquires in exclusive uninterruptible mode for thread already in
 * queue. Used by condition wait methods as well as acquire.独占非中断模式获取锁，针对已经在等待队列里面的节点。
 *
 * @param node the node
 * @param arg the acquire argument
 * @return {@code true} if interrupted while waiting
 */
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) { //自旋
            //获取前驱
            final Node p = node.predecessor();
            //如果是前驱是头结点，说明node要做好准备了，尝试获取
            if (p == head && tryAcquire(arg)) {
                //成功：设置当前节点为头结点
                setHead(node);
                //释放直接的头结点
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            //判断是否要park，park函数是将当前调用Thread阻塞，
            //而unpark函数则是将指定线程Thread唤醒。即让出CPU。

            //park检查和中断检查
            //如果可以的话，则进行Park，并更新中断标记
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        //如果失败，则取消获取
        if (failed)
            cancelAcquire(node);
    }
}
```

### 1.5 shouldParkAfterFailedAcquire——获取不到是否应该park

```java
/**
 * Checks and updates status for a node that failed to acquire.
 * Returns true if thread should block. This is the main signal
 * control in all acquire loops.  Requires that pred == node.prev.
 *
 * @param pred node's predecessor holding status
 * @param node the node
 * @return {@code true} if thread should block
 */
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    //前驱等待状态
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)
        /*
         * This node has already set status asking a release
         * to signal it, so it can safely park.
         */
        //所谓安全停靠点，指的是，当前结点的前驱结点waitStatus = -1(SIGNAL)，下一个就是它了。
        return true;
    if (ws > 0) {
        //跳过所有已取消的前驱节点
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        /*
         * waitStatus must be 0 or PROPAGATE.  Indicate that we
         * need a signal, but don't park yet.  Caller will need to
         * retry to make sure it cannot acquire before parking.
         */
         //如果前节点不处于取消状态，则设为signal -1
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
```

### 1.6 parkAndCheckInterrupt

```java
private final boolean parkAndCheckInterrupt() {
        LockSupport.park(this);//阻塞该线程，至此该线程进入等待状态，等着unpark和interrupt叫醒他
        return Thread.interrupted();//叫醒之后返回该线程是否在中断状态， 并会清除中断记号。

    }
```

### 1.7 cancelAcquire——取消获取

```java
private void cancelAcquire(Node node) {

    if (node == null)
        return;

    //擦除线程
    node.thread = null;

    // Skip cancelled predecessors
    //跳过已取消的前驱
    Node pred = node.prev;
    while (pred.waitStatus > 0)
        node.prev = pred = pred.prev;

    // predNext is the apparent node to unsplice. CASes below will
    // fail if not, in which case, we lost race vs another cancel
    // or signal, so no further action is necessary.
    Node predNext = pred.next;

    //取消
    node.waitStatus = Node.CANCELLED;

    //最后一个节点，设置队尾
    if (node == tail && compareAndSetTail(node, pred)) {
        compareAndSetNext(pred, predNext, null);
    } else {
        // If successor needs signal, try to set pred's next-link
        // 如果后继需要唤醒，尝试连接
        // so it will get one. Otherwise wake it up to propagate.
        int ws;
        //前驱不是头结点 && 前驱已经SIGNAL，或者即将是
        if (pred != head &&
            ((ws = pred.waitStatus) == Node.SIGNAL ||
             (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) &&
            pred.thread != null) {
            Node next = node.next;
            if (next != null && next.waitStatus <= 0)
                compareAndSetNext(pred, predNext, next); //连接起来
        } else {
            //唤醒后继
            unparkSuccessor(node);
        }

        node.next = node; // help GC
    }
}
```

### 2.7 unparkSuccessor——唤醒后继

```java
/**
 * Wakes up node's successor, if one exists.
 *
 * @param node the node
 */
private void unparkSuccessor(Node node) {
    /*
     * If status is negative (i.e., possibly needing signal) try
     * to clear in anticipation of signalling.  It is OK if this
     * fails or if status is changed by waiting thread.
     */
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);

    /*
     * Thread to unpark is held in successor, which is normally
     * just the next node.  But if cancelled or apparently null,
     * traverse backwards from tail to find the actual
     * non-cancelled successor.
     */
    Node s = node.next;
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    //从后面开始找到非取消的后继，
    //unPark这个后继
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

### 2.8 release——释放锁

```java
//AQS的release
public final boolean release(int arg) {
        if (tryRelease(arg)) {
            Node h = head;
            if (h != null && h.waitStatus != 0)
                unparkSuccessor(h);//唤醒后继节点
            return true;
        }
        return false;
 }

//c=0,才完全
protected final boolean tryRelease(int releases) {
    //同步状态
    int c = getState() - releases;

    //当前线程要一致
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {
        free = true;
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}
```

## 共享锁AQS核心源码解析



## acquireShared——获取共享锁

```java
public final void acquireShared(int arg) {
        if (tryAcquireShared(arg) < 0)
            doAcquireShared(arg);
    }
```

## tryAcquireShared——尝试获取共享锁

```java
protected final int tryAcquireShared(int unused) {
    /*
     * Walkthrough:
     * 1. If write lock held by another thread, fail.
     * 2. Otherwise, this thread is eligible for
     *    lock wrt state, so ask if it should block
     *    because of queue policy. If not, try
     *    to grant by CASing state and updating count.
     *    Note that step does not check for reentrant
     *    acquires, which is postponed to full version
     *    to avoid having to check hold count in
     *    the more typical non-reentrant case.
     * 3. If step 2 fails either because thread
     *    apparently not eligible or CAS fails or count
     *    saturated, chain to version with full retry loop.
     */
    Thread current = Thread.currentThread();
    int c = getState();
    //写锁被别的线程占有，直接失败
    if (exclusiveCount(c) != 0 &&
        getExclusiveOwnerThread() != current)
        return -1;

    //读锁的数量
    int r = sharedCount(c);
    //不需要阻塞读
    if (!readerShouldBlock() &&
        r < MAX_COUNT &&
        compareAndSetState(c, c + SHARED_UNIT)) {

        //
        if (r == 0) {
            firstReader = current;
            firstReaderHoldCount = 1;
        } else if (firstReader == current) {
            firstReaderHoldCount++;
        } else {
            HoldCounter rh = cachedHoldCounter;
            if (rh == null || rh.tid != getThreadId(current))
                cachedHoldCounter = rh = readHolds.get();
            else if (rh.count == 0)
                readHolds.set(rh);
            rh.count++;
        }
        return 1;
    }
    return fullTryAcquireShared(current);
}
```

## fullTryAcquireShared

## doAcquireShared——入队

```java
private void doAcquireShared(int arg) {
        //将当前节点加入到等待队列中去并且设为共享模式
        final Node node = addWaiter(Node.SHARED);
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                final Node p = node.predecessor();  //获取当前节点的前驱节点
                if (p == head) {    //如果前驱结点是首节点
                    int r = tryAcquireShared(arg);  //尝试获取资源
                    if (r >= 0) {   //获取资源成功
                        //设置node节点为首节点
                        setHeadAndPropagate(node, r);//源码解析p2-1
                        p.next = null; // help GC
                        if (interrupted)    //如果等待过程有打断此时补上
                            selfInterrupt();
                        failed = false;
                        return;
                    }
                }
                //shouldParkAfterFailedAcquire(p, node)方法清除当前节点之前waitStatus>0的节点(已经标注被取消的节点)。。。
                //并返回false，返回false意味着当前节点无法被挂起，继续进行自旋操作直到可以被挂起或者成为首节点
                //如果返回true，则直接被挂起
                //parkAndCheckInterrupt方法的作用是挂起当前线程，此时从自旋中退出
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())    //继续挂起
                    interrupted = true;
            }
        } finally {
            if (failed) //如果循环获取失败则取消排队
                cancelAcquire(node);//取消正在进行的获取
        }
    }
```

当前节点的前驱节点是头结点，尝试获取同步资源并唤醒被挂起的node节点

一个是当当前节点的前驱节点不是头结点，则清除无效的前驱节点并尝试挂起当前线程

## setHeadAndPropagate

```java
private void setHeadAndPropagate(Node node, int propagate) {
        Node h = head; // Record old head for check below
        setHead(node);//设置node为头节点

        //这里需要特别注意，h是node成为首节点之前的首节点，当h的状态为PRPPAGATE的时候可以进行唤起操作，但是
        //在唤起操作中取的是node节点，并不是这个h了
        if (propagate > 0 || h == null || h.waitStatus < 0 ||
                (h = head) == null || h.waitStatus < 0) {   //如果可以继续传播
            Node s = node.next; //获取node节点的下一个节点
            if (s == null || s.isShared())  //如果s是空或者是共享模式
                doReleaseShared();  //P3
        }
    }
```

## doReleaseShared

```java
private void doReleaseShared() {
        for (;;) {
            Node h = head;
            if (h != null && h != tail) {   //当前节点不为空且不为尾节点(表示不止一个节点)
                int ws = h.waitStatus;  //获取首节点的状态
                if (ws == Node.SIGNAL) {    //如果头节点可以唤起后继结点
                    if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0)) //将h节点的状态更改为0，然后唤醒h节点
                        continue;            // 循环复查
                    unparkSuccessor(h); //P3-1:唤醒当前节点的下一个可用节点
                }
                //将waitStatus设置为可传播
                else if (ws == 0 &&
                        !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                    continue;                // loop on failed CAS
            }
            if (h == head)                   // loop if head changed
                break;
        }
    }
```

