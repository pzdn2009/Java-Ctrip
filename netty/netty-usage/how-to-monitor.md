# WIP：How to monitor?

{% embed url="https://emacsist.github.io/2018/04/26/netty-%E4%B8%AD%E7%9A%84%E6%B5%81%E6%8E%A7%E4%B8%8E%E7%BB%9F%E8%AE%A1/" %}



Netty 值得统计的信息  
外在信息  
连接统计信息：channelActive / channelInactive  
收数据统计：channelRead  
发数据统计：write（写到缓存里），用 ctx.write\(msg\).addListener\(\) 更准确  
异常统计：exceptionCaught / ChannelFuture  
内在信息  
线程数：根据不同实现计算，比如：nioEventLoopGroup.executorCount\(\)；  
待处理任务：executor.pendingTask\(\)；  
积累的数据：channelOutBoundBuffer.totalPendingSize，这是 Channel 级别的；  
可写状态切换：channelWritabilityChanged；  
触发事件统计：userEventTriggered，比如 IdleStateEvent；  
ByteBuf 分配细节：Pooled/UnpooledByteAllocator.DEFAULT.metric\(\)；

————————————————

https://blog.csdn.net/weixin\_33669968/java/article/details/104552393

https://community.jboss.org/wiki/Netty4AndJMXIntegration

[https://grafana.com/grafana/dashboards/4991/reviews](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgrafana.com%2Fgrafana%2Fdashboards%2F4991%2Freviews)

[https://mvnrepository.com/artifact/io.netty/netty-metrics-yammer/4.0.0.Alpha6](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fmvnrepository.com%2Fartifact%2Fio.netty%2Fnetty-metrics-yammer%2F4.0.0.Alpha6)

