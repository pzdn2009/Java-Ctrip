# QuickStart

_in spring boot_

## 1. dependency

分別是： 1. SpringBootStarter的配置； 2. jsr310，支持LocalDateTime API； 3. generator，用於從DB生成Mapper；

其中，只有Starter是必須的。

```markup
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>

<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-typehandlers-jsr310</artifactId>
    <version>1.0.2</version>
</dependency>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>

        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.5</version>
            <dependencies>
                <dependency>
                    <groupId>com.microsoft.sqlserver</groupId>
                    <artifactId>mssql-jdbc</artifactId>
                    <version>6.2.2.jre8</version>
                </dependency>
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.5</version>
                </dependency>
            </dependencies>
            <executions>
                <execution>
                    <id>Generate MyBatis Artifacts</id>
                    <phase>package</phase>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!--允许移动生成的文件 -->
                <verbose>true</verbose>
                <!-- 是否覆盖 -->
                <overwrite>true</overwrite>
                <!-- 自动生成的配置 -->
                <configurationFile>
                    src/main/resources/mybatis-generator.xml</configurationFile>
            </configuration>
        </plugin>

    </plugins>
</build>
```

## 2. application配置

```text
mybatis:
  type-aliases-package: com.***.domain.entity
  mapper-locations: classpath:mapper/*.xml
```

* 配置包；
* 映射文件

## 3. 配置掃描路勁

```java
@MapperScan("com.company.domain.mapper")
public class RestApplication extends SpringBootServletInitializer{

    public static void main(String[] args) {
        SpringApplication.run(RestApplication.class, args);
    }
}
```

## 4. Service层引用

```java
@Resource
private UserMapper userMapper;
```

## 5. Mapper文件

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.company.domain.mapper.UserMapper">
    <resultMap id="BaseResultMap" type="com.company.domain.entity.User">
        <id column="id" jdbcType="INT" property="id" />
        <result column="user_name" jdbcType="VARCHAR" property="userName" />
        <result column="password" jdbcType="VARCHAR" property="password" />
        <result column="age" jdbcType="INT" property="age" />

    </resultMap>
    <sql id="Base_Column_List">
        user_name,password,age
    </sql>
    <select id="selectByPrimaryKey" parameterType="java.lang.String" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List" />
        from t_user
        where id= #{id,jdbcType=INT}
    </select>
</mapper>
```

Generator Ref：[https://blog.csdn.net/baidu\_32877851/article/details/53959268](https://blog.csdn.net/baidu_32877851/article/details/53959268)

