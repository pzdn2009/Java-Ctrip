# Set Header EIP

The SetHeader EIP allows you to set and header on your exchange.

选项：

* name：必须。 Name of message header。

```java
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        from("direct:a")
            .setHeader("myHeader", constant("test"))
            .to("direct:b");
    }
};
```

