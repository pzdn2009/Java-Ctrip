# WebMvcConfigurerAdapter已过时

Spring Boot2.0的版本

新的實現：

```java
@Configuration
public class WebMvcConfg implements WebMvcConfigurer {
  //省略
}

//推荐
@Configuration
public class WebMvcConfg extends WebMvcConfigurationSupport {
  //省略
}
```

