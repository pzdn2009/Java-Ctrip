# Zuul

Ref： [https://blog.csdn.net/chenqipc/article/details/53322830](https://blog.csdn.net/chenqipc/article/details/53322830)

当一个UI应用想要代理调用一个或者多个后台服务的时候，Sping cloud创建了一个嵌入的Zuul proxy很方便的开发一个简单的案例。这个功能对于代理前端需要访问的后端服务非常有用，避免了所有后端服务需要关心管理CORS和认证的问题.

Zuul的主要功能是路由和过滤器。

zuul有以下功能：

* Authentication
* Insights
* Stress Testing
* Canary Testing
* Dynamic Routing
* Service Migration
* Load Shedding
* Security
* Static Response handling
* Active/Active traffic management

四中Filter：

* pre
* routing
* post
* error

## 使用

* maven依赖
* application配置
* @EnableZuulProxy
* ZuulFilter实现

```text
application.yml 
zuul: 
  routes: 
    users: 
      path: /myusers/** 
      serviceId: users_service
```

## 应用场景

前置过滤器、后置过滤器

重试机制

## 高级

Cookie、Header

窒息模式和本地跳转\(Strangulation Patterns and Local Forwards\)

上传文件

