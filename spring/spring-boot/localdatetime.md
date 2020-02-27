# LocalDateTime

* XML
* RegisterModule

```markup
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
    <version>2.6.0</version>
</dependency>
```

```java
public static void main(String[] args) throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.registerModule(new JavaTimeModule());
    System.out.println(objectMapper.writeValueAsString(new Entity()));
}

static class Entity {
    ZonedDateTime time = ZonedDateTime.now();

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd'T'HH:mm:ss.SSSZ")
    public ZonedDateTime getTime() {
        return time;
    }
}
```

## Hibernate 版本

Java8里面新出来了一些API，LocalDate、LocalTime、LocalDateTime 非常好用 Hibernate的版本是5.1（我这里演示的版本是5.1.2.Final）及低版本:

```markup
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-java8</artifactId>
    <version>5.1.2.Final</version>
</dependency>
```

