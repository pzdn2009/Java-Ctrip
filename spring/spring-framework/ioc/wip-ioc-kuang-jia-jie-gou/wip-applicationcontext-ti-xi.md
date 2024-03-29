# ApplicationContext体系

## 1. Introduction

![ApplicationContext](../../../../.gitbook/assets/image%20%281%29.png)

> **Central interface** to provide configuration for an application. This is **read-only** while the application is running, but may be **reloaded** if the implementation supports this.
>
> An ApplicationContext provides: 
>
> * Bean factory methods for accessing application components. 
>   * Inherited from `ListableBeanFactory`. 
>   * 此设计更像一种代理模式
> * The ability to load file resources in a generic fashion. 
>   * Inherited from the `org.springframework.core.io.ResourceLoader` interface. 
>   * 提供资源加载能力
> * The ability to publish events to registered listeners. 
>   * Inherited from the `ApplicationEventPublisher` interface. 
>   * 提供事件机制
> * The ability to resolve messages, supporting internationalization.
>   * Inherited from the `MessageSource` interface. 
>   * 提供国际化资源
> * Inheritance from a parent context. Definitions in a descendant context will always take priority. 
>   * This means, for example, that a single parent context can be used by an entire web application, while each servlet has its own child context that is independent of that of any other servlet.
>   * 提供一个父级上下文



 

