# Server Template

## Skeleton

* Server\(main\)
* ServerHandlers
* ServerChannelInitializer

## Points

* Log
* Connection Info
* Encode & Decode
* Exception
* Thread Config 
* Monitoring
* Tcp Parameters

## Source

```java
public class Server {

  static final int PORT = Integer.parseInt(System.getProperty("port", "8912"));

  public static void main(String[] args) throws Exception {
    new Server().start();
  }

  public void start() throws InterruptedException {
    var bossGroup = new NioEventLoopGroup(1);
    var workerGroup = new NioEventLoopGroup(); //cores * 2
    try {
      var b = new ServerBootstrap();
      b.group(bossGroup, workerGroup) // Master-Slave Reactor
          .channel(NioServerSocketChannel.class)
          .handler(new LoggingHandler(LogLevel.INFO))
          .childHandler(new ServerChannelInitializer());

      // Bind and start to accept incoming connections.
      var f = b.bind(PORT).sync();

      // Wait until the server socket is closed.
      // In this example, this does not happen, but you can do that to gracefully
      // shut down your server.
      f.channel().closeFuture().sync();
    } finally {
      workerGroup.shutdownGracefully();
      bossGroup.shutdownGracefully();
    }
  }
}

```



