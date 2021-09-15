# WIP: Server Side Startup flow

## BootStrap启动分析

* Group
* Channel Or ChannelFactory
* localAddress

### How to specify the thread number?

* if 0 ,   默认处理器 \* 2

### How to 指定Group ?

```java
public ServerBootstrap group(EventLoopGroup group) {
        return group(group, group);
}

public ServerBootstrap group(EventLoopGroup parentGroup, EventLoopGroup childGroup) {
        super.group(parentGroup);
        if (childGroup == null) {
            throw new NullPointerException("childGroup");
        }
        if (this.childGroup != null) {
            throw new IllegalStateException("childGroup set already");
        }
        this.childGroup = childGroup;
        return this;
    }
```

* 可以看出，如果是只有一个线程池，则Boss和worker共用

![](../../.gitbook/assets/image%20%2817%29.png)

### How to create Channel?

```java
public B channel(Class<? extends C> channelClass) {
        if (channelClass == null) {
            throw new NullPointerException("channelClass");
        }
        return channelFactory(new ReflectiveChannelFactory<C>(channelClass));
    }

    /**
     * @deprecated Use {@link #channelFactory(io.netty.channel.ChannelFactory)} instead.
     */
    @Deprecated
    @SuppressWarnings("unchecked")
    public B channelFactory(ChannelFactory<? extends C> channelFactory) {
        if (channelFactory == null) {
            throw new NullPointerException("channelFactory");
        }
        if (this.channelFactory != null) {
            throw new IllegalStateException("channelFactory set already");
        }

        this.channelFactory = channelFactory;
        return (B) this;
    }
    
    final ChannelFuture initAndRegister() {
        Channel channel = null;
        try {
            channel = channelFactory.newChannel();
```

* 使用反射工厂创建Channel，指定Channel的Class类型
* 在initAndRegister方法中执行newChannel

### How to bind ?

```java
    public ChannelFuture bind(SocketAddress localAddress) {
        //...
        return doBind(localAddress);
    }

    private ChannelFuture doBind(final SocketAddress localAddress) {
        final ChannelFuture regFuture = initAndRegister();
        final Channel channel = regFuture.channel();
        if (regFuture.cause() != null) {
            return regFuture;
        }

        if (regFuture.isDone()) {
            // At this point we know that the registration was complete and successful.
            ChannelPromise promise = channel.newPromise();
            doBind0(regFuture, channel, localAddress, promise);
            return promise;
        } else {
            // Registration future is almost always fulfilled already, but just in case it's not.
            final PendingRegistrationPromise promise = new PendingRegistrationPromise(channel);
            regFuture.addListener(new ChannelFutureListener() {
                @Override
                public void operationComplete(ChannelFuture future) throws Exception {
                    Throwable cause = future.cause();
                    if (cause != null) {
                        // Registration on the EventLoop failed so fail the ChannelPromise directly to not cause an
                        // IllegalStateException once we try to access the EventLoop of the Channel.
                        promise.setFailure(cause);
                    } else {
                        // Registration was successful, so set the correct executor to use.
                        // See https://github.com/netty/netty/issues/2586
                        promise.registered();

                        doBind0(regFuture, channel, localAddress, promise);
                    }
                }
            });
            return promise;
        }
    }
```

* 初始化和注册Channel，`NioServerSocketChannel`
  * 使用工厂创建Channel
  * init\(Channel\)
    * 更新Options
    * 更新Attributes
    * AddLast一个ChannelInitializer，触发管道添加机制
      * 创建newContext
      * 插入到head和tail之间
      * 处理回调
  * 获取EventLoop进行注册（EventLoop和EventExecutor的区别，Loop是可以注册Channel的），最终走到Unsafe进行注册
    * NioChannel做Select
    * 回调HandlerAdded，进入到ChannelInitializer中，调用initChannel方法，并在最后添加了ServerBootStrapAcceptor，并设置自动读取
    * 并把自己删除
    * 触发注册成功时间
    * 
  * 返回regFuture

![](../../.gitbook/assets/image%20%2820%29.png)



