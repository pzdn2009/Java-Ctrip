# Hystrix

## 1. 简介

教程Ref:[https://spring.io/guides/gs/circuit-breaker/\#initial](https://spring.io/guides/gs/circuit-breaker/#initial)

熔斷器模式：[http://martinfowler.com/bliki/CircuitBreaker.html。](http://martinfowler.com/bliki/CircuitBreaker.html。)

GIT源碼：git clone [https://github.com/spring-guides/gs-circuit-breaker.git](https://github.com/spring-guides/gs-circuit-breaker.git)

断路器模式

一个相对低层级的服务失败可能造成上层应用的级联失败，服务访问量越大失败率越高。当断路打开的时候，这个调用就被终止了。打开的断路可以阻止级联失败。

Netflix’s Hystrix庫提供了熔斷器的實現。

Hystrix watches for failing calls to that method, and if failures build up to a **threshold**, Hystrix opens the circuit so that subsequent calls automatically fail。

## 2. 使用

* maven依赖

```markup
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Camden.SR5</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

* @EnableCircuitBreaker

```java
@EnableCircuitBreaker
@RestController
@SpringBootApplication
public class ReadingApplication {
//....
}
```

* @HystrixCommand\(fallbackMethod="someMethods"\)

```java
package hello;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.net.URI;

@Service
public class BookService {

  private final RestTemplate restTemplate;

  public BookService(RestTemplate rest) {
    this.restTemplate = rest;
  }

  @HystrixCommand(fallbackMethod = "reliable")
  public String readingList() {
    URI uri = URI.create("http://localhost:8090/recommended");

    return this.restTemplate.getForObject(uri, String.class);
  }

  public String reliable() {
    return "Cloud Native Java (O'Reilly)";
  }

}
```

## 3. Feign支持Hystrix

application配置：

```text
feign.hystrix.enabled=true.
```

定义ACL：

```java
@FeignClient(name = "admin-ms"，fallback = "UserServiceHystrix.class")
public interface UserServiceAPI {

@RequestMapping(value = "/user/getUserInfo", method = RequestMethod.GET)
public BaseResponse<UserInfo> getUserInfo(@RequestParam("userId")Integer userId);
```

降级方法：

```java
@Component
public class UserServiceHystrix implement UserServiceAPI{
 @Override
 public BaseResponse<UserInfo> getUserInfo(Integer userId){
     return new BaseResponse<>().fail("调用服务失败");
 }
}
```

