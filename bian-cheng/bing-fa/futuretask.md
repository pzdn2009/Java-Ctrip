# FutureTask

## Future 接口

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

* **cancel**方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。
  * 参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。
  * 如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；
  * 如果任务**正在执行**，**若mayInterruptIfRunning设置为true，则返回true**，若mayInterruptIfRunning设置为false，则返回false；
  * **如果任务还没有执行**，则无论mayInterruptIfRunning为true还是false，**肯定返回true**。
* **isCancelled**方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
* **isDone**方法表示任务是否已经完成，若任务完成，则返回true；
* **get**\(\)方法用来获取执行结果，这个方法会产生**阻塞**，会一直等到任务执行完毕才返回；
* **get**\(long timeout, TimeUnit unit\)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。

也就是说Future提供了三种功能： 1. 判断任务是否完成； 2. 能够中断任务； 3. 能够获取任务执行结果。

## FutureTask

Ref：[http://www.cnblogs.com/dolphin0520/p/3949310.html](http://www.cnblogs.com/dolphin0520/p/3949310.html)

继承体系：

```java
public class FutureTask<V> implements RunnableFuture<V>

public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}
```

可能的状态转换：

* NEW -&gt; COMPLETING -&gt; NORMAL
* NEW -&gt; COMPLETING -&gt; EXCEPTIONAL
* NEW -&gt; CANCELLED
* NEW -&gt; INTERRUPTING -&gt; INTERRUPTED

构造函数：

```java
public FutureTask(Callable<V> callable) {
}
public FutureTask(Runnable runnable, V result) {
}
```

事实上，FutureTask是Future接口的一个**唯一实现类**。

### 示例

```java
public class Test {
    public static void main(String[] args) {
        //第一种方式
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        executor.submit(futureTask);
        executor.shutdown();

        //第二种方式，注意这种方式和第一种方式效果是类似的，只不过一个使用的是ExecutorService，一个使用的是Thread
        /*Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        Thread thread = new Thread(futureTask);
        thread.start();*/

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }

        System.out.println("主线程在执行任务");

        try {
            System.out.println("task运行结果"+futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```

注：如果为了可取消性而使用 Future 但又不提供可用的结果，则可以声明 `Future<?>` 形式类型、并返回 null 作为底层任务的结果。

