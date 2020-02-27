# MethodInvokingJobDetailFactoryBean

* job
* MethodInvokingJobDetailFactoryBean
* trigger
* scheduler

```markup
<!-- 使用MethodInvokingJobDetailFactoryBean来创建作业对象 -->
<bean id="exampleJob" class="com.earl.quartz.spring.job.ExampleJob"/>
<bean id="methodInvokingJobDetailFactoryBeanExample" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
    <!-- 目标对象，指的是作业任务的实现类 -->
    <property name="targetObject" ref="exampleJob"/>
    <!-- 目标方法，指的是指定实现类中的哪个方法作为调度时的执行方法 -->
    <property name="targetMethod" value="execute"/>
    <!-- 是否并发 -->
    <property name="concurrent" value="false"/>
</bean>

<bean id="simpleTrigger" class="org.springframework.scheduling.quartz.SimpleTriggerFactoryBean">
    <!-- 这里的JobDetail指的就是我们配置的作业任务的bean -->
    <property name="jobDetail" ref="methodInvokingJobDetailFactoryBeanExample" />
    <!-- 延迟5秒开始 -->
    <property name="startDelay" value="5000"></property>
    <!-- 每3秒重复一次 -->
    <property name="repeatInterval" value="3000"></property>     
</bean>

<bean id="startQuartz" class="org.springframework.scheduling.quartz.SchedulerFactoryBean" lazy-init="false">
    <!--指定使用哪些触发器，spring会去调度触发相应的触发器，进而对作业任务进行调度处理-->
    <property name="triggers">
        <list>
            <ref bean="simpleTrigger"/>
        </list>
    </property>
</bean>
```

