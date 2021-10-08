# 实践：Arthas分析高CPU

## 步骤

```text
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

找到消耗CPU多的线程

```text
dashboard
thread 35
//view code

//反编译
jad akka.actor.LightArrayRevolverScheduler

//监控
monitor -c 5 akka.actor.LightArrayRevolverScheduler$$anon$4 executeBucket$1


```

Ref： [https://blog.csdn.net/qq\_32523587/article/details/102872463](https://blog.csdn.net/qq_32523587/article/details/102872463)





