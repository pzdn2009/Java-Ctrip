# Bean EIP

绑定Bean到Camel Message Exchange.

URI Format: `bean:beanID[?options]`

* beanID是可以从Registry里面查询的。

Options:

* ref String
* method String 
* beanType String 
* scope String。默认Singleton。

```java
to("bean:beanName");

// Send message to the bean endpoint
// and invoke method resolved using Bean Binding.
from("direct:start").beanRef("beanName");

// Send message to the bean endpoint
// and invoke given method.
from("direct:start").beanRef("beanName", "methodName");


// Send message to the given bean instance.
from("direct:start").bean(new ExampleBean());

// Explicit selection of bean method to be invoked.
from("direct:start").bean(new ExampleBean(), "methodName");

// Camel will create the instance of bean and cache it for you.
from("direct:start").bean(ExampleBean.class);
```

## Bean Scope

The bean **should be thread-safe** in case concurrent threads is calling the bean at the same time.

When using prototype scope, then the bean will be looked up or created per call. However in case of lookup then this is delegated to the bean registry such as Spring or CDI \(if in use\), which depends on their configuration can act as either singleton or prototype scope. so when using prototype then this depends on the delegated registry. 使用Spring集成。

