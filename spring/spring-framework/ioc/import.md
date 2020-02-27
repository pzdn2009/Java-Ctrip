# @Import

导入到IOC容器中

## 方式一

```java
@Component
/**
 *使用Import 将指定的类的实例注入至Spring 容器中,
  可以放在启动的时候导入
 */
@Import({Customer.class,Broker.class})
public class ImportDirect {
}
```

获取：

```java
public class App {
    public static void main(String[] args) throws Exception {
        ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
        System.out.println(context.getBean(ImportDirect.class));
        System.out.println(context.getBean(Customer.class));
        System.out.println(context.getBean(Broker.class));
        context.close();
    }
}
```

## 方式二

```java
package com.example.demo;
import org.springframework.context.annotation.Bean;

public class MyConfig {
    @Bean
    public Dog getDog(){
        return new Dog();
    }

    @Bean
    public Cat getCat(){
        return new Cat();
    }
}

@ComponentScan
/*导入配置类就可以了*/
@Import(MyConfig.class)
public class App {

    public static void main(String[] args) throws Exception {

        ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
        System.out.println(context.getBean(Dog.class));
        System.out.println(context.getBean(Cat.class));
        context.close();
    }
}
```

