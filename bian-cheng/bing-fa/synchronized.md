# synchronized

在Java 中，关键字 synchronized可以保证在**同一个时刻**，**只有一个线程**可以执行**某个方法**或者**某个代码块**\(主要是对方法或者代码块中存在共享数据的操作\)，同时我们还应该注意到synchronized另外一个重要的作用，synchronized可保证一个线程的变化\(主要是共享数据的变化\)被其他线程所看到（**保证可见性**，完全可以替代Volatile功能，即刷新到主存）。

## 1. 使用方式

### 1.1 修饰实例方法

确保是同一个instance，否则没有意义。

```java
public class AccountingSync implements Runnable{
    //共享资源(临界资源)
    static int i=0;

    /**
     * synchronized 修饰实例方法。
     * 注：如果是不同实例，则此方法失去意义
     */
    public synchronized void increase(){
        i++;
    }
    @Override
    public void run() {
        for(int j=0;j<1000000;j++){
            increase();
        }
    }
    public static void main(String[] args) throws InterruptedException {
        AccountingSync instance=new AccountingSync();
        Thread t1=new Thread(instance);
        Thread t2=new Thread(instance);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
    /**
     * 输出结果:
     * 2000000
     */
}
```

* i++;操作并**不具备原子性**，该操作是先读取值，然后写回一个新值，相当于原来的值加上1，分两步完成。

### 1.2 静态方法

由于静态成员不专属于任何一个实例对象，是类成员，因此通过class对象锁可以控制静态 成员的并发操作。

```java
public static synchronized void increase(){
        i++;
    }
```

### 1.3 同步代码块

```java
public class AccountingSync implements Runnable{
    static AccountingSync instance=new AccountingSync();
    static int i=0;
    @Override
    public void run() {
        //省略其他耗时操作....
        //使用同步代码块对变量i进行同步操作,锁对象为instance
        synchronized(instance){
            for(int j=0;j<1000000;j++){
                    i++;
              }
        }
    }
    public static void main(String[] args) throws InterruptedException {
        Thread t1=new Thread(instance);
        Thread t2=new Thread(instance);
        t1.start();t2.start();
        t1.join();t2.join();
        System.out.println(i);
    }
}
```

另外两种方式：

```java
//this,当前实例对象锁
synchronized(this){
    for(int j=0;j<1000000;j++){
        i++;
    }
}

//class对象锁
synchronized(AccountingSync.class){
    for(int j=0;j<1000000;j++){
        i++;
    }
}
```

## 2. 底层

Ref：[https://blog.csdn.net/javazejian/article/details/72828483](https://blog.csdn.net/javazejian/article/details/72828483)

1. monitorenter，monitorexit；
2. flags: ACC\_PUBLIC, ACC\_SYNCHRONIZED

## 3. 关键点

1. synchronized可重入；
2. 线程中断不影响同步。

