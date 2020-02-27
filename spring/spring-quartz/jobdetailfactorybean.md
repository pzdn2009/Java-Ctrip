# JobDetailFactoryBean

* QuartzJobBean 
* XML

A Spring FactoryBean for creating a Quartz JobDetail instance, supporting bean-style usage for **JobDetail** configuration.

實現Job：QuartzJobBean

```java
public class SimpleJob extends QuartzJobBean {

    @Override
    protected void executeInternal(JobExecutionContext arg0)
            throws JobExecutionException {
        System.out.println("现在时间为："+new Date());
        //可以通过上下文获取到JobDataMap，这里面可以存放一些参数类型的数据
        JobDataMap dataMap=arg0.getMergedJobDataMap();
        String wish=(String) dataMap.get("wish");
        System.out.println(wish);
    }
}
```

配置XML：

```markup
<bean id="jobDetailFactoryBeanExample" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
    <!-- 
        参考源码，我们可以看到属性jobClass为Class类型，所以不能使用ref来引用一个bean，否则就会因为不能将bean转换为Class类型而出现异常。
        <property name="jobClass" ref="simpleJob"/>
        必须使用value对jobClass赋值。 
        <property name="jobClass" value="com.earl.quartz.spring.job.SimpleJob"/>    
    -->
    <property name="jobClass" value="com.earl.quartz.spring.job.SimpleJob"/>
    <!-- 这里设置的jobDataAsMap可以传递一些参数给作业任务 -->
    <property name="jobDataAsMap">
        <map>
            <entry key="wish" value="hello"/>
        </map>
    </property>
</bean>

<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
    <!-- 这里的JobDetail指的就是我们配置的作业任务的bean -->
    <property name="jobDetail" ref="jobDetailFactoryBeanExample"/>
    <!--cronExpression，cron表达式-->
    <property name="cronExpression" value="40 52 17 * * ?"/>
</bean>

<bean id="startQuartz" class="org.springframework.scheduling.quartz.SchedulerFactoryBean" lazy-init="false">
    <!--指定使用哪些触发器，spring会去调度触发相应的触发器，进而对作业任务进行调度处理-->
    <property name="triggers">
        <list>
            <ref bean="cronTrigger"/>
        </list>
    </property>
</bean>
```

