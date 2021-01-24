# Startup FailureAnalyzer

## 1. Introduction

`FailureAnalyzer` 用于启动失败分析。 其目的是明确了给出错误的一些定位方向，让我们更容易定位清楚错误的来源。比如端口被占用，就很明显。

定义抽象简洁——_异常给我，我帮你分析一下_。

```java
@FunctionalInterface
public interface FailureAnalyzer {

	/**
	 * Returns an analysis of the given {@code failure}, or {@code null} if no analysis
	 * was possible.
	 * @param failure the failure
	 * @return the analysis or {@code null}
	 */
	FailureAnalysis analyze(Throwable failure);
}
```

典型的：

* 端口被占用`PortInUseFailureAnalyzer`
* 数据源无url`ConnectionFactoryBeanCreationFailureAnalyzer`

## 2. 自定义Steps

### 2.1 创建分析器

```java
public class NullPointerFailureAnalyzer extends AbstractFailureAnalyzer<NullPointerException> {

    @Override
    protected FailureAnalysis analyze(Throwable rootFailure, NullPointerException cause) {
        return new FailureAnalysis(cause.getMessage(), "Please check null pointer!", cause);
    }
}
```

### 2.2 META-INF/spring.factories中注册

```yaml
org.springframework.boot.diagnostics.FailureAnalyzer=\
your.NullPointerFailureAnalyzer
```

### 2.3 测试

```text
***************************
APPLICATION FAILED TO START
***************************

Description:

null

Action:

Please check null pointer!
```

## 3. Summary

* 定义异常分析器
* 定义提示信息
  * 可以给出明确的消息
  * 给出操作

## 4. Reference



{% embed url="https://www.jianshu.com/p/fd1d85c584cb" %}



