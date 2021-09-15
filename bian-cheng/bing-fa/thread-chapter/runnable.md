# Runnable

The `Runnable` interface should be implemented by any class whose instances are intended to be executed by a thread.

The class must define a method of no arguments called `run`.

This interface is designed to **provide a common protocol** for objects that wish to execute code while they are active.

简而言之，实现了此接口的类，都可以扔给Thread去Start执行。是业务Action的一个载体，传递给Thread。

