# Effective Java

## 必要时进行保护性拷贝

对于构造器的每个可变参数进行保护性拷贝（defensive copy）是必要的

在内部组件返回给客户端的时候，也要考虑是否可以返回一个指向内部引用的数据。或者，不使用拷贝，你也可以返回一个不可变对象。如：Colletions.unmodifiableList\(List&lt;? extends T&gt; list\)

eg：

```java
static <T> List<T> defensiveCopy(Iterable<T> iterable) {
    ArrayList<T> list = new ArrayList();
    Iterator i$ = iterable.iterator();

    while(i$.hasNext()) {
        T element = i$.next();
        list.add(Preconditions.checkNotNull(element));
    }

    return list;
}
```

