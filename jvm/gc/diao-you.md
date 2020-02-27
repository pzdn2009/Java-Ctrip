# 调优

* 日志分析
* 调优命令
* 调优工具

## 日志分析

PSYoungGen、ParOldGen、PSPermGen属于Parallel收集器。其中PSYoungGen表示gc回收前后年轻代的内存变化；ParOldGen表示gc回收前后老年代的内存变化；PSPermGen表示gc回收前后永久区的内存变化。young gc 主要是针对年轻代进行内存回收比较频繁，耗时短；full gc 会对整个堆内存进行回城，耗时长，因此一般尽量减少full gc的次数

## 调优命令

Sun JDK监控和故障处理命令有jps jstat jmap jhat jstack jinfo

* jps，JVM Process Status Tool,显示指定系统内所有的HotSpot虚拟机进程。
* jstat，JVM statistics Monitoring是用于监视虚拟机运行时状态信息的命令，它可以显示出虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据。
* jmap，JVM Memory Map命令用于生成heap dump文件
* jhat，JVM Heap Analysis Tool命令是与jmap搭配使用，用来分析jmap生成的dump，jhat内置了一个微型的HTTP/HTML服务器，生成dump的分析结果后，可以在浏览器中查看
* jstack，用于生成java虚拟机当前时刻的线程快照。
* jinfo，JVM Configuration info 这个命令作用是实时查看和调整虚拟机运行参数。

## 调优工具

常用调优工具分为两类,jdk自带监控工具：jconsole和jvisualvm，第三方有：MAT\(Memory Analyzer Tool\)、GChisto。

* jconsole，Java Monitoring and Management Console是从java5开始，在JDK中自带的java监控和管理控制台，用于对JVM中内存，线程和类等的监控
* jvisualvm，jdk自带全能工具，可以分析内存快照、线程快照；监控内存变化、GC变化等。
* MAT，Memory Analyzer Tool，一个基于Eclipse的内存分析工具，是一个快速、功能丰富的Java heap分析工具，它可以帮助我们查找内存泄漏和减少内存消耗
* GChisto，一款专业分析gc日志的工具

