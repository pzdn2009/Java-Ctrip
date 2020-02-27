# Preconditions

参数门卫。

1. checkArgument\(Boolean\)。

   功能描述：检查boolean是否为真。 用作方法中检查参数 失败时抛出的异常类型: **IllegalArgumentException**

2. checkNotNull

   功能描述：检查value不为null， 直接返回value； 失败时抛出的异常类型：**NullPointerException**

3. checkState。检查迭代器状态
4. checkElementIndex。索引范围是否对。 **IndexOutOfBoundsException**
5. checkPositionIndex。IndexOutOfBoundsException
6. checkPositionIndexes。IndexOutOfBoundsException

```java
public PostExample(final String title, final Date date, final String author) {  
    this.title = checkNotNull(title);  
    this.date = checkNotNull(date);  
    this.author = checkNotNull(author);  
} 

Preconditions.checkArgument(count > 0, "must be positive: %s", count);
```

Points:

* After **static imports**, the Guava methods are clear and unambiguous. checkNotNull makes it clear what is being done, and what exception will be thrown.
* checkNotNull returns its argument after validation, allowing simple one-liners in constructors: **this.field = checkNotNull\(field\)**;.
* Simple, varargs "printf-style" exception messages. \(This advantage is also why we recommend continuing to use checkNotNull over Objects.requireNonNull\) 

  Eg：

  ```java
  checkArgument(i >= 0, "Argument was %s but expected nonnegative", i);
  checkArgument(i < j, "Expected i < j, but %s > %s", i, j);
  ```

Ref：[https://github.com/google/guava/wiki/PreconditionsExplained](https://github.com/google/guava/wiki/PreconditionsExplained)

