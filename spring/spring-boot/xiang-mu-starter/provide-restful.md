# WIP Restful Api

## Rest常用注解

* @**RestController** = @Controller + @ResponseBody ，作用是能夠返回Json數據。
* @**RequestMapping**\("/greeting"\)。配置路由
* @**RequestParam**\(value="name", defaultValue="World"\)。指定请求参数，queryString
* @**GetMapping**\("/"\)。GET方法映射的路由
* @**PostMapping**。Post方法映射的路由
* @**Valid**。用来校验请求体
* @**RequestBody**。请求体，form。
* **RequestMethod**.POST 。请求方法
* @**ResponseEntity**。响应体。有StatusCode以及Body。

## 各层註解\(到Bean工厂\)

1. @Controller 控制器（注入服务）
2. @Service 服务（注入dao）
3. @Repository dao（实现dao访问）
4. @Component （**把普通pojo实例化到spring容器中，相当于配置文件中的**）

@Component,@Service,@Controller,@Repository注解的类，并把这些类纳入进spring容器中管理。

下面写这个是引入component的扫描组件 在applicationContext.xml中：

```markup
<context:component-scan base-package=”com.mmnc”>
```

其中base-package为需要扫描的包（含所有子包） 1. @Service用于标注业务层组件 2. @Controller用于标注控制层组件\(如struts中的action\) 3. @Repository用于标注数据访问组件，即DAO组件. 4. @Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。

效果相当于：

```markup
<bean id="resourceApplicationService"
class="com.***.cms.domain.service.ResourceApplicationServiceImpl" scope="prototype">
</bean>
```

