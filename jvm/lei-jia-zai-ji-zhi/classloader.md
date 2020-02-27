# ClassLoader

Class\#getClassLoader\(\)

自定义的，需要实现find

## 类的加载

类加载有三种方式：

1、命令行启动应用时候由JVM初始化加载

2、通过Class.forName\(\)方法动态加载

3、通过ClassLoader.loadClass\(\)方法动态加载

Class.forName\(\)和ClassLoader.loadClass\(\)区别

* Class.forName\(\)：将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块；
* ClassLoader.loadClass\(\)：只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容,只有在newInstance才会去执行static块。

