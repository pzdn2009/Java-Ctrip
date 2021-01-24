# ApplicationRunner

Spring Boot 启动之后执行**初始化**工作。

* 容器启动成功后的最后一步回调；
* 可以结合@Order来指定执行顺序；
* 可以替代的方案：基于ApplicationEvents来触发。

EG：

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



