# ReflectionUtils

* isEqualsMethod：判断是否为equals方法

  ```java
  public static boolean isEqualsMethod(@Nullable Method method) {
    if (method == null || !method.getName().equals("equals")) {
        return false;
    }
    Class<?>[] paramTypes = method.getParameterTypes();
    return (paramTypes.length == 1 && paramTypes[0] == Object.class);
  }
  ```

* isHashCodeMethod：是否有hashCode方法

