# FunctionalInterface

Ref:[https://www.cnblogs.com/runningTurtle/p/7092632.html](https://www.cnblogs.com/runningTurtle/p/7092632.html)

* 什么是函数式接口（Functional Interface）
* 函数式接口用途
* 关于@FunctionalInterface注解
* 函数式接口里允许定义默认方法
* 函数式接口里允许定义静态方法
* 函数式接口里允许定义java.lang.Object里的public方法
* JDK中的函数式接口举例
* 参考资料

## 函数式接口

函数式接口，当然首先是一个接口，然后就是在这个接口里面**只能有一个抽象方法**。

## 用途

它们主要用在Lambda表达式和方法引用（实际上也可认为是Lambda表达式）上。

```java
@FunctionalInterface
    interface GreetingService 
    {
        void sayMessage(String message);
    }

GreetingService greetService1 = message -> System.out.println("Hello " + message);
```

## @FunctionalInterface注解

Java 8为函数式接口引入了一个新注解@FunctionalInterface，主要用于**编译级错误检查**，加上该注解，当你写的接口不符合函数式接口定义的时候，编译器会报错。

## 函数式接口里允许定义默认方法

```java
@FunctionalInterface
    interface GreetingService
    {
        void sayMessage(String message);

        default void doSomeMoreWork1()
        {
            // Method body
        }

        default void doSomeMoreWork2()
        {
            // Method body
        }
    }
```

## 函数式接口里允许定义静态方法

```java
@FunctionalInterface
    interface GreetingService 
    {
        void sayMessage(String message);
        static void printHello(){
            System.out.println("Hello");
        }
    }
```

## Object

```java
@FunctionalInterface
    interface GreetingService  
    {
        void sayMessage(String message);

        @Override
        boolean equals(Object obj);
    }
```

## 举例

```java
java.lang.Runnable,
java.awt.event.ActionListener, 
java.util.Comparator,
java.util.concurrent.Callable
java.util.function包下的接口，如Consumer、Predicate、Supplier等
```

