# Feign

* 簡介
* Spring Cloud中使用
* 高級用法
* 安全驗證

## 1. Feign 简介

角色：Netflix中的 HTTP客户端。

类似的：

* URLConnection
* Apache的Http Client
* Netty的异步HTTP Client, 
* Spring的RestTemplate。

Feign是一种声明式、模板化的HTTP客户端。可使用Feign 注解和JAX-RS注解。Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果

## 2. Spring Cloud 中如何使用

引用：

```markup
<dependency> 
    <groupId>org.springframework.cloud</groupId>      
    <artifactId>spring-cloud-starter-feign</artifactId> 
</dependency>
```

Application上开启功能：

```java
@EnableFeignClients
```

声明：

```java
@FeignClient(value = "service-hi") 
interface SchedualServiceHi { 

    // 这里的/hello 是你注册的Eureka中的生命接口 
    @RequestMapping(value = ["/hello"], method =[(RequestMethod.GET)]) 
    fun sayHiFromClientOne(@RequestParam(value = "name") name:String): String 
}
```

* @FeignClient 指定了代理哪一个服务。
* @RequestHeader，@RequestBody，@RequestParam

调用：

```java
@Autowired private lateinit var schedualServiceHi: SchedualServiceHi
```

## 3. 高级用法

### 3.1 Encoder,Decoder,ErrorDecoder

Feign将方法签名中方法参数对象序列化为请求参数放到HTTP请求中的过程，是由编码器\(Encoder\)完成的。

同理，将HTTP响应数据反序列化为java对象是由解码器\(Decoder\)完成的。

默认情况下，Feign会将标有**@RequestParam**注解的参数转换成字符串添加到URL中，将**没有注解的参数**通过Jackson转换成json放到请求体中。

注意，如果在@RequetMapping中的method将请求方式指定为非POST，那么所有未标注解的参数将会被忽略

### 3.2 换成HTTPClient

```markup
<!-- 使用Apache HttpClient替换Feign原生httpclient --> 
<dependency> 
   <groupId>org.apache.httpcomponents</groupId> 
   <artifactId>httpclient</artifactId> 
</dependency> 

<dependency> 
   <groupId>com.netflix.feign</groupId> 
   <artifactId>feign-httpclient</artifactId> 
   <version>${feign-httpclient}</version> 
</dependency>
```

配置

```text
feign.httpclient.enabled=true
```

### 3.3 独立使用

* maven依赖
* 声明接口
* Builder
* 调用

@RequestLine @RequestHeader @Body

```java
@Headers({"Content-Type: application/json","Accept: application/json"}) 
@RequestLine("POST /users/list")

@RequestLine("POST /")
@Headers("Content-Type: application/json")
// json curly braces must be escaped! 
// 这里JSON格式需要的花括号居然需要转码，有点蛋疼了。
@Body("%7B\"user_name\": \"{user_name}\", \"password\": \"{password}\"%7D")
void json(@Param("user_name") String user, @Param("password") String password);
```

### 3.4 Feign Builder

```java
private final List<RequestInterceptor> requestInterceptors = new ArrayList();
private Level logLevel;
private Contract contract;
private Client client;
private Retryer retryer;
private Logger logger;
private Encoder encoder;
private Decoder decoder;
private ErrorDecoder errorDecoder;
private Options options;
private InvocationHandlerFactory invocationHandlerFactory;
private boolean decode404;
```

支持多种序列化机制、

Ref：[https://blog.csdn.net/u010862794/article/details/73649616](https://blog.csdn.net/u010862794/article/details/73649616)

## 4. 安全驗證

### 4.1 Basic

使用內置的Basic驗證：

* 註冊Bean
* 使用配置類

```java
@Configuration
public class FeignConfiguration{
     @Bean
     public Contract feignContract(){
         //表示默認的Feign方式，非Spring的.
         return new feign.Contract.Default();
    }

    @Bean
    public BasicAuthRequestInterceptor basicAuthRequestInterceptor(){
         return new BasicAuthRequestInterceptor("user","password");
   }
}

@FeignClient(name="hello", configuration=FeignConfiguration.class)
public interface HelloService{
    @RequestLine("GET /message")
    HelloMessage hello();
}
```

PS: FeignConfiguration类不能包含在主应用程序上下文的@ComponentScan中，否则该配置会被所有的@FeignClient共享。

