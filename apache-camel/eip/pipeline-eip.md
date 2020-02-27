# Pipeline EIP

链式处理。

选项：无。

```java
from("activemq:SomeQueue")
    .pipeline()
      .bean("foo")
      .bean("bar")
      .to("acitvemq:OutputQueueu");
```

**The pipeline is the default mode**, which can be omitted, and therefore you almost often write as:

```java
from("activemq:SomeQueue")
    .bean("foo")
    .bean("bar")
    .to("acitvemq:OutputQueueu");
```

