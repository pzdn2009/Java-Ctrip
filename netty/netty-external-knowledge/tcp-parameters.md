# WIP:TCP Parameters

* SO\_RCVBUF
  * 
* SO\_SNDBUF
  * 
* TCP\_NODELAY
  * 
* SO\_KEEPALIVE
  * 
* SO\_REUSEADDR
  * 
* SO\_LINGER
  * 
* SO\_BACKLOG
  * [http://www.ideabuffer.cn/2018/02/22/TCP%E7%9A%84%E8%BF%9E%E6%8E%A5%E9%98%9F%E5%88%97%E4%B8%8Ebacklog%E5%8F%82%E6%95%B0/](http://www.ideabuffer.cn/2018/02/22/TCP%E7%9A%84%E8%BF%9E%E6%8E%A5%E9%98%9F%E5%88%97%E4%B8%8Ebacklog%E5%8F%82%E6%95%B0/)
  * [https://zhuanlan.zhihu.com/p/57869001](https://zhuanlan.zhihu.com/p/57869001)
* SO\_BROADCAST



* ChannelOption.SO\_BACKLOG, 1024

       BACKLOG用于构造服务端[套接字](https://www.baidu.com/s?wd=%E5%A5%97%E6%8E%A5%E5%AD%97&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)ServerSocket对象，标识当服务器请求处理线程全满时，用于临时存放已完成[三次握手](https://www.baidu.com/s?wd=%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)的请求的队列的最大长度。如果未设置或所设置的值小于1，Java将使用[默认值](https://www.baidu.com/s?wd=%E9%BB%98%E8%AE%A4%E5%80%BC&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)50。

* ChannelOption.SO\_KEEPALIVE, true

      是否启用心跳保活机制。在双方TCP[套接字](https://www.baidu.com/s?wd=%E5%A5%97%E6%8E%A5%E5%AD%97&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)建立连接后（即都进入ESTABLISHED状态）并且在两个小时左右上层没有任何数据传输的情况下，这套机制才会被激活。

* ChannelOption.TCP\_NODELAY, true

 在[TCP/IP协议](https://www.baidu.com/s?wd=TCP%2FIP%E5%8D%8F%E8%AE%AE&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)中，无论发送多少数据，总是要在数据前面加上协议头，同时，对方接收到数据，也需要发送ACK表示确认。为了尽可能的利用[网络带宽](https://www.baidu.com/s?wd=%E7%BD%91%E7%BB%9C%E5%B8%A6%E5%AE%BD&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)，TCP总是希望尽可能的发送足够大的数据。这里就涉及到一个名为Nagle的算法，该算法的目的就是为了尽可能发送大块数据，避免网络中充斥着许多小数据块。

 TCP\_NODELAY就是用于启用或关于[Nagle算法](https://www.baidu.com/s?wd=Nagle%E7%AE%97%E6%B3%95&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)。如果要求高实时性，有数据发送时就马上发送，就将该选项设置为true关闭[Nagle算法](https://www.baidu.com/s?wd=Nagle%E7%AE%97%E6%B3%95&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YYPy7WPjK9ujwBPH7BPj6s0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWcsPjRLrjR1nHfdnWbYnjnsr0)；如果要减少发送次数减少网络交互，就设置为false等累积一定大小后再发送。默认为false。

4.ChannelOption.SO\_REUSEADDR, true

```text
SO_REUSEADDR允许启动一个监听服务器并捆绑其众所周知端口，即使以前建立的将此端口用做他们的本地端口的连接仍存在。这通常是重启监听服务器时出现，若不设置此选项，则bind时将出错。
SO_REUSEADDR允许在同一端口上启动同一服务器的多个实例，只要每个实例捆绑一个不同的本地IP地址即可。对于TCP，我们根本不可能启动捆绑相同IP地址和相同端口号的多个服务器。
SO_REUSEADDR允许单个进程捆绑同一端口到多个套接口上，只要每个捆绑指定不同的本地IP地址即可。这一般不用于TCP服务器。
SO_REUSEADDR允许完全重复的捆绑：当一个IP地址和端口绑定到某个套接口上时，还允许此IP地址和端口捆绑到另一个套接口上。一般来说，这个特性仅在支持多播的系统上才有，而且只对UDP套接口而言（TCP不支持多播）

5.ChannelOption.SO_RCVBUF  AND  ChannelOption.SO_SNDBUF 
定义接收或者传输的系统缓冲区buf的大小，

6.ChannelOption.ALLOCATOR 
Netty4使用对象池，重用缓冲区
bootstrap.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
bootstrap.childOption(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
```

