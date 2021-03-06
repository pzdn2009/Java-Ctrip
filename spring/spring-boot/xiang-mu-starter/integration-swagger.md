# Integration-Swagger

## 1. Introduction

### Swagger  [https://swagger.io/](https://swagger.io/)

* OPEN API 
* Codegen
* Editor
* Swagger-UI

### Springfox [http://springfox.github.io/springfox/docs/current/](http://springfox.github.io/springfox/docs/current/)

* Spring SDK
* 被广泛使用

## 2. Usage

### 2.1 with deps

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

### 2.2 EnableSwagger2

```java
/**
* 更多配置细节：http://springfox.github.io/springfox/docs/current/
*/
@Configuration  
@EnableSwagger2  
public class Swagger2Config {
  
    //注入Docket
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

### 2.3 Controller和Model使用注解

Controller

* @Api
* @ApiOperation

```java
@Api
@RequestMapping("/api/qe")
@RestController
@Slf4j
public class QeController {

    @ApiOperation(value = "Query DP Categories by CountryCode.")
    @PostMapping("getDpCategories")
    public Object getDpCategories(@RequestBody GetDpCategoriesRequest request) {

        //todo: 
        
    }
} 
```

Model

* @ApiModel
* @ApiModelProperty

```java
@ApiModel
public class GetDpCategoriesRequest {

    @ApiModelProperty(value = "The Country Code for getting DP categories.")
    private CountryCode countryCode;
}
```

### 2.4 访问

> /swagger-ui.html

## 3. Swagger注解

* jar包：swagger-annotations
* package：`io.swagger.annotations`

| Annotation | Description |
| :--- | :--- |
| @Api | 描述Controller |
| @ApiOperation | 描述API Method |
| @ApiParam | 描述请求参数 |
| @ApiImplicitParam |  |
| @ApiResponse | 响应 |
| @ApiResponses |  |
|  |  |
| @ApiModel | 模型 |
| @ApiModelProperty | 模型属性 |

## 4. Reference

* OPEN API SPECIFICATION：[https://swagger.io/resources/open-api/](https://swagger.io/resources/open-api/)
* springFox git：[https://github.com/springfox/springfox](https://github.com/springfox/springfox)
* springFox doc：[http://springfox.github.io/springfox/docs/current/](http://springfox.github.io/springfox/docs/current/)
* oauth：[https://github.com/springfox/springfox-oath2-demo/blob/master/src/main/java/myprojects/sample/oauth/config/SwaggerConfig.java](https://github.com/springfox/springfox-oath2-demo/blob/master/src/main/java/myprojects/sample/oauth/config/SwaggerConfig.java)

