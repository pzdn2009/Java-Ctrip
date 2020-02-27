# ImportBeanDefinitionRegistrar

动态创建Bean的方式

* 实现接口
* 导入
* 获取

## 实现接口

```java
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
import org.springframework.core.type.AnnotationMetadata;

/**
 * registerBeanDefinitions 方法中的参数 BeanDefinitionRegistry 可以往Spring IOC Container 中动态地装配Bean
 */
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

        //手动注入 Forwards 类的实例
        BeanDefinitionBuilder beanDef_forwards = BeanDefinitionBuilder.rootBeanDefinition(Forwards.class);
        registry.registerBeanDefinition("forwards", beanDef_forwards.getBeanDefinition());

        //手动注入 Security类的实例
        BeanDefinitionBuilder beanDef_security = BeanDefinitionBuilder.rootBeanDefinition(Security.class);
        registry.registerBeanDefinition("security", beanDef_security.getBeanDefinition());
    }
}
```

## 导入实现 & 测试

```java
@Import(MyImportBeanDefinitionRegistrar.class)
@Configuration
public class App {

    public static void main(String[] args) throws Exception {
        ConfigurableApplicationContext context = SpringApplication.run(App.class, args);

        //实现ImportBeanDefinitionRegistrar 实现Bean的注入
        System.out.println(context.getBean(MyImportBeanDefinitionRegistrar.class));
        System.out.println(context.getBean(Forwards.class));
        System.out.println(context.getBean(Security.class));
    }
}
```

