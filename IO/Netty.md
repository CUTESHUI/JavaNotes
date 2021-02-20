## Netty

#### BIO、NIO、AIO区别

- **BIO (Blocking I/O)**
  - 同步阻塞 I/O 模式，数据的读取写入必须阻塞在一个线程内等待其完成
  - 在客户端连接数量不高的情况下，是没问题的。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型就无能为力了
- **NIO (Non-blocking/New I/O)** 
  - NIO 是一种同步非阻塞的 I/O 模型，于 Java 1.4 中引入，对应 java.nio 包，提供了 Channel、Selector、Buffer 等抽象
  - NIO 中的 N 可以理解为 Non-blocking，它支持面向缓冲的，基于通道的 I/O 操作方法
  -  NIO 提供了与传统 BIO 模型中的 Socket 和 ServerSocket 相对应的 SocketChannel 和 ServerSocketChannel 两种不同的套接字通道实现，两种通道都支持阻塞和非阻塞两种模式
  - 对于高负载、高并发的（网络）应用，应使用 NIO 的非阻塞模式来开发
- **AIO (Asynchronous I/O)** 
  - AIO 也就是 NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2,它是异步非阻塞的 IO 模型
  - AIO 是异步 IO 的缩写，异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作
  - 虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是同步的
  - 对于 NIO 来说，我们的业务线程是在 IO 操作准备好时，得到通知，接着就由这个线程自行进行 IO 操作，IO 操作本身是同步的，Netty 之前也尝试使用过 AIO，不过又放弃了



#### NIO

- 一个 Selector 对应一个处理线程
- 一个 Selector 上可以注册多个 Channel
- 每个 Channel 都会对应一个 Buffer（有时候一个 Channel 可以使用多个 Buffer，这时候程序要进行多个 Buffer 的分散和聚集操作），Buffer 的本质是一个内存块，底层是一个数组
- Selector 会根据不同的事件在各个 Channel 上切换
- Buffer 是双向的，既可以读也可以写，切换读写方向要调用 Buffer 的 flip()
- 同样，Channel 也是双向的，数据既可以流入也可以流出



#### 零拷贝技术

- 在 “将本地磁盘中文件发送到网络中” 这一场景中
- 直接 IO 技术
  - 内核缓冲区是 Linux 系统的 Page Cahe
  - 为了加快磁盘的 IO，Linux 系统会把磁盘上的数据以 Page 为单位缓存在操作系统的内存里
  -  Page 是 Linux 系统定义的一个逻辑概念，一个 Page 一般为 4K
  - 整个过程有四次数据拷贝，读进来两次，写回去又两次
  - 磁盘-->内核缓冲区-->应用程序；应用程序-->Socket 缓冲区-->网络
- 内存映射文件技术
  - 整个过程有三次数据拷贝，不再经过应用程序内存，直接在内核空间中从内核缓冲区拷贝到 Socket 缓冲区
  - 磁盘-->内核缓冲区（映射了应用程序的地址）-->Socket 缓冲区-->网络

- 零拷贝技术
  - 内核缓冲区到 Socket 缓冲区之间并没有做数据的拷贝，只是一个地址的映射
  - 底层的网卡驱动程序要读取数据并发送到网络上的时候，看似读取的是 Socket 的缓冲区中的数据，其实直接读的是内核缓冲区中的数据
  - 零拷贝中所谓的 “零” 指的是内存中数据拷贝的次数为 0
  - 磁盘-->内核缓冲区（映射了Socket 缓冲区的地址）-->网络



#### Netty 是什么

- Netty 是一个 **基于 NIO** 的 client-server(客户端服务器)框架，使用它可以快速简单地开发网络应用程序
- 它极大地简化并优化了 TCP 和 UDP 套接字服务器等网络编程，并且性能以及安全性等很多方面甚至都要更好
- **支持多种协议**，如 FTP、SMTP、HTTP 以及各种二进制和基于文本的传统协议

- 官方：Netty 成功地找到了一种在不妥协可维护性和性能的情况下实现易于开发，性能，稳定性和灵活性的方法



#### 为什么用 Netty

- 统一的 API，支持多种传输类型，阻塞和非阻塞的
- 零拷贝技术
- 简单而强大的线程模型
- 自带编解码器解决 TCP 粘包/拆包问题
- 自带各种协议栈
- 真正的无连接数据包套接字支持
- 比直接使用 Java 核心 API 有更高的吞吐量、更低的延迟、更低的资源消耗和更少的内存复制
- 安全性不错，有完整的 SSL/TLS 以及 StartTLS 支持
- 社区活跃
- 成熟稳定，经历了大型项目的使用和考验，而且很多开源项目都使用到了 Netty， 比如 Dubbo、RocketMQ 等



#### Netty 应用场景

理论上来说，NIO 可以做的事情 ，使用 Netty 都可以做并且更好。Netty 主要用来做网络通信 

- RPC 框架的网络通信工具
- HTTP 服务器
- 即时通讯系统
- 实现消息推送系统



#### Netty 框架

- 主要基于主从 Reactor 多线程模式，并做了一定的改进

- Netty 抽象出两组线程池

  - **BossGroup** 和 **WorkerGroup**，也可以叫做 BossNioEventLoopGroup 和 WorkerNioEventLoopGroup
    - 类型都是：NioEventLoopGroup
    - BossGroup 中的线程专门负责和客户端建立连接
    - WorkerGroup 中的线程专门负责处理连接上的读写
  - NioEventLoopGroup 相当于一个事件循环组
    - 这个组中含有多个事件循环，每个事件循环就是一个 NioEventLoop
    - NioEventLoop 表示一个不断循环的执行事件处理的线程，每个 NioEventLoop 都包含一个 Selector，用于监听注册在其上的 Socket 网络连接（Channel）
    - NioEventLoopGroup 可以含有多个线程，即可以含有多个 NioEventLoop

  - 每个 **BossNioEventLoop** 中循环执行以下三个步骤
    - select：轮训注册在其上的 ServerSocketChannel 的 accept 事件（OP_ACCEPT 事件）
    - processSelectedKeys：处理 accept 事件，与客户端建立连接，生成一个 NioSocketChannel，并将其注册到某个 WorkerNioEventLoop 上的 Selector 上
    - runAllTasks：再去以此循环处理任务队列中的其他任务
  - 每个 **WorkerNioEventLoop** 中循环执行以下三个步骤
    - select：轮训注册在其上的 NioSocketChannel 的 read/write 事件（OP_READ/OP_WRITE 事件）
    - processSelectedKeys：在对应的 NioSocketChannel 上处理 read/write 事件
    - runAllTasks：再去以此循环处理任务队列中的其他任务

  - 在以上两个 processSelectedKeys 步骤中，会使用 Pipeline（管道），Pipeline 中引用了 Channel，即通过 Pipeline 可以获取到对应的 Channel，Pipeline 中维护了很多的处理器（拦截处理器、过滤处理器、自定义处理器等）



#### 基于Netty的TCP

- 通过两段简短的代码得到了一个基于主从 Reactor 多线程模式的服务器，一个高吞吐量和并发量的服务器，一个异步处理服务器...
- 服务端

```java
public static void main(String[] args) throws InterruptedException {

    // 创建 BossGroup 和 WorkerGroup
    // 1. bossGroup 只处理连接请求
    // 2. 业务处理由 workerGroup 来完成
    EventLoopGroup bossGroup = new NioEventLoopGroup();
    EventLoopGroup workerGroup = new NioEventLoopGroup();

    try {
        // 创建服务器端的启动对象
        ServerBootstrap bootstrap = new ServerBootstrap();
        // 配置参数
        bootstrap
                // 设置线程组
                .group(bossGroup, workerGroup)
                // 说明服务器端通道的实现类（便于 Netty 做反射处理）
                .channel(NioServerSocketChannel.class)
                // 设置等待连接的队列的容量（当客户端连接请求速率 > NioServerSocketChannel 接收速率的时候，会使用该队列做缓冲）
                // option()方法用于给服务端的 ServerSocketChannel
                // 添加配置
                .option(ChannelOption.SO_BACKLOG, 128)
                // 设置连接保活
                // childOption()方法用于给服务端 ServerSocketChannel
                // 接收到的 SocketChannel 添加配置
                .childOption(ChannelOption.SO_KEEPALIVE, true)
                // handler()方法用于给 BossGroup 设置业务处理器
                // childHandler()方法用于给 WorkerGroup 设置业务处理器
                .childHandler(
                        // 创建一个通道初始化对象
                        new ChannelInitializer<SocketChannel>() {
                            // 向 Pipeline 添加业务处理器
                            @Override
                            protected void initChannel(
                                    SocketChannel socketChannel
                            ) throws Exception {
                                socketChannel.pipeline().addLast(
                                        new NettyServerHandler()
                                );
                                
                                // 可以继续调用 socketChannel.pipeline().addLast()
                                // 添加更多 Handler
                            }
                        }
                );

        System.out.println("server is ready...");

        // 绑定端口，启动服务器，生成一个 channelFuture 对象，
        // ChannelFuture 涉及到 Netty 的异步模型，后面展开讲
        ChannelFuture channelFuture = bootstrap.bind(8080).sync();
        // 对通道关闭进行监听
        channelFuture.channel().closeFuture().sync();
    } finally {
        bossGroup.shutdownGracefully();
        workerGroup.shutdownGracefully();
    }
}

/**
 * 自定义一个 Handler，需要继承 Netty 规定好的某个 HandlerAdapter（规范）
 * InboundHandler 用于处理数据流入本端（服务端）的 IO 事件
 * InboundHandler 用于处理数据流出本端（服务端）的 IO 事件
 */
static class NettyServerHandler extends ChannelInboundHandlerAdapter {
    /**
     * 当通道有数据可读时执行
     *
     * @param ctx 上下文对象，可以从中取得相关联的 Pipeline、Channel、客户端地址等
     * @param msg 客户端发送的数据
     * @throws Exception
     */
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg)
            throws Exception {
        // 接收客户端发来的数据

        System.out.println("client address: "
                + ctx.channel().remoteAddress());

        // ByteBuf 是 Netty 提供的类，比 NIO 的 ByteBuffer 性能更高
        ByteBuf byteBuf = (ByteBuf) msg;
        System.out.println("data from client: "
                + byteBuf.toString(CharsetUtil.UTF_8));
    }

    /**
     * 数据读取完毕后执行
     *
     * @param ctx 上下文对象
     * @throws Exception
     */
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx)
            throws Exception {
        // 发送响应给客户端
        ctx.writeAndFlush(
                // Unpooled 类是 Netty 提供的专门操作缓冲区的工具
                // 类，copiedBuffer 方法返回的 ByteBuf 对象类似于
                // NIO 中的 ByteBuffer，但性能更高
                Unpooled.copiedBuffer(
                        "hello client! i have got your data.",
                        CharsetUtil.UTF_8
                )
        );
    }

    /**
     * 发生异常时执行
     *
     * @param ctx   上下文对象
     * @param cause 异常对象
     * @throws Exception
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
            throws Exception {
        // 关闭与客户端的 Socket 连接
        ctx.channel().close();
    }
}
```

- 客户端

```java
public static void main(String[] args) throws InterruptedException {

    // 客户端只需要一个事件循环组，可以看做 BossGroup
    EventLoopGroup eventLoopGroup = new NioEventLoopGroup();

    try {
        // 创建客户端的启动对象
        Bootstrap bootstrap = new Bootstrap();
        // 配置参数
        bootstrap
                // 设置线程组
                .group(eventLoopGroup)
                // 说明客户端通道的实现类（便于 Netty 做反射处理）
                .channel(NioSocketChannel.class)
                // handler()方法用于给 BossGroup 设置业务处理器
                .handler(
                        // 创建一个通道初始化对象
                        new ChannelInitializer<SocketChannel>() {
                            // 向 Pipeline 添加业务处理器
                            @Override
                            protected void initChannel(
                                    SocketChannel socketChannel
                            ) throws Exception {
                                socketChannel.pipeline().addLast(
                                        new NettyClientHandler()
                                );
                                
                                // 可以继续调用 socketChannel.pipeline().addLast()
                                // 添加更多 Handler
                            }
                        }
                );

        System.out.println("client is ready...");

        // 启动客户端去连接服务器端，ChannelFuture 涉及到 Netty 的异步模型，后面展开讲
        ChannelFuture channelFuture = bootstrap.connect(
                "127.0.0.1",
                8080).sync();
        // 对通道关闭进行监听
        channelFuture.channel().closeFuture().sync();
    } finally {
        eventLoopGroup.shutdownGracefully();
    }
}

/**
 * 自定义一个 Handler，需要继承 Netty 规定好的某个 HandlerAdapter（规范）
 * InboundHandler 用于处理数据流入本端（客户端）的 IO 事件
 * InboundHandler 用于处理数据流出本端（客户端）的 IO 事件
 */
static class NettyClientHandler extends ChannelInboundHandlerAdapter {
    /**
     * 通道就绪时执行
     *
     * @param ctx 上下文对象
     * @throws Exception
     */
    @Override
    public void channelActive(ChannelHandlerContext ctx)
            throws Exception {
        // 向服务器发送数据
        ctx.writeAndFlush(
                // Unpooled 类是 Netty 提供的专门操作缓冲区的工具
                // 类，copiedBuffer 方法返回的 ByteBuf 对象类似于
                // NIO 中的 ByteBuffer，但性能更高
                Unpooled.copiedBuffer(
                        "hello server!",
                        CharsetUtil.UTF_8
                )
        );
    }

    /**
     * 当通道有数据可读时执行
     *
     * @param ctx 上下文对象
     * @param msg 服务器端发送的数据
     * @throws Exception
     */
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg)
            throws Exception {
        // 接收服务器端发来的数据

        System.out.println("server address: "
                + ctx.channel().remoteAddress());

        // ByteBuf 是 Netty 提供的类，比 NIO 的 ByteBuffer 性能更高
        ByteBuf byteBuf = (ByteBuf) msg;
        System.out.println("data from server: "
                + byteBuf.toString(CharsetUtil.UTF_8));
    }

    /**
     * 发生异常时执行
     *
     * @param ctx   上下文对象
     * @param cause 异常对象
     * @throws Exception
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
            throws Exception {
        // 关闭与服务器端的 Socket 连接
        ctx.channel().close();
    }
}
```



#### Netty 核心组件

- Buffer（缓冲区）

  - 本质上是一个可读可写的内存块，可以理解成一个容器对象，Channel 读写文件或者网络都要经由 Buffer
  - 在 Java NIO 中，Buffer 是一个顶层抽象类，它的常用子类有：
    - ByteBuffer
    - CharBuffer
    - ShortBuffer
    - IntBuffer
    - LongBuffer
    - DoubleBuffer
    - FloatBuffer
  - 其中 ByteBuffer 支持类型化的数据存取，即可以往 ByteBuffer 中放 byte 类型数据、也可以放 char、int、long、double 等类型的数据，但读取的时候要做好类型匹配处理，否则会抛出 BufferUnderflowException
  - Buffer 体系中还有一个重要的 MappedByteBuffer（ByteBuffer 的子类），可以让文件内容直接在堆外内存中被修改，而如何同步到文件由 NIO 来完成

- Channel（网络操作抽象类）

  - 双向，可读可写
  - 在 Java NIO 中，Buffer 是一个顶层接口，它的常用子类有：
    - FileChannel：用于文件读写
    - DatagramChannel：用于 UDP 数据包收发
    - ServerSocketChannel：用于服务端 TCP 数据包收发
    - SocketChannel：用于客户端 TCP 数据包收发

- EventLoop（事件循环）

- Selector（选择器）

  - Netty 的 IO 线程 NioEventLoop 聚合了 Selector，可以同时并发处理成百上千的客户端连接

  - 实现 IO 多路复用的关键，多个 Channel 注册到某个 Selector 上，当 Channel 上有事件发生时，Selector 就会取得事件然后调用线程去处理事件
  - IO 多路复用的核心：
    - 只有当连接上真正有读写等事件发生时，线程才会去进行读写等操作，这就不必为每个连接都创建一个线程，一个线程可以应对多个连接
  - 在 Java NIO 中，Selector 是一个抽象类，常用方法有：

  ```java
  public abstract class Selector implements Closeable {
      ......
      
      /**
       * 得到一个选择器对象
       */
      public static Selector open() throws IOException {
          return SelectorProvider.provider().openSelector();
      }
      ......
  
      /**
       * 返回所有发生事件的 Channel 对应的 SelectionKey 的集合，通过
       * SelectionKey 可以找到对应的 Channel
       */
      public abstract Set<SelectionKey> selectedKeys();
      ......
      
      /**
       * 返回所有 Channel 对应的 SelectionKey 的集合，通过 SelectionKey
       * 可以找到对应的 Channel
       */
      public abstract Set<SelectionKey> keys();
      ......
      
      /**
       * 监控所有注册的 Channel，当其中的 Channel 有 IO 操作可以进行时，
       * 将这些 Channel 对应的 SelectionKey 找到。参数用于设置超时时间
       */
      public abstract int select(long timeout) throws IOException;
      
      /**
      * 无超时时间的 select 过程，一直等待，直到发现有 Channel 可以进行
      * IO 操作
      */
      public abstract int select() throws IOException;
      
      /**
      * 立即返回的 select 过程
      */
      public abstract int selectNow() throws IOException;
      ......
      
      /**
      * 唤醒 Selector，对无超时时间的 select 过程起作用，终止其等待
      */
      public abstract Selector wakeup();
      ......
  }
  ```

  



