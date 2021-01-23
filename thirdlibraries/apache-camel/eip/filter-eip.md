# Filter EIP

Ref：[https://camel.apache.org/manual/latest/filter-eip.html](https://camel.apache.org/manual/latest/filter-eip.html)

![](https://camel.apache.org/manual/latest/_images/eip/MessageFilter.gif)

```java
from("direct:a")
    .filter(simple("${header.foo} == 'bar'"))
        .to("direct:b");

from("direct:start").
        filter().xpath("/person[@name='James']").
        to("mock:result");

from("direct:start")
    .filter().method(MyBean.class, "isGoldCustomer")
      .to("mock:gold")
    .end()
    .to("mock:all");

public static class MyBean {
    public boolean isGoldCustomer(@Header("level") String level) {
        return level.equals("gold");
    }
}
```

使用.stop\(\);

**Exchange.FILTER\_MATCHED**

