# Eureka使用

## 實例

```text
eureka.instance.instanceId 来唯一标识，如果这个值没有设置，就采用**eureka.instance.metadataMap.instanceId**来代替。
```

instance之间通过eureka.instance.appName 来彼此访问，在spring cloud中默认值是spring.application.name,如果没有设置则为UNKNOWN。在实际使用中spring.application.name不可或缺,因为相同名字的应用会被Eureka合并成一个群集。

## eureka HA 相互註冊

1. hostname

   ```text
   eureka.instance.hostname=master 
   eureka.client.serviceUrl.defaultZone=http://backup:2222/eureka/ 
   ＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝ 
   eureka.instance.hostname=backup 
   eureka.client.serviceUrl.defaultZone=http://master:1111/eureka/
   ```

2. spring.application.name或eureka.instance.appname必须一致
3. 相互注册要开启： 

   ```text
   eureka.client.register-with-eureka=true 
   eureka.client.fetch-registry=true
   ```

### register-with-eureka

应用启动时，会利用指定的eureka.client.serviceUrl.defaultZone注册到对应的Eureka server中。

之后每隔30s（通过eureka.instance.leaseRenewalIntervalInSeconds来配置）向Eureka server发送一次心跳，如果Eureka server在90s（通过eureka.instance.leaseExpirationDurationInSeconds配置）内没有收到某个instance发来的心跳就会把这个instance从注册中心中移走。发送心跳的操作是一个异步任务，如果发送失败，则以2的指数形式延长重试的时间，直到达到eureka.instance.leaseRenewalIntervalInSeconds \* eureka.client.heartbeatExecutorExponentialBackOffBound这个上限,之后一直以这个上限值作为重试间隔，直至重新连接到Eureka server，并且重新尝试连接到Eureka server的次数是不受限制的。

### eureka.client.fetch-registry

Eureka client在启动时会从Eureka server获取注册信息并缓存到本地，之后只会增量获取信息（可以把eureka.client.shouldDisableDelta设置成false来强制每次都全量获取）。获取注册信息的操作也是一个异步任务，每隔30秒执行一次（通过eureka.client.registryFetchIntervalSeconds配置），如果操作失败，也是以2的指数形式延长重试时间，直到达到eureka.client.registryFetchIntervalSeconds \* eureka.client.cacheRefreshExecutorExponentialBackOffBound 这个上限，之后一直以这个上限值作为重试间隔，直至重新获取到注册信息，并且重新尝试获取注册信息的次数是不受限制的。 这些任务都是在com.netflix.discovery.DiscoveryClient中启动，spring cloud用org.springframework.cloud.netflix.eureka.CloudEurekaClient对这个类进行了扩展。

## 自定义Eureka的InstanceID

在Spring Cloud中，服务的Instance ID的默认值是**${spring.cloud.client.hostname}:${spring.application.name}:${spring.application.instance\_id:${server.port}}** ，也就是机器主机名:应用名称:应用端口 。因此在Eureka Server首页中看到的服务的信息类似如下：itmuch:microservice-provider-user:8000 。如果想要自定义这部分的信息怎么办？

示例：

```text
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
  instance:
    preferIpAddress: true
    instance-id: ${spring.cloud.client.ipAddress}:${server.port}        # 将Instance ID设置成IP:端口的形式
```

