# CopyOnWriteArrayList

Ref :[http://ifeve.com/java-copy-on-write/](http://ifeve.com/java-copy-on-write/)

Copy-On-Write简称COW，是一种用于程序设计中的优化策略。

其基本思路是，从一开始大家都在共享同一个内容，当某个人想要修改这个内容的时候，才会真正把内容Copy出去形成一个新的内容然后再改，这是一种延时懒惰策略。

从JDK1.5开始Java并发包里提供了两个使用CopyOnWrite机制实现的并发容器,它们是**CopyOnWriteArrayList**和**CopyOnWriteArraySet**。

## CopyOnWriteArrayList的实现原理

在使用CopyOnWriteArrayList之前，我们先阅读其源码了解下它是如何实现的。以下代码是向ArrayList里添加元素，可以发现在添加的时候是需要加锁的，否则多线程写的时候会Copy出N个副本出来。

```java
public boolean add(T e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {

        Object[] elements = getArray();

        int len = elements.length;
        // 复制出新数组

        Object[] newElements = Arrays.copyOf(elements, len + 1);
        // 把新元素添加到新数组里

        newElements[len] = e;
        // 把原数组引用指向新数组

        setArray(newElements);

        return true;

    } finally {

        lock.unlock();

    }

}

final void setArray(Object[] a) {
    array = a;
}
```

读的时候不需要加锁，如果读的时候有多个线程正在向ArrayList添加数据，读还是会读到旧的数据，因为写的时候不会锁住旧的ArrayList。

```java
public E get(int index) {
    return get(getArray(), index);
}
```

## CopyOnWrite的应用场景

CopyOnWrite并发容器用于读多写少的并发场景。比如白名单，黑名单，商品类目的访问和更新场景，假如我们有一个搜索网站，用户在这个网站的搜索框中，输入关键字搜索内容，但是某些关键字不允许被搜索。这些不能被搜索的关键字会被放在一个黑名单当中，黑名单每天晚上更新一次。当用户搜索时，会检查当前关键字在不在黑名单当中，如果在，则提示不能搜索。实现代码如下：

```java
package com.ifeve.book;

import java.util.Map;

import com.ifeve.book.forkjoin.CopyOnWriteMap;

/**
 * 黑名单服务
 *
 * @author fangtengfei
 *
 */
public class BlackListServiceImpl {

    private static CopyOnWriteMap<String, Boolean> blackListMap = new CopyOnWriteMap<String, Boolean>(
            1000);

    public static boolean isBlackList(String id) {
        return blackListMap.get(id) == null ? false : true;
    }

    public static void addBlackList(String id) {
        blackListMap.put(id, Boolean.TRUE);
    }

    /**
     * 批量添加黑名单
     *
     * @param ids
     */
    public static void addBlackList(Map<String,Boolean> ids) {
        blackListMap.putAll(ids);
    }
}
```

