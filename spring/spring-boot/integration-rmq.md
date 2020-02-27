# Integration Rmq

## 1. config

POM：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

application地址配置：

```text
spring:
  rabbitmq:
    port: 5672
    username: admin
    password: admin
    virtual-host: CgpDev
    host: 192.18.21.226
```

## 2. Consumer Example

```java
@Slf4j
@Service
@Configuration
public class CallbackMessageService {

    private static final String CALLBACK_MESSAGE = "CALLBACK_QUEUE";

    @Bean
    public Queue callbackQueue() {
        return new Queue(CALLBACK_MESSAGE, true);
    }

    /**
     * 处理消息
     *
     * @param message
     */
    @RabbitListener(queues = CALLBACK_MESSAGE)
    public void processCallbackMessage(Message message) {

        String body = "";
        try {
            body = new String(message.getBody());
            log.debug("processCallbackMessage消费消息成功");
        } catch (Exception ex) {
            log.error("processCallbackMessage消费消息失败");
        }

        if (StringUtils.hasLength(body)) {
            //dealCallbackMessage(body);
        }
    }
}
```

## 3. 采坑

使用官方例子：

```java
@Component
@RabbitListener(queues="test_mq_testQueue")
public class Receiver {

    @RabbitHandler 
    public void process(String msg){
        System.out.println(msg);
    }
}
```

但是运行接收消息时，后台一直循环报错

```text
Caused by: org.springframework.amqp.AmqpException: No method found for class [B
```

@RabbitListener\(queues=”test\_mq\_testQueue”\)不需要放在类上。直接注释在方法上就好

Ref：[http://blog.csdn.net/olinner123/article/details/77477323](http://blog.csdn.net/olinner123/article/details/77477323)

