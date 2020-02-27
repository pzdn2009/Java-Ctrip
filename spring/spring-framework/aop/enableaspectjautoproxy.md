# EnableAspectJAutoProxy

启用AspectJ自动代理：

* 参数
* 注册原理
* 示例

## 參數

* exposeProxy=true，表示通过aop框架暴露该代理对象，aopContext能够访问.
* proxyTargetClass=true，使用CGLIB

它导入 AspectJAutoProxyRegister.class对象， 该对象是基于注解@EnableAspectJAutoProxy注册一个AnnotationAwareAspectJAutoProxyCreator。

该对象通过调用AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary\(registry\);

注册一个aop代理对象生成器。

## AspectJAutoProxyRegistrar

```java
/**
 * Registers an {@link org.springframework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator
 * AnnotationAwareAspectJAutoProxyCreator} against the current {@link BeanDefinitionRegistry}
 * as appropriate based on a given @{@link EnableAspectJAutoProxy} annotation.
 */
class AspectJAutoProxyRegistrar implements ImportBeanDefinitionRegistrar {

    /**
     * Register, escalate, and configure the AspectJ auto proxy creator based on the value
     * of the @{@link EnableAspectJAutoProxy#proxyTargetClass()} attribute on the importing
     * {@code @Configuration} class.
     */
    @Override
    public void registerBeanDefinitions(
            AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

        AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry);

        AnnotationAttributes enableAspectJAutoProxy =
                AnnotationConfigUtils.attributesFor(importingClassMetadata, EnableAspectJAutoProxy.class);
        if (enableAspectJAutoProxy != null) {
            if (enableAspectJAutoProxy.getBoolean("proxyTargetClass")) {
                AopConfigUtils.forceAutoProxyCreatorToUseClassProxying(registry);
            }
            if (enableAspectJAutoProxy.getBoolean("exposeProxy")) {
                AopConfigUtils.forceAutoProxyCreatorToExposeProxy(registry);
            }
        }
    }

}
```

## 示例

```java
@Aspect
@Component
@EnableAspectJAutoProxy(proxyTargetClass = true)
@Slf4j
public class SigmaRetryAspect {

    public SigmaRetryAspect() {
        log.debug("RetryerAspect已被加載");
    }

    @Around(value = "@annotation(sigmaRetry)")
    public Object around(ProceedingJoinPoint pjp, SigmaRetry sigmaRetry) throws IllegalAccessException, InstantiationException {

        Object result = RetryUtil.retry(() -> {
            try {
                return pjp.proceed();
            } catch (Throwable throwable) {
                return null;
            }
        }, (RetryConfig) sigmaRetry.config().newInstance());

        return result;
    }
}
```

