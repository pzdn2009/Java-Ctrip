# 本地消息表

目的：解决服务A完成了通知服务B的情况。 背景：服务A和服务B是两个独立的微服务系统，但需要协调在一起工作。两阶段提交会严重影响性能，故采用最终一致性的方案。

特点：基于事件和消息队列。此问题可以抽象为一个模块。

SA--&gt;SB，且不需要SB返回值的模型。

宕机、网络抖动、网络超时、单向调用，使用与此模型。

## 组成

* SA
* SB
* MQ
* LocalDB
* Schedule
* MessageProducer
* MessageConsumer

步骤： SA中启动

```sql
begin transaction
try
  sqls...
  try
    push msg
  catch
    insert to localmessage
  commit
catch
  rollback
end
```

ScheduleA：

```text
for 3 times
  try
    read localmessage
    push msg
    delete or set sended
    break;
  catch
    cnt++
end for
if cnt > 3
   send email localmessage
```

MQ： 提供持久化支持

ServiceB：

```text
read msg;
begin tx
  save consumer_message
  commit;
end;
ack;
```

ScheduleB：

```text
read consumer_message
begin tx
  business,call Service B API
  commit
end
set message processed
```

## 实现

EventPublish是记录待发布事件的表. 其中: id: 每个事件在创建的时候都会生成一个全局唯一ID, 例如UUID. status: 事件状态, 枚举类型. 现在只有两个状态: 待发布\(NEW\), 已发布\(PUBLISHED\). payload: 事件内容. 这里我们会将事件内容转成json存到这个字段里. eventType: 事件类型, 枚举类型. 每个事件都会有一个类型, 比如我们之前提到的创建用户USER\_CREATED就是一个事件类型. EventProcess是用来记录待处理的事件. 字段与EventPublish基本相同.

![](http://skaka.me/images/custom/20160421/event_sequence1.png)

![](http://skaka.me/images/custom/20160421/event_sequence2.png)

Ref:[http://blog.csdn.net/pingyan158/article/details/52764286](http://blog.csdn.net/pingyan158/article/details/52764286)

