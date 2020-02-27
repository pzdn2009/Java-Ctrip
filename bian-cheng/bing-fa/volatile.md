# volatile

Ref：[http://www.importnew.com/18126.html](http://www.importnew.com/18126.html)

## 1. Point

1. 内存模型：主存（物理内存）、高速缓存、CPU、共享变量（在其他CPU中也存在该变量的副本）、缓存一致性协议MESI。
2. MESI：保证了每个缓存中使用的共享变量的副本是一致的。它核心的思想是：当CPU写数据时，如果发现操作的变量是共享变量，即在其他CPU中也存在该变量的副本，会发出信号通知其他CPU将该变量的缓存行置为无效状态，因此当其他CPU需要读取这个变量时，发现自己缓存中缓存该变量的缓存行是无效的，那么它就会从内存重新读取。
3. 并发概念：原子性、可见性、有序性
4. 可见性是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。
5. 指令重排序（Instruction Reorder），
6. Java内存模型（Java Memory Model，JMM）。happens-before原则
7. 当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。
8. 通过synchronized和Lock也能够保证可见性，synchronized和Lock能保证同一时刻只有一个线程获取锁然后执行同步代码，并且在释放锁之前会将对变量的修改刷新到主存当中。因此可以保证可见性。

## 2. happens-before原则

* 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作
* 锁定规则：一个unLock操作先行发生于后面对同一个锁额lock操作
* volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作
* 传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C
* 线程启动规则：Thread对象的start\(\)方法先行发生于此线程的每个一个动作
* 线程中断规则：对线程interrupt\(\)方法的调用先行发生于被中断线程的代码检测到中断事件的发生
* 线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join\(\)方法结束、Thread.isAlive\(\)的返回值手段检测到线程已经终止执行
* 对象终结规则：一个对象的初始化完成先行发生于他的finalize\(\)方法的开始

## 3. volatile 深入

一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：

1. 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
2. 禁止进行指令重排序。

```java
//x、y为非volatile变量
//flag为volatile变量

x = 2;        //语句1
y = 0;        //语句2
flag = true;  //语句3
x = 4;         //语句4
y = -1;       //语句5
```

flag 一定处于第三句这个拓扑位置。

increase并不能保证原子性：

```java
public class Test {
    public volatile int inc = 0;

    public void increase() {
        inc++; //读取、缓存中操作、写入，多线程情况下会发生阻塞
    }
}
```

## 4. 使用场景

通常来说，使用volatile必须具备以下2个条件： 1. 对变量的写操作不依赖于当前值 2. 该变量没有包含在具有其他变量的不变式中

实际上，这些条件表明，可以被写入 volatile 变量的这些有效值独立于任何程序的状态，包括变量的当前状态。

事实上，我的理解就是上面的2个条件需要**保证操作是原子性操作**，才能保证使用volatile关键字的程序在并发时能够正确执行。

下面列举几个Java中使用volatile的几个场景。

### 4.1 状态标记

eg1:

```java
volatile boolean flag = false;

while(!flag){
    doSomething();
}

public void setFlag() {
    flag = true;
}
```

eg2:

```java
volatile boolean inited = false;
//线程1:
context = loadContext();  
inited = true; //原子性操作：读取或赋值     

//线程2:
while(!inited ){
sleep()
}
doSomethingwithconfig(context);
```

### 4.2 Double check

```java
class Singleton{
    private volatile static Singleton instance = null;

    private Singleton() {

    }

    public static Singleton getInstance() {
        if(instance==null) { //多个线程可能读取到都是空的，然后都去创建
            synchronized (Singleton.class) {
                if(instance==null)
                    instance = new Singleton();
            }
        }
        return instance;
    }
}
```

