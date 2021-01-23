# Process EIP

实现消息转换，或者消费者。

选项：

* ref：必须。指向注册的Processor.

```java
public class MyProcessor implements Processor {
  public void process(Exchange exchange) throws Exception {
    // do something...
  }
}

from("activemq:myQueue").to("myProcessor");


Processor myProcessor = new MyProcessor();
...
from("activemq:myQueue").process(myProcessor);


    from("activemq:myQueue").process(new Processor() {
        public void process(Exchange exchange) throws Exception {
            String payload = exchange.getIn().getBody(String.class);
            // do something with the payload and/or exchange here
           exchange.getIn().setBody("Changed body");
       }
    }).to("activemq:myOtherQueue");
```

