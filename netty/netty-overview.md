# Netty Overview

## What can netty do?

* Build a tcp server
* Build IM
* Custom protocol

## Architecture

![Figure 1 Netty Architecture](../.gitbook/assets/image%20%2811%29.png)

### Features

#### Design设计

* **Unified API for various transport types - blocking and non-blocking socket**
* 为多种传输类型提供统一的API，包括阻塞和非阻塞Socket
* **Based on a flexible and extensible event model which allows clear separation of concerns**
* 基于灵活和扩展的事件模型，职责清晰
* **Highly customizable thread model - single thread, one or more thread pools such as SEDA**
* 高度订制化线程模型，覆盖单线程，一个或多个线程池，如SEDA
* True connectionless datagram socket support \(since 3.1\)

#### Ease of use易于使用

* **Well-documented Javadoc, user guide and examples**
* 提供全面的Javadoc，包括用户指南和示例，尤其在代码中注释很详尽
* No additional dependencies, JDK 5 \(Netty 3.x\) or 6 \(Netty 4.x\) is enough
  * Note: Some components such as HTTP/2 might have more requirements. Please refer to [the Requirements page](https://netty.io/wiki/requirements.html) for more information.

#### Performance性能

* **Better throughput, lower latency**
* 更好的吞吐量，低延迟
* **Less resource consumption**
* 更少的资源消耗
* **Minimized unnecessary memory copy**
* 最小化不必要的内存拷贝

#### Security安全

* Complete SSL/TLS and StartTLS support

#### Community社区

* Release early, release often
* The author has been writing similar frameworks **since 2003** and he still finds your feed back precious!
* 20年！！！

### Docs

Official Doc: [https://netty.io/](https://netty.io/) 

Github: [https://github.com/netty/netty](https://github.com/netty/netty) 

Blog: [https://juejin.cn/post/6844903704668160008](https://juejin.cn/post/6844903704668160008)

## Core Concepts

### Tables

Tags

* THRD -- Thread
* BUF -- Buffer
* CHNL -- Channel
* WRPP -- Wrapper

|  | Tags | Description |
| :--- | :--- | :--- |
| ChannelFuture | THRD | The result of an asynchronous Channel I/O operation. |
| ChannelPromise | THRD | Special ChannelFuture which is writable. |
| Bootstrap | WRPP | A Bootstrap that makes it easy to bootstrap a Channel to use for clients. |
| ServerBootstrap | WRPP | Bootstrap sub-class which allows easy bootstrap of ServerChannel |
| ByteBuf | BUF | This interface provides an abstract view for one or more primitive byte arrays \(byte\[\]\) and NIO buffers. |
| EventLoopGroup | THRD | Special EventExecutorGroup which allows registering Channels that get processed for later selection during the event loop. |
| EventLoop | THRD | Will handle all the I/O operations for a Channel once registered. One EventLoop instance will usually handle more than one Channel but this may depend on implementation details and internals. |
| Channel | CHNL | A nexus to a network socket or a component which is capable of I/O operations such as read, write, connect, and bind.      A channel provides a user: the current state of the channel \(e.g. is it open? is it connected?\), the configuration parameters of the channel \(e.g. receive buffer size\), the I/O operations that the channel supports \(e.g. read, write, connect, and bind\), and the ChannelPipeline which handles all I/O events and requests associated with the channel. |
| ChannelPipeline | CHNL | A list of ChannelHandlers which handles or intercepts **inbound** **events** and **outbound** **operations** of a Channel. ChannelPipeline implements an advanced form of the Intercepting Filter pattern to give a user full control over how an event is handled and how the ChannelHandlers in a pipeline interact with each other. |
| ChannelHandler | CHNL | Handles an I/O event or intercepts an I/O operation, and forwards it to its next handler in its ChannelPipeline. |
| ChannelHandlerContext | CHNL | Enables a ChannelHandler to interact with its ChannelPipeline and other handlers. Among other things a handler can notify the next ChannelHandler in the ChannelPipeline as well as modify the ChannelPipeline it belongs to dynamically. |
| ChannelOption | CHNL | A ChannelOption allows to configure a ChannelConfig in a type-safe way. Which ChannelOption is supported depends on the actual implementation of ChannelConfig and may depend on the nature of the transport it belongs to. |
| Selector | IO | A multiplexor of SelectableChannel objects. |
|  |  |  |

### Nature Language

> 一个Application，包括Client和Server。
>
> Client发起连接，Server绑定本地端口，接收连接。
>
> 当连接成功时，进行Read、Write操作，接收和发送数据。
>
> 收到数据之后，进行业务逻辑处理，并响应。



## Relative Techs

### Built in Tech

#### Pooling池化

#### JAVA NIO

#### Thread

### Eco Tech

#### Reactor Pattern

#### Zero Copy

#### TCP Protocol

## Roadmap of learning

### Phrase 1 

* Echo Server
* IO Knowledge
* Reactor Pattern
* Deep in Channel
* EmbeddedChannel
* Simple Client-Server Project

### Phase 2





