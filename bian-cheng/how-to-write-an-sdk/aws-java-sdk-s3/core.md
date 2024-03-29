# core

## Exception

```text
SdkBaseException
```

##  ThreadLocal

提供了一个注册器，维护多个ThreadLocal

```java
@SdkProtectedApi
public final class SdkThreadLocalsRegistry {
  //generic params <?>
  private static final List<ThreadLocal<?>> threadLocals = new ArrayList();
  
  //singleton
  private SdkThreadLocalsRegistry() {
  }
  
  //register
  public static synchronized <T> ThreadLocal<T> register(ThreadLocal<T> threadLocal) {
    threadLocals.add(threadLocal);
    return threadLocal;
  }

  //clear all
  public static synchronized void remove() {
    Iterator var0 = threadLocals.iterator();

    while(var0.hasNext()) {
      ThreadLocal<?> t = (ThreadLocal)var0.next();
      t.remove();
    }

  }
}
```

## Credential

* AccessKey
* SecretKey

![](../../../.gitbook/assets/image%20%2824%29.png)

* Basic，对应一个默认实现
* Properties，从配置文件加载
* Anonymous，匿名的
* Provider，提供了多种实现

## Configuration

```text
ClientConfiguration
ClientConfigurationFactory
```

##  AwsClientBuilder





 

