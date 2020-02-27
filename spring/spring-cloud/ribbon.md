# Ribbon

Ribbon is a client side load balancer which gives you a lot of control over the behaviour of HTTP and TCP clients.

**Feign already uses Ribbon**, so if you are using @FeignClient then this section also applies.

## 使用

### 依赖

* feign
* ribbon

### 启动类

```java
@SpringCloudApplication
@EnableFeignClients
@EnableEurekaClient
```

### 声明、使用Feign

```java
@FeignClient ...
```

## 高级

### Eureka与Ribbon

### Ribbon缓存配置

### Ribbon属性选项

REf

[https://blog.csdn.net/Jeson0725/article/details/70058910](https://blog.csdn.net/Jeson0725/article/details/70058910)

