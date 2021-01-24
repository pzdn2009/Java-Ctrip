# Java 匿名类

new + 接口名 实现一个匿名类。

```java
new 父类构造器（参数列表）|实现接口（）    
    {    
     //匿名内部类的类体部分    
    }
```

Sample1：

```java
ISay say = new ISay() {          
            public void sayHello() {
                System.out.println("Hello java!");
            }
        };
```

Sample2：

```java
Advice EMPTY_ADVICE = new Advice() {};
```

