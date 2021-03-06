# 运行时数据区域

线程独占：JVM栈、本地方法栈、程序计数器

线程共享：方法区、Java堆

## 程序计数器（Program Counter Register）

### 作用

记录当前线程所执行到的字节码的行号。

字节码解释器工作的时候就是通过改变这个计数器的值来选取下一条需要执行的字节码指令。

### 意义

JVM的多线程是通过线程轮流切换并分配处理器来实现的，对于我们来说的并行事实上一个处理器也只会执行一条线程中的指令。

所以，为了保证各线程指令的安全顺利执行，每条线程都有独立的私有的程序计数器。

### 存储内容

当线程中执行的是一个Java方法时，程序计数器中记录的是正在执行的线程的虚拟机字节码指令的地址。

当线程中执行的是一个本地方法时，程序计数器中的值为空。

### 可能出现异常

此内存区域是**唯一一个**在JVM上不会发生内存溢出异常（OutOfMemoryError）的区域。

## JVM栈（Java Virtual Machine Stacks）

### 作用

描述Java方法执行的内存模型。每个方法在执行的同时都会开辟一段内存区域用于存放方法运行时所需的数据，成为**栈帧**，一个栈帧包含如：**局部变量表**、**操作数栈**、**动态链接**、**方法出口**等信息。

### 意义

JVM是基于栈的，所以每个方法从调用到执行结束，就对应着一个栈帧在虚拟机栈中入栈和出栈的整个过程。

### 存储内容

局部变量表（编译期可知的各种基本数据类型、引用类型和指向一条字节码指令的returnAddress类型）、操作数栈、动态链接、方法出口等信息。

值得注意的是：局部变量表所需的内存空间在**编译期间**完成分配。在方法运行的阶段是不会改变局部变量表的大小的。

### 可能出现的异常

* 如果线程请求的栈深度大于虚拟机所允许的深度，将抛出StackOverflowError异常。 ——栈溢出
* 如果在动态扩展内存的时候无法申请到足够的内存，就会抛出OutOfMemoryError异常。——内存溢出

## 本地方法栈（Native Method Stack）

### 作用

为JVM所调用到的Nativa即本地方法服务。

### 可能出现的异常

和虚拟机栈出现的异常很相像。

## Java堆（Java Heap）

### 作用

所有线程共享一块内存区域，在虚拟机开启的时候创建。

### 意义

1. 存储**对象**实例，更好地分配内存。 
2. 垃圾回收（GC）。堆是垃圾收集器管理的主要区域。更好地回收内存。 

### 存储内容

存放对象实例，几乎所有的对象实例都在这里进行分配。堆可以处于物理上不连续的内存空间，只要逻辑上是连续的就可以。 值得注意的是：在JIT编译器等技术的发展下，所有对象都在堆上进行分配已变得不那么绝对。有些对象实例也可以分配在栈中。

### 可能出现的异常

* 实现堆可以是固定大小的，也可以通过设置配置文件设置该为可扩展的。 
* 如果堆上没有内存进行分配，并无法进行扩展时，将会抛出OutOfMemoryError异常。

## 方法区（Method Area）

### 作用

用于存储运行时**常量池**、已被虚拟机**加载的类信息**、**常量**、**静态变量**、**即时编译器编译后的代码**等数据。

### 意义

对运行时常量池、常量、静态变量等数据做出了规定。

### 存储内容

运行时常量池（具有动态性）、已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

### 可能出现的异常

当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常。

