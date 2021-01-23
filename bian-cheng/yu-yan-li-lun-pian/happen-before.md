# Happen Before

## Introduction

1. **单线程**happen-before原则：
   1. 在同一个线程中，书写在前面的操作happen-before后面的操作。
   2. 顺序性
2. **锁**的happen-before原则：
   1. 同一个锁的unlock操作happen-before此锁的lock操作。
   2. 锁的可用性
3. **volatile**的happen-before原则：
   1. 对一个volatile变量的写操作happen-before对此变量的任意操作。
   2. 先有值
4. happen-before的**传递性**原则：
   1. 如果A操作 happen-before B操作，B操作happen-before C操作，那么A操作happen-before C操作。
   2. 传递性
5. **线程启动**的happen-before原则：
   1. 同一个线程的start方法happen-before此线程的其它方法。
   2. 线程的开始
6. **线程中断**的happen-before原则：
   1. 对线程`interrupt`方法的调用happen-before被中断线程的检测到中断发送的代码。
7. **线程终结**的happen-before原则：
   1. 线程中的所有操作都happen-before线程的终止检测。
   2. 线程的结束
8. **对象创建**的happen-before原则：
   1. 一个对象的初始化完成先于他的finalize方法调用。
   2. 生命周期

