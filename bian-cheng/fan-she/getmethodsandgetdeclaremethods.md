# getMethods&getDeclareMethods

Member接口中的定义：

```java
    /**
     * Identifies the set of all public members of a class or interface,
     * including inherited members.
     */
    public static final int PUBLIC = 0;

    /**
     * Identifies the set of declared members of a class or interface.
     * Inherited members are not included.
     */
    public static final int DECLARED = 1;
```

Declare：

> **this object**, including **public, protected, default \(package\) access, and private methods**, **but excluding inherited methods**.

当前类型的所有方法。

Public：

> including those declared by the class or interface and those inherited from superclasses and superinterfaces.

包括继承的公有方法。

