# 示例

## Console

```markup
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://camel.apache.org/schema/spring http://camel.apache.org/schema/spring/camel-spring.xsd">

  <!-- tag::e1[] -->
  <!-- camelContext is the Camel runtime, where we can host Camel routes -->
  <camelContext xmlns="http://camel.apache.org/schema/spring">
    <route>
      <!-- read input from the console using the stream component -->
      <from uri="stream:in?promptMessage=Enter something: "/>
      <!-- transform the input to upper case using the simple language -->
      <!-- you can also use other languages such as groovy, ognl, mvel, javascript etc. -->
      <transform>
        <simple>${body.toUpperCase()}</simple>
      </transform>
      <!-- and then print to the console -->
      <to uri="stream:out"/>
    </route>
  </camelContext>
  <!-- end::e1[] -->

</beans>
```

## rabbitmq

```java
@Component
public class SampleCamelRouter extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("timer:hello?period=1000")
            .transform(simple("Random number ${random(0,100)}"))
            .to("rabbitmq:foo");

        from("rabbitmq:foo")
            .log("From RabbitMQ: ${body}");
    }

}
```

配置：

```text
camel.component.rabbitmq.hostname=localhost
camel.component.rabbitmq.port-number=5672
camel.component.rabbitmq.username=guest
camel.component.rabbitmq.password=guest
```

