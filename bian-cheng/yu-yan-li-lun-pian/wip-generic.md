# Generic

## 1. 泛型方法

1. 所有泛型方法声明都有一个类型参数声明部分（由尖括号分隔），该类型参数声明部分在方法返回类型之前。
2. 类型参数只能代表引用型类型，不能是原始类型（像int,double,char的等）。

```java
public static<T,E> void print(T value) {
}

public <T, S extends T> T testGenericMethodDefine(T t, S s){

}
```

定义带类型参数的方法，目的是为了**表达多个参数以及返回值之间的关系**。例如本例子中T和S的继承关系， 返回值的类型和第一个类型参数的值相同。

```text
public <T> void testGenericMethodDefine2(List<T> s){

}

//使用通配符定义
public void testGenericMethodDefine2(List<？> s){

}
```

## 2. 上下界

```java
<T extends Comparable<T>>
<T super F>
```

## 3. 泛型类

```java
public class TestClassDefine<T, S extends T> {

}
```

## 4. 通配符Wildcard

```java
List<?>
List<? extends Number>
List<? extends T>

interface Collection<E> { 
  ... 
  public boolean addAll(Collection<? extends E> c); 
  ... 
}
```

