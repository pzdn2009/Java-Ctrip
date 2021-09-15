# Deep in sychronized

## Object Header

> Object in JVM = **Object header** + instance variables + fill data
>
> **Object Header** : 2 word = Mark Word + Class Metadata Address

| 虚拟机位数 | 头对象结构 | 说明 |
| :--- | :--- | :--- |
| 32/64bit | Mark Word | 存储对象的hashCode、锁信息或分代年龄或GC标志等信息 |
| 32/64bit | Class Metadata Address | 类型指针指向对象的类元数据，JVM通过这个指针确定该对象是哪个类的实例。 |

32 Mark word: 

| 锁状态 | 25bit | 4bit | 1bit是否是偏向锁 | 2bit 锁标志位 |
| :--- | :--- | :--- | :--- | :--- |
| 无锁状态 | 对象HashCode | 对象分代年龄 | 0 | 01 |

![](../../../.gitbook/assets/image%20%2816%29.png)

其中，偏向锁和轻量级锁是 java6对重量级锁的优化新增的。

sychronized属于重量级锁，对应的标志位为10。30bit的指针指向对应的Monitor（管程）的起始地址。

## HotSpot ObjectMonitor

{% embed url="https://github.com/JetBrains/jdk8u\_hotspot/blob/master/src/share/vm/runtime/objectMonitor.hpp" %}

```cpp
class ObjectWaiter : public StackObj {
 public:
  enum TStates { TS_UNDEF, TS_READY, TS_RUN, TS_WAIT, TS_ENTER, TS_CXQ } ;
  enum Sorted  { PREPEND, APPEND, SORTED } ;
  ObjectWaiter * volatile _next;
  ObjectWaiter * volatile _prev;
  Thread*       _thread;
  jlong         _notifier_tid;
  ParkEvent *   _event;
  volatile int  _notified ;
  volatile TStates TState ;
  Sorted        _Sorted ;           // List placement disposition
  bool          _active ;           // Contention monitoring is enabled
 public:
  ObjectWaiter(Thread* thread);

  void wait_reenter_begin(ObjectMonitor *mon);
  void wait_reenter_end(ObjectMonitor *mon);
};

// initialize the monitor, exception the semaphore, all other fields
  // are simple integers or pointers
  ObjectMonitor() {
    _header       = NULL;
    _count        = 0;  // reference count to prevent reclaimation/deflation
                        // at stop-the-world time.  See deflate_idle_monitors().
                        // _count is approximately |_WaitSet| + |_EntryList|
    _waiters      = 0,  // number of waiting threads
    _recursions   = 0; //锁的重入次数
    _object       = NULL;
    _owner        = NULL; //指向持有锁的线程
    _WaitSet      = NULL; //处于wait状态的线程，加入到_WaitSet
    _WaitSetLock  = 0 ;
    _Responsible  = NULL ;
    _succ         = NULL ;
    _cxq          = NULL ;
    FreeNext      = NULL ;
    _EntryList    = NULL ; //处于等待锁block状态的线程，加入到_EntryList
    _SpinFreq     = 0 ;
    _SpinClock    = 0 ;
    OwnerIsThread = 0 ;
    _previous_owner_tid = 0;
  }
  

```

* \_EntryList:  // Threads blocked on entry or reentry.
* \_WaitSet:  // LL of threads wait\(\)ing on the monitor
* \_owner: // pointer to owning thread OR BasicLock

![](../../../.gitbook/assets/image%20%2815%29.png)

ObjectMonitor中有两个队列，_WaitSet 和 \_EntryList，用来保存ObjectWaiter对象列表\( 每个等待锁的线程都会被封装成ObjectWaiter对象\)，\_owner指向持有ObjectMonitor对象的线程，当多个线程同时访问一段同步代码时，首先会进入 \_EntryList 集合，当线程获取到对象的monitor 后进入 \_Owner 区域并把monitor中的owner变量设置为当前线程同时monitor中的计数器count加1，若线程调用 wait\(\) 方法，将释放当前持有的monitor，owner变量恢复为null，count自减1，同时该线程进入 WaitSe t集合中等待被唤醒。若当前线程执行完毕也将释放monitor\(锁\)并复位变量的值，以便其他线程进入获取monitor\(锁\)。_ 

## synchronized -- code block

javap -v XXX.

```cpp
  public void test2() {
    Class var1 = Synchronos.class;
    synchronized(Synchronos.class) {
      ++i;
    }
  }
  
  public void test2();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=3, args_size=1
         0: ldc           #3                  // class com/xxx/Synchronos
         2: dup
         3: astore_1
         4: monitorenter
         5: getstatic     #2                  // Field i:I
         8: iconst_1
         9: iadd
        10: putstatic     #2                  // Field i:I
        13: aload_1
        14: monitorexit
        15: goto          23
        18: astore_2
        19: aload_1
        20: monitorexit
        21: aload_2
        22: athrow
        23: return
      Exception table:
         from    to  target type
             5    15    18   any
            18    21    18   any
      LineNumberTable:
        line 12: 0
        line 13: 5
        line 14: 13
        line 15: 23
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      24     0  this   Lcom/xxx/Synchronos;
      StackMapTable: number_of_entries = 2
        frame_type = 255 /* full_frame */
          offset_delta = 18
          locals = [ class com/xxxx/Synchronos, class java/lang/Object ]
          stack = [ class java/lang/Throwable ]
        frame_type = 250 /* chop */
          offset_delta = 4

```

* monitorenter
* monitorexit

## synchronized -- method

```cpp
  public synchronized void test();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_SYNCHRONIZED
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #2                  // Field i:I
         3: iconst_1
         4: iadd
         5: putstatic     #2                  // Field i:I
         8: return
      LineNumberTable:
        line 8: 0
        line 9: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  this   Lcom/xxx/Synchronos;


```

* ACC\_PUBLIC：public 修饰符
* ACC\_SYNCHRONIZED：同步方法

## 锁优化

在JDK1.6之前，synchronized的实现直接调用ObjectMonitor的enter和exit，这种锁被称之为**「重量级锁」**。从JDK6开始，HotSpot虚拟机开发团队对Java中的锁进行优化，如增加了适应性自旋、锁消除、锁粗化、轻量级锁和偏向锁等优化策略。

锁的状态总共有四种，无锁状态、偏向锁、轻量级锁和重量级锁。随着锁的竞争，锁可以从偏向锁升级到轻量级锁，再升级的重量级锁，但是锁的升级是单向的，也就是说只能从低到高升级，不会出现锁的降级.

![](../../../.gitbook/assets/image%20%2818%29.png)

### 偏向锁 

偏向锁是Java 6之后加入的新锁，它是一种针对加锁操作的优化手段，经过研究发现，在大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，因此为了减少同一线程获取锁\(会涉及到一些CAS操作,耗时\)的代价而引入偏向锁。偏向锁的核心思想是，**如果一个线程获得了锁，那么锁就进入偏向模式**，此时Mark Word 的结构也变为偏向锁结构，当这个线程再次请求锁时，无需再做任何同步操作，即获取锁的过程，这样就省去了大量有关锁申请的操作，从而也就提供程序的性能。所以，对于没有锁竞争的场合，偏向锁有很好的优化效果，毕竟极有可能连续多次是同一个线程申请相同的锁。但是对于锁竞争比较激烈的场合，偏向锁就失效了，因为这样场合极有可能每次申请锁的线程都是不相同的，因此这种场合下不应该使用偏向锁，否则会得不偿失，需要注意的是，偏向锁失败后，并不会立即膨胀为重量级锁，而是先升级为轻量级锁。下面我们接着了解轻量级锁。

### 轻量级锁

倘若偏向锁失败，虚拟机并不会立即升级为重量级锁，它还会尝试使用一种称为轻量级锁的优化手段\(1.6之后加入的\)，此时Mark Word 的结构也变为轻量级锁的结构。轻量级锁能够提升程序性能的依据是“对绝大部分的锁，在整个同步周期内都不存在竞争”，注意这是经验数据。需要了解的是，轻量级锁所适应的场景是线程交替执行同步块的场合，如果存在同一时间访问同一锁的场合，就会导致轻量级锁膨胀为重量级锁。

### 自旋锁

轻量级锁失败后，虚拟机为了避免线程真实地在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。这是基于在大多数情况下，线程持有锁的时间都不会太长，如果直接挂起操作系统层面的线程可能会得不偿失，毕竟操作系统实现线程之间的切换时需要从用户态转换到核心态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高，因此自旋锁会假设在不久将来，当前的线程可以获得锁，因此虚拟机会让当前想要获取锁的线程做几个空循环\(这也是称为自旋的原因\)，一般不会太久，可能是50个循环或100循环，在经过若干次循环后，如果得到锁，就顺利进入临界区。如果还不能获得锁，那就会将线程在操作系统层面挂起，这就是自旋锁的优化方式，这种方式确实也是可以提升效率的。最后没办法也就只能升级为重量级锁了。

### 锁消除 

消除锁是虚拟机另外一种锁的优化，这种优化更彻底，Java虚拟机在JIT编译时\(可以简单理解为当某段代码即将第一次被执行时进行编译，又称即时编译\)，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁，通过这种方式消除没有必要的锁，可以节省毫无意义的请求锁时间，如下StringBuffer的append是一个同步方法，但是在add方法中的StringBuffer属于一个局部变量，并且不会被其他线程所使用，因此StringBuffer不可能存在共享资源竞争的情景，JVM会自动将其锁消除。

* 偏向锁：在无竞争的情况下，把整个同步都消除掉，CAS操作都不做。
* 轻量级锁：在没有多线程竞争时，相对重量级锁，减少操作系统互斥量带来的性能消耗。但是，如果存在锁竞争，除了互斥量本身开销，还额外有CAS操作的开销。
* 自旋锁：减少不必要的CPU上下文切换。在轻量级锁升级为重量级锁时，就使用了自旋加锁的方式
* 锁粗化：将多个连续的加锁、解锁操作连接在一起，扩展成一个范围更大的锁。
* 锁消除:虚拟机即时编译器在运行时，对一些代码上要求同步，但是被检测到不可能存在共享数据竞争的锁进行削除。

