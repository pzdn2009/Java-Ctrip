# 核心概念

Ref: [https://camel.apache.org/manual/latest/book-getting-started.html](https://camel.apache.org/manual/latest/book-getting-started.html)

## ENDPOINT

* A JMS queue.
* A web service.
* A file. A file may sound like an unlikely type of endpoint, until you realize that in some systems one application might write information to a file and, later, another application might read that file.
* An FTP server.
* An email address. A client can send a message to an email address, and a server can read an incoming message from a mail server.
* A POJO \(plain old Java object\).

## CAMELCONTEXT

CamelContext是一个容器。 你可以把它看作Camel的运行时系统，它把所有的东西放在一起。

CamelContext提供了许多有用的服务，最值得注意的是组件，类型转换器，注册表，端点，路由，数据格式和语言。

## CAMELTEMPLATE

The CamelTemplate class is a thin wrapper around the CamelContext class。对camelContext的Wrapper。

## THE MEANING OF URL, URI, URN AND IRI

The syntax of a URN is `urn:<scheme-name>:<unique-identifier>`

## COMPONENTS

组件是创建EndPoint的。

```java
myCamelContext.getEndpoint("pop3://john.smith@mailserv.example.com?password=myPassword");
```

## MESSAGE AND EXCHANGE

The Message interface provides an abstraction for a single message, such as a request, reply or exception message. 消息接口提供对单一消息的抽象，比如请求，响应 或者异常消息。

The Exchange interface provides an abstraction for an exchange of messages, that is, a request message and its corresponding reply or exception message. In Camel terminology, the request, reply and exception messages are called **in**, **out** and **fault** messages. Exchange接口提供了消息通道的抽象。

## PROCESSOR

消息处理器

ChoiceProcessor

void process\(Exchange exchange\) throws Exception;

处理器是一个核心Camel概念，代表了一个能够使用，创建或修改传入交换的节点。 在路由期间，交换从一个处理器流向另一个; 因此，您可以将路由视为具有专用处理器作为节点的图形，以及将一个处理器的输出连接到另一个处理器的输入的线路。 许多处理器都是EIP的实现，但是可以轻松实现自己的定制处理器并将其插入到路由中。

## ROUTES, ROUTEBUILDERS AND JAVA DSL

A **route** is the step-by-step movement of a Message from an input queue, through arbitrary types of decision making \(such as filters and routers\) to a destination queue \(if any\)

* RouteBuilder
* myCamelContext.addRoutes\(builder\);

