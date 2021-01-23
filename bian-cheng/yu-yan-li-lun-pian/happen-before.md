# WIP Happen Before



* **单线程**happen-before原则：在同一个线程中，书写在前面的操作happen-before后面的操作。
* **锁**的happen-before原则：同一个锁的unlock操作happen-before此锁的lock操作。
* **volatile**的happen-before原则：对一个volatile变量的写操作happen-before对此变量的任意操作\(当然也包括写操作了\)。
* happen-before的**传递性**原则：如果A操作 happen-before B操作，B操作happen-before C操作，那么A操作happen-before C操作。
* **线程启动**的happen-before原则：同一个线程的start方法happen-before此线程的其它方法。
* **线程中断**的happen-before原则：对线程interrupt方法的调用happen-before被中断线程的检测到中断发送的代码。
* **线程终结**的happen-before原则：线程中的所有操作都happen-before线程的终止检测。
* **对象创建**的happen-before原则：一个对象的初始化完成先于他的finalize方法调用。

