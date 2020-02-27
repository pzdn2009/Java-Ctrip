# ThreadLocal

Ref：[http://www.cnblogs.com/dolphin0520/p/3920407.html](http://www.cnblogs.com/dolphin0520/p/3920407.html)

ThreadLocal为变量在每个线程中都创建了一个**副本**。

其实现了线程封闭，在本线程内使用变量都是安全的，不存在竞争。

其内部实现是通过：ThreadLocalMap。可以通过IDEA查看源码。

## 应用场景

数据库连接、Session管理、**请求头、订单号**等。

## 内存泄露

Ref：[https://www.cnblogs.com/xzwblog/p/7227509.html](https://www.cnblogs.com/xzwblog/p/7227509.html)

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

