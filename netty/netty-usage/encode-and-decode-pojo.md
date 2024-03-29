# Encode & DeCode POJO

## Java Serialization



```java
POJO implements Serializable

//server
public class ServerChannelInitializer extends ChannelInitializer<SocketChannel> {
  @Override
  protected void initChannel(SocketChannel ch) throws Exception {
    ch.pipeline().addLast(
        new ObjectDecoder(1024 * 1024, ClassResolvers.weakCachingConcurrentResolver(this.getClass().getClassLoader())));
    ch.pipeline().addLast(new ObjectEncoder());
    ch.pipeline().addLast(ServerHandler.NAME, new ServerHandler());
  }
}

  @Override
  public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    TrackingRequest subscribeReq = (TrackingRequest) msg;
    System.out.println("Received data：" + subscribeReq);
    ctx.writeAndFlush(new TrackingResponse(subscribeReq.getSchemaId(), "daniu"));
  }
  
  

//client
public class ClientChannelInitializer extends ChannelInitializer<SocketChannel> {
  @Override
  protected void initChannel(SocketChannel ch) throws Exception {

    ch.pipeline().addLast(new LoggingHandler(LogLevel.INFO));
    ch.pipeline().addLast(
        new ObjectDecoder(1024 * 1024, ClassResolvers.weakCachingConcurrentResolver(this.getClass().getClassLoader())));
    ch.pipeline().addLast(new ObjectEncoder());
    ch.pipeline().addLast(new ClientHandler());
  }
}

  @Override
  public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    TrackingResponse subscribeResp = (TrackingResponse) msg;
    System.out.println("Server response：" + subscribeResp.toString());
  }

```

 

