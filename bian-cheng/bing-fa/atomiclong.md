# AtomicLong

可以用原子方式更新的 long 值。

## API

1. public final long get\(\)  获取当前值。返回：当前值
2. public final void set\(long newValue\)  设置为给定值。
3. public final long getAndSet\(long newValue\)  以原子方式设置为给定值，并返回旧值。
4. public final boolean compareAndSet\(long expect, long update\) 如果当前值 == 预期值，则以原子方式将该值设置为给定的更新值。

   参数：expect - 预期值 update - 新值

   返回：如果成功，则返回 true。返回 false 指示实际值与预期值不相等

5. public final long getAndIncrement\(\)  以原子方式将当前值加 1。 返回：以前的值
6. public final long incrementAndGet\(\)   以原子方式将当前值加 1。返回：更新的值
7. public long longValue\(\) 以 long 形式返回指定的数值。这可能涉及到舍入或取整。

eg:

```java
import java.util.concurrent.atomic.AtomicLong;

public class TestThread {

   static class Counter {
      private AtomicLong c = new AtomicLong(0);

      public void increment() {
         c.getAndIncrement();
      }

      public long value() {
         return c.get();
      }
   }
   public static void main(final String[] arguments) throws InterruptedException {
      final Counter counter = new Counter();
      //1000 threads
      for(int i = 0; i < 1000 ; i++) {
         new Thread(new Runnable() {
            public void run() {
               counter.increment();
            }
         }).start();
      }  
      Thread.sleep(6000);

      System.out.println("Final number (should be 1000): " + counter.value());
   }  
}
```

