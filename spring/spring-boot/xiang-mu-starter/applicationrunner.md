# WIP ApplicationRunner

Spring Boot 启动之后执行**初始化**工作。

Usage

```java
@Component
@Order(value = 1)
public class InitializeUnionConfig implements ApplicationRunner {

    private MerchantService merchantService;

    @Autowired
    public InitializeUnionConfig(MerchantService merchantService) {
        this.merchantService = merchantService;
    }

    @Override
    public void run(ApplicationArguments applicationArguments) throws Exception {

        LogAdapterAppender.addAppender();

        var properties = merchantService.getMerchant().toProperties();

        //
    }
}
```

如果有多个，可以通过@Order注解来指定顺序。

