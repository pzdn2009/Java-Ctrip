# EIP

Ref:[https://camel.apache.org/manual/latest/enterprise-integration-patterns.html](https://camel.apache.org/manual/latest/enterprise-integration-patterns.html)

## 1. MESSAGING SYSTEMS

### 消息信道 Message Channel.

![](https://camel.apache.org/manual/latest/_images/eip/ChannelIcon.gif) _How does one application communicate with another using messaging?_

Connect the applications using a Message Channel, where one application writes information to the channel and the other one reads that information from the channel.

可以理解为队列。

Eg: 标识一个JMS信道：

```java
jms:queue:foo
```

### 消息 Message.

![](https://camel.apache.org/manual/latest/_images/eip/MessageIcon.gif) How can two applications connected by a message channel exchange a piece of information?

信息的载体

* id
* header
* body 
* attachment

### 管道过滤 PIPELINE and Filters

![](https://camel.apache.org/manual/latest/_images/eip/PipesAndFiltersIcon.gif)

How can we perform complex processing on a message while maintaining independence and flexibility?

### 消息路由 Message Router

![](https://camel.apache.org/manual/latest/_images/eip/ContentBasedRouterIcon.gif)

How can you decouple individual processing steps so that messages can be passed to different filters depending on a set of conditions?

在inQueue和outQueue之间。

Eg: 是一个choice路由。

```java
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        from("direct:a")
            .choice()
                .when(simple("${header.foo} == 'bar'"))
                    .to("direct:b")
                .when(simple("${header.foo} == 'cheese'"))
                    .to("direct:c")
                .otherwise()
                    .to("direct:d");
    }
};
```

### 消息转换 Message Translator.

![](https://camel.apache.org/manual/latest/_images/eip/MessageTranslatorIcon.gif)

How can systems using different data formats communicate with each other using messaging?

在**消息与消息**之间进行转换。即使用Processor。

Eg:使用Bean处理器

```java
from("activemq:SomeQueue")
  .bean("myTransformerBean", "myMethodName")
  .to("mqseries:AnotherQueue");
```

Eg：使用Velocity转换

```java
from("activemq:My.Queue")
    .to("velocity:com/acme/MyResponse.vm")
    .to("activemq:Another.Queue");
```

### 消息终结点 Message EndPoint.

![](https://camel.apache.org/manual/latest/_images/eip/MessageEndpointIcon.gif)

How does an application connect to a messaging channel to send and receive messages?

## 2. MESSAGING CHANNELS

* 点对点信道 Point To Point Channel.

How can the caller be sure that exactly one receiver will receive the document or perform the call?

* 发布订阅信道 Publish Subscribe Channel.

How can the sender broadcast an event to all interested receivers?

* 死信信道 Dead Letter Channel

What will the messaging system do with a message it cannot deliver?

## 3. MESSAGE CONSTRUCTION

### 事件消息 Event Message.

![](https://camel.apache.org/manual/latest/_images/eip/EventMessageIcon.gif) How can messaging be used to transmit events from one application to another?

使用InOnly和InOut区分。

指定为单向：

```java
foo:bar?exchangePattern=InOnly

from("mq:someQueue").
  setExchangePattern(ExchangePattern.InOnly).
  bean(Foo.class);

from("mq:someQueue").
  inOnly("mq:anotherQueue");
```

### 请求响应 Request Reply.

![](https://camel.apache.org/manual/latest/_images/eip/RequestReplyIcon.gif)

![](https://camel.apache.org/manual/latest/_images/eip/RequestReply.gif)

When an application sends a message, how can it get a response from the receiver?

For example when using JMS with InOut the component will by default perform these actions

* 默认创建一个临时inbound队列 create by default a temporary inbound queue
* set the JMSReplyTo destination on the request message
* set the JMSCorrelationID on the request message
* send the request message
* consume the response and associate the inbound message to the request using the JMSCorrelationID \(as you may be performing many concurrent request/responses\).

强制：

```java
jms:MyQueue?exchangePattern=InOut
```

### 返回地址 Return Address

How does a replier know where to send the reply?

![](https://camel.apache.org/manual/latest/_images/eip/ReturnAddressSolution.gif)

### 关联标识 Correlation Identifier.

How does a requestor that has received a reply know which request this is the reply for?

**Exchange.CORRELATION\_ID** 

```java
from("direct:start")
    .to(ExchangePattern.InOut, "jms:queue:foo?useMessageIDAsCorrelationID=true")
    .to("mock:result");
```

### 消息过期 Message Expiration.

How can a sender indicate when a message should be considered stale and thus shouldn’t be processed?

```java
  from("direct:cheese")
    .to("jms:queueu:cheese?timeToLive=5000");
```

## 4. MESSAGE ROUTING

### 基于内容路由 Content Based Router.

How do we handle a situation where the implementation of a single logical function \(e.g., inventory check\) is spread across multiple physical systems?

```java
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        errorHandler(deadLetterChannel("mock:error"));

        from("direct:a")
            .choice()
                .when(header("foo").isEqualTo("bar"))
                    .to("direct:b")
                .when(header("foo").isEqualTo("cheese"))
                    .to("direct:c")
                .otherwise()
                    .to("direct:d");
    }
};
```

### 过滤器 Message Filter.

How can a component avoid receiving uninteresting messages?

![](https://camel.apache.org/manual/latest/_images/eip/MessageFilter.gif)

**Predicate**

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

**Exchange.FILTER\_MATCHED**

### 动态路由

How can you avoid the dependency of the router on all possible destinations while maintaining its efficiency?

```java
from("direct:start")
    // use a bean as the dynamic router
    .dynamicRouter(method(DynamicRouterTest.class, "slip"));

public String slip(String body) {
    bodies.add(body);
    invoked++;

    if (invoked == 1) {
        return "mock:a";
    } else if (invoked == 2) {
        return "mock:b,mock:c";
    } else if (invoked == 3) {
        return "direct:foo";
    } else if (invoked == 4) {
        return "mock:result";
    }

    // no more so return null
    return null;
}
```

**@DynamicRouter**

* 收件人列表 Recipient List

How do we route a message to a list of \(static or dynamically\) specified recipients?

* 分割器 Splitter

How can we process a message if it contains multiple elements, each of which may have to be processed in a different way?

* 聚合器 Aggregator

How do we combine the results of individual, but related messages so that they can be processed as a whole?

* 限流 Throttler

How can I throttle messages to ensure that a specific endpoint does not get overloaded, or we don’t exceed an agreed SLA with some external service?

## 5. MESSAGE TRANSFORMATION

### 内容丰富 Content Enricher.

![](https://camel.apache.org/manual/latest/_images/eip/DataEnricher.gif)

How do we communicate with another system if the message originator does not have all the required data items available?

* 内容过滤 Content Filter.

How do you simplify dealing with a large message, when you are interested only in a few data items?

* 权限检查 Claim Check.

  How can we reduce the data volume of message sent across the system without sacrificing information content?

* 归一化 Normalizer How do you process messages that are semantically equivalent, but arrive in a different format?
* 排序 Sort. How can I sort the body of a message?
* 脚本 Script. How do I execute a script which may not change the message?
* 验证 Validate.

How can I validate a message?

## 6. MESSAGING ENDPOINTS

* 消息是配置 Message Adapter.

How do you move data between domain objects and the messaging infrastructure while keeping the two independent of each other?

* 事件驱动消费者 Event Driven Consumer.

How can an application automatically consume messages as they become available?

* 轮训消费者 Polling Consumer.

How can an application consume a message when the application is ready?

## 7. SYSTEM MANAGEMENT

