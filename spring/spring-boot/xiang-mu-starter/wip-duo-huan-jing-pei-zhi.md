# 多环境配置

## 1. Introduction

* Environment
  * Profile：dev,test,uat,prod
  * Properties：\(key,value\)

## 2. Profile用法

### 2.1 多个配置文件

```bash
application.yml
application-dev.yml
application-test.yml
application-uat.yml
application-prod.yml
```

* 加载按照{profile}来，具有高优先级

### 2. @Profile

Any `@Component`, `@Configuration` or `@ConfigurationProperties` can be marked with `@Profile` to limit when it is loaded

```java
@Configuration(proxyBeanMethods = false)
@Profile("production")
public class ProductionConfiguration {

    // ...
}
```

## 3. Properties用法

### 3.1 Source种类

* properties文件
* yaml文件
* json文件
* 环境变量
* 命令行参数
* 自定义数据源
* 配置中心

### 3.2 如何使用Property?

* 单个值，可以通过`@Value("${}")`注入；
* 结构化对象，可以通过`@ConfigurationProperties`来注入；







