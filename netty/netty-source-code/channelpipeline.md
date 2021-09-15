# ChannelPipeline

Inbound event **propagation** methods: 

* ChannelHandlerContext.fireChannelRegistered\(\) 
* ChannelHandlerContext.fireChannelActive\(\) 
* ChannelHandlerContext.fireChannelRead\(Object\) 
* ChannelHandlerContext.fireChannelReadComplete\(\) 
* ChannelHandlerContext.fireExceptionCaught\(Throwable\) 
* ChannelHandlerContext.fireUserEventTriggered\(Object\) 
* ChannelHandlerContext.fireChannelWritabilityChanged\(\) 
* ChannelHandlerContext.fireChannelInactive\(\) 
* ChannelHandlerContext.fireChannelUnregistered\(\) 

Outbound event **propagation** methods: 

* ChannelHandlerContext.bind\(SocketAddress, ChannelPromise\) 
* ChannelHandlerContext.connect\(SocketAddress, SocketAddress, ChannelPromise\) 
* ChannelHandlerContext.write\(Object, ChannelPromise\) 
* ChannelHandlerContext.flush\(\) 
* ChannelHandlerContext.read\(\) 
* ChannelHandlerContext.disconnect\(ChannelPromise\) 
* ChannelHandlerContext.close\(ChannelPromise\) 
* ChannelHandlerContext.deregister\(ChannelPromise\)

![](../../.gitbook/assets/image%20%2819%29.png)

```java
   传播示例
   public class MyInboundHandler extends ChannelInboundHandlerAdapter {
        @Override
       public void channelActive(ChannelHandlerContext ctx) {
           System.out.println("Connected!");
           ctx.fireChannelActive();
       }
   }
  
   public class MyOutboundHandler extends ChannelOutboundHandlerAdapter {
        @Override
       public void close(ChannelHandlerContext ctx, ChannelPromise promise) {
           System.out.println("Closing ..");
           ctx.close(promise);
       }
   }
   
```

## DefaultChannelPipeline

### Constructor

```java
final AbstractChannelHandlerContext head;
final AbstractChannelHandlerContext tail;

    protected DefaultChannelPipeline(Channel channel) {
        this.channel = ObjectUtil.checkNotNull(channel, "channel");
        succeededFuture = new SucceededChannelFuture(channel, null);
        voidPromise =  new VoidChannelPromise(channel, true);

        tail = new TailContext(this);
        head = new HeadContext(this);

        head.next = tail;
        tail.prev = head;
    }
```

* head , tail ,  双向链表的头尾

### AddFirst

```java
public final ChannelPipeline addFirst(EventExecutorGroup group, String name, ChannelHandler handler) {
        final AbstractChannelHandlerContext newCtx;
        synchronized (this) {
            checkMultiplicity(handler);
            name = filterName(name, handler);

            newCtx = newContext(group, name, handler);

            addFirst0(newCtx);

            // If the registered is false it means that the channel was not registered on an eventLoop yet.
            // In this case we add the context to the pipeline and add a task that will call
            // ChannelHandler.handlerAdded(...) once the channel is registered.
            if (!registered) {
                newCtx.setAddPending();
                callHandlerCallbackLater(newCtx, true);
                return this;
            }

            EventExecutor executor = newCtx.executor();
            if (!executor.inEventLoop()) {
                callHandlerAddedInEventLoop(newCtx, executor);
                return this;
            }
        }
        callHandlerAdded0(newCtx);
        return this;
    }

    private void addFirst0(AbstractChannelHandlerContext newCtx) {
        AbstractChannelHandlerContext nextCtx = head.next;
        newCtx.prev = head;
        newCtx.next = nextCtx;
        head.next = newCtx;
        nextCtx.prev = newCtx;
    }
```

* 检查是否被添加了，isSharable && added状态
* 检查名称，不允许重复；如果为空，就生成一个
* 生成一个新的Context，为当前的Handler
* 选择一个EventLoop
* 头结点插入newContext
* 通知HandlerAdded

### Read

```java
 @Override
    public final ChannelPipeline fireChannelRead(Object msg) {
        AbstractChannelHandlerContext.invokeChannelRead(head, msg);
        return this;
    }

    @Override
    public final ChannelPipeline fireChannelReadComplete() {
        AbstractChannelHandlerContext.invokeChannelReadComplete(head);
        return this;
    }
```

* 回到Context去读取了，head表名Unsafe
* 读完了，继续传递



