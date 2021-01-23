# Set Body EIP

The SetBody EIP allows you to set the body of **your exchange**.

无选项。

Eg：

```java
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        from("direct:a")
            .setBody(constant("test"))
            .to("direct:b");
    }
};
```

