# Integration swagger

## 1. dependency

在pom.xml中添加：

```markup
<dependency>  
    <groupId>io.springfox</groupId>  
    <artifactId>springfox-swagger2</artifactId>  
    <version>2.6.1</version>  
</dependency>  
<dependency>  
    <groupId>io.springfox</groupId>  
    <artifactId>springfox-swagger-ui</artifactId>  
    <version>2.6.1</version>  
</dependency>
```

## 2. EnableSwagger2

```java
@Configuration  
@EnableSwagger2  
public class Swagger2 {  
    @Bean  
    public Docket createRestApi(){  
        return  new Docket(DocumentationType.SWAGGER_2)  
                .apiInfo(apiInfo())  
                .select()  
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))  
                .paths(PathSelectors.any())  
                .build();  
    }  


    private ApiInfo apiInfo(){  
        return new ApiInfoBuilder()  
                .title("支付網關")  
                .description("Some apples.")  
                .termsOfServiceUrl("http://terms-of-services.url")  
                .version("1.0")  
                .build();  
    }  
}
```

## 3. Usage

* @Api

  描述Controller

* @ApiOperation\(value="接口描述"\)

  描述Action

* @ApiModel

  描述DTO

* @ApiModelProperty\(value = "状态码", example="40001", required = true, position=-2\)

  描述DTO的屬性

## 4. Docs

[https://github.com/springfox/springfox](https://github.com/springfox/springfox) [http://springfox.github.io/springfox/docs/current/](http://springfox.github.io/springfox/docs/current/)

oauth：[https://github.com/springfox/springfox-oath2-demo/blob/master/src/main/java/myprojects/sample/oauth/config/SwaggerConfig.java](https://github.com/springfox/springfox-oath2-demo/blob/master/src/main/java/myprojects/sample/oauth/config/SwaggerConfig.java)

