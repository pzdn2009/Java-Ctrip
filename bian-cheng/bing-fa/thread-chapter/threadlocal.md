# ThreadLocal

## 简介

ThreadLocal为变量在每个线程中都创建了一个**副本**。其实现了线程封闭，在本线程内使用变量都是安全的，不存在竞争。其内部实现是通过：ThreadLocalMap。

应用场景：数据库连接、Session管理、**请求头、订单号**等。

## 源码

```java

public void set(T value) {
    Thread t = Thread.currentThread(); //获取当前线程t
    ThreadLocalMap map = getMap(t);  //根据当前线程获取到ThreadLocalMap
    if (map != null)
        map.set(this, value); //K，V设置到ThreadLocalMap中
    else
        createMap(t, value); //创建一个新的ThreadLocalMap
}

    /**
     * Returns the value in the current thread's copy of this
     * thread-local variable.  If the variable has no value for the
     * current thread, it is first initialized to the value returned
     * by an invocation of the {@link #initialValue} method.
     *
     * @return the current thread's value of this thread-local
     */
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
    
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
    
    /**
     * ThreadLocalMap is a customized hash map suitable only for
     * maintaining thread local values. No operations are exported
     * outside of the ThreadLocal class. The class is package private to
     * allow declaration of fields in class Thread.  To help deal with
     * very large and long-lived usages, the hash table entries use
     * WeakReferences for keys. However, since reference queues are not
     * used, stale entries are guaranteed to be removed only when
     * the table starts running out of space.
     */
    static class ThreadLocalMap {

        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }

        /**
         * The initial capacity -- MUST be a power of two.
         */
        private static final int INITIAL_CAPACITY = 16;

        /**
         * The table, resized as necessary.
         * table.length MUST always be a power of two.
         */
        private Entry[] table;

    
Thread.java

    /* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */
    ThreadLocal.ThreadLocalMap threadLocals = null;


```

* **Thread**中有一个变量——ThreadLocalMap，而且是独立的一个Map
* **ThreadLocalMap**有一个Entry数组，每个Entry是一个完整的对象——
  * key：ThreadLocal本身
  * value：泛型值
* 每个线程在往ThreadLocal里设置值的时候，都是往自己的ThreadLocalMap里存，读也是以某个ThreadLocal作为引用，在自己的map里找对应的key，从而实现了线程隔离。
* 非常巧妙：**Thread --&gt; ThreadLocalMap\(ThreadLocal&lt;?&gt;,  Value\)**

## 内存泄露

Ref：[https://www.cnblogs.com/xzwblog/p/7227509.html](https://www.cnblogs.com/xzwblog/p/7227509.html) \[深入理解ThreadLocal\]

* 弱引用（Weak Reference）：弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了**只具有弱引用**的对象，不管当前内存空间足够与否，都会回收它的内存
* Key采用了弱引用，Key在GC完了之后，可能就为NULL，但是Value还在，如果当前线程再迟迟不结束的话，这些key为null的Entry的value就会一直存在一条强引用链：

  > Thread Ref -&gt; Thread -&gt; ThreaLocalMap -&gt; Entry -&gt; value

* 


阿里doc建议： 必须回收自定义的ThreadLocal变量，尤其在线程池场景下，线程经常会被复用，如果不清理自定义的 ThreadLocal变量，可能会影响后续业务逻辑和造成内存泄露等问题。尽量在代理中使用try-finally块进行回收。

```java
/**
     * @author caikang
     * @date 2017/04/07
     */
    public class UserHolder {
        private static final ThreadLocal<User> userThreadLocal = new ThreadLocal<User>();

        public static void set(User user){
            userThreadLocal.set(user);
        }

        public static User get(){
            return userThreadLocal.get();
        }

        public static void remove(){
            userThreadLocal.remove();
        }
    }

    /**
     * @author caikang
     * @date 2017/04/07
     */
    public class UserInterceptor extends HandlerInterceptorAdapter {
        @Override
        public boolean preHandle(HttpServletRequest request,
            HttpServletResponse response, Object handler) throws Exception {
            UserHolder.set(new User());
            return true;
        }

        @Override
        public void afterCompletion(HttpServletRequest request,
            HttpServletResponse response, Object handler, Exception ex) throws Exception {
            UserHolder.remove();
        }
    }
```

### 示例

新建一個攔截器

```java
public class CatInfoInterceptor extends HandlerInterceptorAdapter {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HandlerMethod method = (HandlerMethod) handler;
        CatInfo.init(method.getMethod().getDeclaringClass() + "->" + method.getMethod().getName());
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        CatInfo.remove();
    }

    @Override
    public void afterConcurrentHandlingStarted(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    }
}
```

註冊攔截器

```java
@Configuration
public class AppConfig extends WebMvcConfigurerAdapter {

    /**
     *
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new CatInfoInterceptor()).addPathPatterns("/api/mpg/**");
    }
}
```

CatInfo實現Remove

```java
public static void remove() {
        catInfoThreadLocal.remove();
    }
```

