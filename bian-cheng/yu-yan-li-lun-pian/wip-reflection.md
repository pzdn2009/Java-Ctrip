# Reflection

## 1. Introduction

反射机制是 Java 语言提供的一种基础功能，赋予了 Java 程序在运行时的**自省**（introspect，官方用语）的能力。通过 Java 的反射机制，程序员可以在 Java 程序在运行态的时候操作任意的类或者对象的属性、方法。利用 Java 的反射机制，可以做到以下：

* 在Runtime可以获取对象所属的类定义——`Class`；
* 在Runtime可以构造类的对象实例——`Constructor`；
* 在Runtime可以获取，或者修改类的成员属性——`Field`；
* 在Runtime可以调用某个类，或者对象的方法——`Method`；
* 在Runtime可以获取类的其他信息，比如描述修饰符、父类信息等——`Modifier`；
* 动态创建数组：`Array`。

在Java的世界，一切皆Object，而描述对象定义的数据，可以理解为元数据，反射就属于此范畴的操作。

核心API：

* `getX`：返回public的X，包括集成的
* `getDeclaredX`：返回所有修饰的X
* `invoke`：调用方法
* `getConstructors`：返回所有构造函数
* `getSuperclass`：返回父类
* `getInterfaces`：返回接口
* `getDeclaredField`：返回属性
* `setAccessible`：授权访问属
* `getAnnotations`：返回注解

## 2. Usage

### 2.1 反射調用成员方法

```java
//newValue 赋值到 target 过程
private void updateFields(Object target, Object newValue) throws IllegalAccessException {
    for (Field fieldNew : newValue.getClass().getDeclaredFields()) {
        try {
            var fieldOld = target.getClass().getDeclaredField(fieldNew.getName());
            if (!Modifier.isStatic(fieldNew.getModifiers())) {
                fieldOld.setAccessible(true);
                fieldNew.setAccessible(true);

                fieldOld.set(target, fieldNew.get(newValue));
            }
        } catch (NoSuchFieldException exception) {
            log.error("No such field {}, new add it? {}", fieldNew.getName(), exception);
        }
    }
}
```

* `getFields`：获取字段
* `Modifier`：修饰符
* `setAccessible`：设置可访问
* `get`：获取字段的值
* `set`：设置字段的值

### 2.2 获取Class的几种方法

```java
//根据全名获取Class
Class.forName("类的全名"); 

//根据instance获取Class
person.getClass();

//根据Class符号获取Class
Config.class;
```

### 2.3 Official

{% embed url="https://www.oracle.com/technical-resources/articles/java/javareflection.html" %}

## 3. QA

### AccessibleObject有什么用？

Constructor、Field、Method 这三个类都继承 `AccessibleObject`，该对象有一个非常重要的方法 `AccessibleObject#setAccessible​(boolean flag)`，这里的所谓 accessible 可以理解成修饰成员的 public、protected、private，这意味着我们可以在程序运行时修改类成员的访问限制。  


### Declared是什么意思？

* 含Declared：所有声明的方法
* 不含Declared：搜索`publicOnly`的。

```java
private Method[] privateGetDeclaredMethods(boolean publicOnly) {
        checkInitted();
        Method[] res;
        ReflectionData<T> rd = reflectionData();
        if (rd != null) {
            res = publicOnly ? rd.declaredPublicMethods : rd.declaredMethods;
            if (res != null) return res;
        }
        // No cached value available; request value from VM
        res = Reflection.filterMethods(this, getDeclaredMethods0(publicOnly));
        if (rd != null) {
            if (publicOnly) {
                rd.declaredPublicMethods = res;
            } else {
                rd.declaredMethods = res;
            }
        }
        return res;
    }
```

如：

| 方法 | 含义 |
| :--- | :--- |
| getFields\(\) | 返回包含一个数组 Field 对象，表示的类或接口的所有可访问的**公共**字段 |
| getDeclaredFields\(\) | 返回包含一个数组 Field 对象，表示的类或接口声明的所有字段 |
| getField\(String name\) | 返回包含一个数组 Field 对象，表示的类或接口指定的可访问的**公共**字段 |
| getDeclaredField\(String name\) | 返回包含一个数组 Field 对象，表示的类或接口指定的任意权限的所有字段 |

### Class类如何理解？

* 本质上是一个类，即类的类；
* 有一个私有构造函数，只有**JVM才能创建它，**那么只有工厂方法**；**
* 同一类的对象，都只映射到一个Class。

```java
/*
     * Private constructor. Only the Java Virtual Machine creates Class objects.
     * This constructor is not used and prevents the default constructor being
     * generated.
     */
    private Class(ClassLoader loader) {
        // Initialize final field for classLoader.  The initialization value of non-null
        // prevents future JIT optimizations from assuming this final field is null.
        classLoader = loader;
    }

//方法：
public native Class<? super T> getSuperclass();
public Class<?>[] getClasses();
public Class<?>[] getDeclaredClasses()

```

###  判断是否为抽象类？

使用`Modifier`

```java
Class a = Class.forName("A"); 
System.out.println(Modifier.isAbstract(a.getModifiers())); 
```

## 4. 开源框架--Reflections框架能做什么？

1. 获取某个类型的全部子类——_**AllSubClasses**_; 
2. 只要类型、构造器、方法，字段上带有特定**注解**，便能获取带有这个注解的全部信息（类型、构造器、方法，字段）——_**Annotation based**_；
3. 获取所有能匹配某个正则表达式的资源；——_**Regex based**_；
4. 获取所有带有特定签名的方法，包括参数，参数注解，返回类型；——_**Signature**_ ；
5. 获取所有方法的名字；——_**Methods**_；
6. 获取代码里所有字段、方法名、构造器的使用；——_**Usage**_;

```markup
<dependency>
  <groupId>org.reflections</groupId>
  <artifactId>reflections</artifactId>
  <version>0.9.10</version>
</dependency>
```

代码片段：

```java
Set<Class<? extends InputStream>> allTypes = 
    reflections.getSubTypesOf(InputStream.class);

public class ReflectionTest {
    public static void main(String[] args) {
        // 扫包
        Reflections reflections = new Reflections(new ConfigurationBuilder()
                .forPackages("com.boothsun.reflections") // 指定路径URL
                .addScanners(new SubTypesScanner()) // 添加子类扫描工具
                .addScanners(new FieldAnnotationsScanner()) // 添加 属性注解扫描工具
                .addScanners(new MethodAnnotationsScanner() ) // 添加 方法注解扫描工具
                .addScanners(new MethodParameterScanner() ) // 添加方法参数扫描工具
                );
 
        // 反射出子类
        Set<Class<? extends ISayHello>> set = reflections.getSubTypesOf( ISayHello.class ) ;
        System.out.println("getSubTypesOf:" + set);
 
        // 反射出带有指定注解的类
        Set<Class<?>> ss = reflections.getTypesAnnotatedWith( MyAnnotation.class );
        System.out.println("getTypesAnnotatedWith:" + ss);
 
        // 获取带有特定注解对应的方法
        Set<Method> methods = reflections.getMethodsAnnotatedWith( MyMethodAnnotation.class ) ;
        System.out.println("getMethodsAnnotatedWith:" + methods);
 
        // 获取带有特定注解对应的字段
        Set<Field> fields = reflections.getFieldsAnnotatedWith( Autowired.class ) ;
        System.out.println("getFieldsAnnotatedWith:" + fields);
 
        // 获取特定参数对应的方法
        Set<Method> someMethods = reflections.getMethodsMatchParams(long.class, int.class);
        System.out.println("getMethodsMatchParams:" + someMethods);
 
        Set<Method> voidMethods = reflections.getMethodsReturn(void.class);
        System.out.println( "getMethodsReturn:" + voidMethods);
 
        Set<Method> pathParamMethods =reflections.getMethodsWithAnyParamAnnotated( PathParam.class);
        System.out.println("getMethodsWithAnyParamAnnotated:" + pathParamMethods);
    }
}
```

More：[https://www.programcreek.com/java-api-examples/index.php?api=org.reflections.scanners.MethodAnnotationsScanner](https://www.programcreek.com/java-api-examples/index.php?api=org.reflections.scanners.MethodAnnotationsScanner)

