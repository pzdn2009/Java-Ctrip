# Inner Class

## 1. Introduction

{% hint style="info" %}
内部类是一个编译时的概念，一旦编译成功，就会成为完全不同的两类。

对于一个名为outer的外部类和其内部定义的名为inner的内部类。编译完成后出现`outer.class`和`outer$inner.class`两类。所以内部类的成员变量/方法名可以和外部类的**相同**。
{% endhint %}

为什么使用Nested/Inner Class？

* 逻辑上找个地方放在一起，比较合适；
* 封装
* 更可读和易于维护

类别：

* 成员内部类
* 静态内部类
* 匿名内部类
* 局部内部类

## 2. **成员内部类**

* **成员**类，与外部类Outer的成员并列，非静态修饰；
* 能够访问Outer的所有`member`和`method`；
  * 不能含有static的成员和方法；
  * `this.成员`，自己的
  * `Outer.this.成员`，外部的
  * 如果内外唯一的情况下，可以省略`this.`或`Outer.this.`；
* Outer访问Inner：
  * 成员方法：通常的套路——`new Inner()`来访问；
  * 静态方法：new Outer\(\)，接着 outer.new Inner\(\)来访问；
* 内部类对象隐含地保存了一个**引用**，指向创建它的外围类对象

```java
    //HashMap中的成员内部类
    final class Values extends AbstractCollection<V> {
        //访问Outer size
        public final int size()                 { return size; }
        //调用Outer clear，用HashMap.this，是因为内外同名了，区分一下
        public final void clear()               { HashMap.this.clear(); }
        //新建另一个成员类，因为能够访问任何成员
        public final Iterator<V> iterator()     { return new ValueIterator(); }
        //调用Outer#containsValue 方法
        public final boolean contains(Object o) { return containsValue(o); }
        //新建另一个静态内部类
        public final Spliterator<V> spliterator() {
            return new ValueSpliterator<>(HashMap.this, 0, -1, 0, 0);
        }
        
        //遍历，来吧，随便访问
        public final void forEach(Consumer<? super V> action) {
            Node<K,V>[] tab;
            if (action == null)
                throw new NullPointerException();
            if (size > 0 && (tab = table) != null) {
                int mc = modCount;
                for (int i = 0; i < tab.length; ++i) {
                    for (Node<K,V> e = tab[i]; e != null; e = e.next)
                        action.accept(e.value);
                }
                if (modCount != mc)
                    throw new ConcurrentModificationException();
            }
        }
    }
    

   //Outer：HashMap中直接new来用
   public Collection<V> values() {
        Collection<V> vs = values;
        if (vs == null) {
            vs = new Values();
            values = vs;
        }
        return vs;
    }
```

## **3. 静态内部类**

* 嵌套类：
  * static class；
  * 不依赖于Outer而存在，创建时不依赖Outer；
  * 不能访问Outer的非static成员变量和方法；
* Inner访问Outer
  * Inner可以有成员和static的；
  * Inner访问Outer的，只能是static的——即成员间没有关系；
* Outer访问Inner
  * static：直接dot调用；
  * 成员：new Inner来访问

```java
    //LinkedList中的静态内部类
 
     private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
    
    /**
     * Links e as first element.
     */
    private void linkFirst(E e) {
        final Node<E> f = first;
        //直接new Node
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
    }
```

## 4. 局部内部类

* 这个类的使用主要是应用与解决比较复杂的问题，想创建一个类来辅助我们的解决方案，到那时又不希望这个类是公共可用的；
* 局部内部类和成员内部类一样被编译，只是它的作用域发生了改变，它只能在该方法和属性中被使用，出了该方法和属性就会失效。

《Think in java》：

```java
public class Parcel5 {
    public Destionation destionation(String str){
        class PDestionation implements Destionation{
            private String label;
            private PDestionation(String whereTo){
                label = whereTo;
            }
            public String readLabel(){
                return label;
            }
        }
        return new PDestionation(str);
    }
    
    public static void main(String[] args) {
        Parcel5 parcel5 = new Parcel5();
        Destionation d = parcel5.destionation("chenssy");
    }
}
```

## 5. 匿名内部类

* 匿名内部类是没有访问修饰符的
* new 匿名内部类，这个类首先是要存在的，比如是一个接口定义；

 

```java
.additionalInterceptors(new ClientHttpRequestInterceptor() {
    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        return null;
    }
});

@Bean
public WebMvcConfigurer apiMetricsWebMvcConfigurer(MetricsProperties metricsProperties, ApiMetricsHandlerInterceptor apiMetricsHandlerInterceptor) {
    return new WebMvcConfigurer() {
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            registry.addInterceptor(apiMetricsHandlerInterceptor).addPathPatterns(metricsProperties.getApiCall().getPathPatterns());
        }
    };
}
```

 

##  



