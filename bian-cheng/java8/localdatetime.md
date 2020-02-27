# LocalDateTime

Ref：  
[https://www.cnblogs.com/exmyth/p/6425878.html](https://www.cnblogs.com/exmyth/p/6425878.html)  
[http://blog.csdn.net/bangrenzhuce/article/details/52270232](http://blog.csdn.net/bangrenzhuce/article/details/52270232) ,（**\***）

* Instant：瞬时实例。
* LocalDate：本地日期，不包含具体时间 
* LocalTime：本地时间，不包含日期。
* LocalDateTime：组合了日期和时间，但不包含时差和时区信息。
* ZonedDateTime：最完整的日期时间，包含时区和相对UTC或格林威治的时差。

Java 8中 java.util.Date 类新增了两个方法，分别是from\(Instant instant\)和toInstant\(\)方法。

相互轉化：

```java
public class DateLocalDateTimeUtil {

    public static LocalDateTime UDateToLocalDate(Date date) {
        Instant instant = date.toInstant();
        ZoneId zone = ZoneId.systemDefault();
        return LocalDateTime.ofInstant(instant, zone);
    }

    public static Date LocalDateTimeToUdate(LocalDateTime localDateTime) {
        ZoneId zone = ZoneId.systemDefault();
        Instant instant = localDateTime.atZone(zone).toInstant();
        java.util.Date date = Date.from(instant);
        return date;
    }
}
```

```java
LocalTime localTime = localDateTime.toLocalTime();
LocalDate localDate = localDateTime.toLocalDate();

//获取当前时间
LocalDateTime.now()
//计算时间差
java.time.Duration duration = java.time.Duration.between(LocalDateTime startTime,  LocalDateTime endTime);

duration.toMinutes()    //两个时间差的分钟数
toNanos()//纳秒
toMillis()//毫秒
toMinutes()//分钟
toHours()//小时
toDays()//天数
```

## 1. Spring boot 请求参数支持？

在添加 JSR-310 模块后, 让 jackson 能够识别出 Java 8 的日期 & 时间类型

```markup
<dependency>
  <groupId>com.fasterxml.jackson.datatype</groupId>
  <artifactId>jackson-datatype-jsr310</artifactId>
</dependency>
```

启用：

```text
spring.jackson.serialization.WRITE_DATES_AS_TIMESTAMPS = false
```

## 2. 在使用Jackson时，加上

```text
@JsonDeserialize(using = LocalDateTimeDeserializer.class) 
@JsonSerialize(using = LocalDateTimeSerializer.class)
```

结果：

```text
"time": [2017,8,15,14,57,37,525000000]
```

