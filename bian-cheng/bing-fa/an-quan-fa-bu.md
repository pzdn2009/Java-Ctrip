# 安全发布

## Escape

```java
class Unsafepublish {
       private String[] states={"AK","AL"};
       public String[] getStates(){ 
           return states;
}
```

内部类的实例包含了对封装实例隐含的引用，这样在对象没有被正确构造之前，就会被发布，有可能会有不安全因素：

```java
public class Escape{
    private int thisCanBeEscape = 0;
    public Escape(){
        new InnerClass();
    }

    private  class InnerClass {
        public InnerClass() {
      //这里可以在Escape对象完成构造前提前引用到Escape的private变量          System.out.println(Escape.this.thisCanBeEscape);
   }
}

    public static void main(String[] args) {
        new Escape();
    }
}
```

一个导致this引用在构造期间逸出的错误，是在构造函数中启动一个线程，无论是隐式启动线程，还是显式启动线程，都会造成this引用逸出，新线程总会在所属对象构造完毕前看到它。

所以如果要在构造函数中创建线程，那么不要启动它，而应该采用一个专有的start或initialize方法来统一启动线程。

我们可以采用工厂方法和私有构造函数来完成对象创建和监听器的注册，这样就可以避免不正确的创建。

记住，我们的目的是，在对象未完成构造之前，不可以将其发布。

## 安全发布

1. 通过**静态初始化器**初始化对象引用。 
2. 将发布对象的引用存储到volatile域或者具有原子性的域中（如：java5.0中的AtomicReference）。 
3. 将发布对象引用存放到正确创建的对象的final域中。 
4. 将发布对象引用存放到由锁保护的域中（如：同步化的容器）。

注： 如果要发布一个被静态创建的对象，最简单的方式就是使用静态初始化器，如下面代码所示：public static Holder holder=new Holder\(\);静态初始化器由JVM在类初始化时执行，**JVM在执行静态变量的初始化时会有内在同步保护**，因此可以保证对象的安全发布。

一个对象是可变的，但是它的状态不会在发布后被修改，这样的对象称作“**高效不可变对象**”。

## 安全共享对象

1. 线程限制：一个被线程限制的对象，由线程独占，并且只能被占有它的线程修改。封闭在线程内。
2. 共享只读：一个共享只读的对象，在没有额外同步的情况下，可以被多个线程并发访问，但是任何线程都不能修改它。
3. 线程安全对象：一个线程安全的对象或者容器，在内部通过同步机制来保证线程安全，所以其他线程无需额外的同步就可以通过公共接口随意访问它。
4. 被守护对象：被守护对象只能通过获取特定的锁来访问

