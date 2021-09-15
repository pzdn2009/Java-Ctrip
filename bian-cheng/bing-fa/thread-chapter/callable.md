# Callable

A task that returns a result and may throw an exception.

* 执行返回结果
* 抛出异常

```java
@FunctionalInterface
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

## 与Runnable比较

* 两者都需要调用Thread.start\(\)启动线程；
* 实现Callable接口的任务线程能返回执行结果；而实现Runnable接口的任务线程不能返回结果；
* Callable接口的call\(\)方法允许抛出异常；而Runnable接口的run\(\)方法的异常只能在内部消化，不能继续上抛；

## 结果获取

```java
FutureTask.get() //阻塞主线程
```

通常结合ExecutorService一起使用。

