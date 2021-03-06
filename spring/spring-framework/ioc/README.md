# ONG IoC Container

## 1. Introduction

主要包含两个大内容：

* IOC容器本身；
* IOC容器里面装的Bean；

其中，IOC容器本身是一项结构化工程，需要大量的定义和抽象来构建一个强功能的炼丹炉；而Bean更偏向流水线产品，是容器产出的丹药。

## 2. IoC 容器 Summary

### 2.1 容器的框架结构



### 2.2 容器的启动/装载过程



### 2.3 容器的运行时操作



## 3. Bean Summary

### 3.1 Bean相关抽象与定义



### 3.2 Bean的Create过程



## 4. TODO:

{% embed url="https://blog.csdn.net/yu\_kang/article/details/88068619?utm\_medium=distribute.pc\_relevant.none-task-blog-searchFromBaidu-3.control&depth\_1-utm\_source=distribute.pc\_relevant.none-task-blog-searchFromBaidu-3.control" %}



`ClassPathXmlApplicationContext` FileSystemXmlApplicationContext

Container,Beans

AnnotationConfigApplicationContext

1. 构造函数
2. 继承层次
3. 父类构造函数的作用
4. 生成reader和scanner
   1. 
5. 继承层次图
6. getEnvironment
7. beans注册
   1. this.beanFactory.beanDefinitionMap
8. refresh\(\)

```java
// Prepare this context for refreshing.
prepareRefresh();

// Tell the subclass to refresh the internal bean factory.
ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

// Prepare the bean factory for use in this context.
prepareBeanFactory(beanFactory);

try {
   // Allows post-processing of the bean factory in context subclasses.
   postProcessBeanFactory(beanFactory);

   StartupStep beanPostProcess = this.applicationStartup.start("spring.context.beans.post-process");
   // Invoke factory processors registered as beans in the context.
   invokeBeanFactoryPostProcessors(beanFactory);

   // Register bean processors that intercept bean creation.
   registerBeanPostProcessors(beanFactory);
   beanPostProcess.end();

   // Initialize message source for this context.
   initMessageSource();

   // Initialize event multicaster for this context.
   initApplicationEventMulticaster();

   // Initialize other special beans in specific context subclasses.
   onRefresh();

   // Check for listener beans and register them.
   registerListeners();

   // Instantiate all remaining (non-lazy-init) singletons.
   finishBeanFactoryInitialization(beanFactory);

   // Last step: publish corresponding event.
   finishRefresh();
}

catch (BeansException ex) {
   if (logger.isWarnEnabled()) {
      logger.warn("Exception encountered during context initialization - " +
            "cancelling refresh attempt: " + ex);
   }

   // Destroy already created singletons to avoid dangling resources.
   destroyBeans();

   // Reset 'active' flag.
   cancelRefresh(ex);

   // Propagate exception to caller.
   throw ex;
}

finally {
   // Reset common introspection caches in Spring's core, since we
   // might not ever need metadata for singleton beans anymore...
   resetCommonCaches();
   contextRefresh.end();
}
```

BeanFactory的生命周期，Bean的生命周期。



 





