# final

final域能夠保證初始化過程是安全的，从而可以不受限制地访问不可变对象，并在共享这些对象时无须同步。

将字段修饰为final是一种良好的编程习惯，除非field是可变的。

Ref:[http://www.importnew.com/7553.html](http://www.importnew.com/7553.html)

1. final关键字可以用于**成员变量、本地变量、方法以及类**。
2. final成员变量必须在**声明的时候初始化**或者在**构造器中初始化**，否则就会报编译错误。
3. 你不能够对final变量再次赋值。
4. 本地变量必须在声明时赋值。
5. 在**匿名类**中所有变量都必须是final变量。
6. final方法不能被重写。
7. final类不能被继承。
8. final关键字不同于finally关键字，后者用于异常处理。
9. final关键字容易与finalize\(\)方法搞混，后者是在Object类中定义的方法，是在垃圾回收之前被JVM调用的方法。

   接口中声明的所有变量本身是final的。

10. final和abstract这两个关键字是反相关的，final类就不可能是abstract的。
11. final方法在编译阶段绑定，称为**静态绑定**\(static binding\)。
12. 没有在声明时初始化final变量的称为空白final变量\(blank final variable\)，它们必须在构造器中初始化，或者调用this\(\)初始化。不这么做的话，编译器会报错“final变量\(变量名\)需要进行初始化”。
13. 将类、方法、变量声明为final能够**提高性能**，这样JVM就有机会进行估计，然后优化。
14. 按照Java代码惯例，final变量就是常量，而且通常常量名要大写：

    ```java
    private final int COUNT = 10;
    ```

15. 对于集合对象声明为final指的是引用不能被更改，但是你可以向其中增加，删除或者改变内容。譬如：

    ```java
    private final List Loans = new ArrayList();
    list.add(“home loan”);  //valid
    list.add("personal loan"); //valid
    loans = new Vector();  //not valid
    ```

