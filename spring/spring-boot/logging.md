# Logging

## Logging

## 基础篇

![](../../.gitbook/assets/springboot_logging.png)

### 1. 日志简介

Slf4j（Simple Logging Façade For Java），是一个简单日志门面抽象框架，它本身只提供了日志Facade API和一个简单的日志类实现，一般常配合**Log4j**，**LogBack**，**JdkLog**使用。

LogBack和Log4j都是开源日记工具库，LogBack是Log4j的改良版本，比Log4j拥有更多的特性，同时也带来很大性能提升。

spring-boot默认支持logback，所以无需引用任何以来只需要，配置application.properties即可。默认Console就可用，FileAPPend而可选无需做配置。

### 2. 日志格式

```text
2014-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
```

logback是没有FATAL的，映射到了ERROR。

### 3. Console输出

#### 3.1 bash

默认情况下console的被输出， 命令行启动制定日志级别：

```bash
$ java -jar myapp.jar --debug
```

debug模式启用的时候，**a selection of core loggers** \(embedded container, Hibernate and Spring Boot\) are configured to output more information.

#### 3.2 application

```text
debug:
  true
```

### 4. 文件输出

默认情况下，Spring boot 只开启Console的日志，不开启File的日志。

如何开启文件日志？如下属性至少选一个。

```text
logging.file= #文件路径，可以是相对当前目录的路径或者绝对路径
logging.path= #默认文件是spring.log。文件目录，可以是相对当前的路径或者绝对路径
```

文件的大小为10M滚动。

### 5. 日志级别level

```text
ALL < DEBUG < INFO < WARN < ERROR <FATAL < OFF
```

配置格式：‘logging.level.\*=LEVEL’，如：

```text
logging.level.root=WARN
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR
```

结合actuator动态修改level：

* 查看所有：GET [http://localhost:8091/loggers](http://localhost:8091/loggers)
* 查看包：GET [http://localhost:8091/loggers/com.l3.sigma](http://localhost:8091/loggers/com.l3.sigma)
* 修改日志级别：POST [http://localhost:8091/loggers/com.l3.sigma](http://localhost:8091/loggers/com.l3.sigma)

  Body：

  ```javascript
  {
    "configuredLevel":"DEBUG"
  }
  ```

### 6. 自定义日志扩展

| 日志系统 | 可以自定义的地方 |
| :--- | :--- |
| Logback | logback-spring.xml, logback-spring.groovy, logback.xml or logback.groovy |
| Log4j2 | log4j2-spring.xml or log4j2.xml |
| JDK \(Java Util Logging\) | logging.properties |

强烈推荐使用-spring的文件。

### 7. profile

不同的环境配置不同的日志：

```markup
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev, staging">
    <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
    <!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```

## 经验篇

### 自定義Appender的問題

Ref：[http://www.itkeyword.com/doc/9602162384580714x181/spring-boot-logback-autowire](http://www.itkeyword.com/doc/9602162384580714x181/spring-boot-logback-autowire)

自定義的AppenderBase不能和Spring框架的Bean很好地結合起來。

要重新寫。

在单利里面不能使用SpringUtil，总之就是获取不到SpringApplicationContext

最后的解决方法：addAppender

```java
   public static void addAppender() {
        LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
        JoranConfigurator jc = new JoranConfigurator();
        jc.setContext(lc);

        Appender appender = new AppenderBase<ILoggingEvent>() {
            private String systemcode = "***";
            private String source = "XXXXX";

            @Override
            protected void append(ILoggingEvent event) {
                try {
                    var args = event.getArgumentArray();
                    if (args != null && args.length == 1) {

                        if (args[0] != null && args[0].toString().startsWith("detail:")) {

                            LogEntity logEntity = new LogEntity();
                            logEntity.setDetail(args[0].toString());
                            logEntity.setSource(source);
                            logEntity.setMessage(event.getMessage());
                            logEntity.setSystemCode(systemcode);

                            logEntity.setCreateTime(LocalDateTime.now().toString() + "+08:00");

                            InetAddress addr = InetAddress.getLocalHost();
                            logEntity.setMachineName(addr.getHostName());
                            logEntity.setIpAddress(addr.getHostAddress());

                            logEntity.setProcessID(Long.parseLong(ManagementFactory.getRuntimeMXBean().getName().split("@")[0]));
                            logEntity.setProcessName(ManagementFactory.getRuntimeMXBean().getName());

                            logEntity.setThreadID(Thread.currentThread().getId());

                            logEntity.setLevel(event.getLevel());

                            LogManager.getInstance().Enqueue(logEntity);
                        }
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        };
        ch.qos.logback.classic.Logger rootLogger = lc.getLogger(
                Logger.ROOT_LOGGER_NAME);

        rootLogger.addAppender(appender);

        appender.start();
    }
```

**原理**：日志框架中有一个工厂类：LoggerFactory，在这个类中可以获取到当前日志的上下文对象LoggerContext，通过LoggerContext可以获取到指定包的Logger ,通过Logger对象那就无所不能了。

```java
LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();

ch.qos.logback.classic.Logger rootLogger = lc.getLogger(
                Logger.ROOT_LOGGER_NAME);
```

**场景**：动态修改日志对象以及内部。

**原因**：The logging system is initialized early in the application lifecycle. Consequently, logging properties are not found in property files loaded through @PropertySource annotations.

