# Configuration

* properties
* settings
* typeAliases
* typeHandlers
* plugins
* mappers 

完整的XML配置結構：

```markup
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE configuration  
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-config.dtd">  
<!-- 配置文件的根元素 -->  
<configuration>  
    <!-- 属性：定义配置外在化 -->  
    <properties></properties>  
    <!-- 设置：定义mybatis的一些全局性设置 -->  
    <settings>  
       <!-- 具体的参数名和参数值 -->  
       <setting name="" value=""/>   
    </settings>  
    <!-- 类型名称：为一些类定义别名 -->  
    <typeAliases></typeAliases>  
    <!-- 类型处理器：定义Java类型与数据库中的数据类型之间的转换关系 -->  
    <typeHandlers></typeHandlers>  
    <!-- 对象工厂 -->  
    <objectFactory type=""></objectFactory>  
    <!-- 插件：mybatis的插件,插件可以修改mybatis的内部运行规则 -->  
    <plugins>  
       <plugin interceptor=""></plugin>  
    </plugins>  
    <!-- 环境：配置mybatis的环境 -->  
    <environments default="">  
       <!-- 环境变量：可以配置多个环境变量，比如使用多数据源时，就需要配置多个环境变量 -->  
       <environment id="">  
          <!-- 事务管理器 -->  
          <transactionManager type=""></transactionManager>  
          <!-- 数据源 -->  
          <dataSource type=""></dataSource>  
       </environment>   
    </environments>  
    <!-- 数据库厂商标识 -->  
    <databaseIdProvider type=""></databaseIdProvider>  
    <!-- 映射器：指定映射文件或者映射类 -->  
    <mappers></mappers>  
</configuration>
```

Ref：[http://www.mybatis.org/mybatis-3/zh/configuration.html](http://www.mybatis.org/mybatis-3/zh/configuration.html)

## 1. properties

配置屬性

## 2. settings

重要的全局設置

## 3. typeAliases

類型別名，為了簡化

## 4. typeHandlers

類型處理器，即類型轉換器。

Eg:EnumTypeHandler\(存储枚举的名称\),EnumOrdinalTypeHandler\(存储枚举的索引\)

## 5. plugins

MyBatis允许你在已映射的语句执行过程中的某一点进行拦截调用。

## 6. mappers

```markup
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

```text
mybatis:
  type-aliases-package: com.wingontravel.moto.motorest.domain.entity
  mapper-locations: classpath:mapper/*.xml
```

