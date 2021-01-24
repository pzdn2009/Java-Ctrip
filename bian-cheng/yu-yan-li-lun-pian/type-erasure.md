# Type Erasure

## 1. Introduction

基于以下事实：

* 类型擦除是针对**泛型**来讲的；那么对于T的擦除，就会到其上界，要么是Bounded类型，要么是Object；
* Java的泛型是编译器级别的，并非语言层面；
* 在生成的`Java字节码`中是不包含泛型中的类型信息的。使用泛型的时候加上的类型参数，会在编译器在编译的时候去掉。这个过程就称为**类型擦除**。

导致的结果tricky：

* 不同的泛型类型，getClass是`equals`的，如`ArrayList<Integer>与ArrayList<String>`；
* 通过反射，是可以直接任意类型操作，如`ArrayList#add()`

Eg：

```java
public static  <E> boolean containsElement(E [] elements, E element){
    for (E e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}
```

编译之后：

```java
public static  boolean containsElement(Object [] elements, Object element){
    for (Object e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}
```



## 2. 类型擦除引发的问题

### 2.1 Templates只能注入String或者Object

在使用RedisTemplate或者KafkaTemplate的时候，曾妄想支持泛型的RestTemplate&lt;String,T&gt;，可T的类型是永远都取不到的，因为会被擦除。

故而，在使用的时候，SDK提供了两种：

* \[String\]\(Redis/Kafka\)Template

### 2.2 通过反射，可以绕过T约束

因为编译完之后都是 Object，发射set值时，可以任意。

## 3. 总结

基于Type Erasure的特点，泛型参数T：

* 不能是基本类型，例如：`int`；
* 不能获取带泛型类型的`Class`，例如：`Pair<String>.class，`永远返回`Pair.Class`；
* 不能判断带泛型类型的类型，例如：`x instanceof Pair<String>，`同上；
* 不能实例化`T`类型，例如：`new T()`。只能`class.newInstance()`;







