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



#### 基于 Netty的 TCP

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
                            protected void initChannel(SocketChannel socketChannel) throws Exception {
                                socketChannel.pipeline().addLast(new NettyServerHandler());
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
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        // 接收客户端发来的数据

        System.out.println("client address: " + ctx.channel().remoteAddress());

        // ByteBuf 是 Netty 提供的类，比 NIO 的 ByteBuffer 性能更高
        ByteBuf byteBuf = (ByteBuf) msg;
        System.out.println("data from client: " + byteBuf.toString(CharsetUtil.UTF_8));
    }

    /**
     * 数据读取完毕后执行
     *
     * @param ctx 上下文对象
     * @throws Exception
     */
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        // 发送响应给客户端
        ctx.writeAndFlush(
                // Unpooled 类是 Netty 提供的专门操作缓冲区的工具
                // 类，copiedBuffer 方法返回的 ByteBuf 对象类似于
                // NIO 中的 ByteBuffer，但性能更高
                Unpooled.copiedBuffer("hello client! i have got your data.", CharsetUtil.UTF_8)
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
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
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
                            protected void initChannel(SocketChannel socketChannel) throws Exception {
                                socketChannel.pipeline().addLast(new NettyClientHandler());
                                // 可以继续调用 socketChannel.pipeline().addLast()
                                // 添加更多 Handler
                            }
                        }
                );

        System.out.println("client is ready...");

        // 启动客户端去连接服务器端，ChannelFuture 涉及到 Netty 的异步模型，后面展开讲
        ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 8080).sync();
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
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        // 向服务器发送数据
        ctx.writeAndFlush(
                // Unpooled 类是 Netty 提供的专门操作缓冲区的工具
                // 类，copiedBuffer 方法返回的 ByteBuf 对象类似于
                // NIO 中的 ByteBuffer，但性能更高
                Unpooled.copiedBuffer("hello server!",　CharsetUtil.UTF_8)
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
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        // 接收服务器端发来的数据

        System.out.println("server address: " + ctx.channel().remoteAddress());

        // ByteBuf 是 Netty 提供的类，比 NIO 的 ByteBuffer 性能更高
        ByteBuf byteBuf = (ByteBuf) msg;
        System.out.println("data from server: " + byteBuf.toString(CharsetUtil.UTF_8));
    }

    /**
     * 发生异常时执行
     *
     * @param ctx   上下文对象
     * @param cause 异常对象
     * @throws Exception
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        // 关闭与服务器端的 Socket 连接
        ctx.channel().close();
    }
}
```



---

#### Bytebuf（字节容器）

- 网络通信最终都是通过字节流进行传输的

- Netty 为了解决 Buffer 原生接口的复杂操作提供了 ByteBuf， ByteBuf是一个很好的经过优化过的数据容器，其内部是一个字节数组，可以将字节数据添加到 ByteBuf 中或从 ByteBuf 中获取数据， 相比于原生 Buffer，ByteBuf 更加灵活和易用

- Netty 的数据处理主要通过两个API 提供

  - abstract class ByteBuf
  - interface ByteBufHolder

- 使用 ByteBuf API 能够带来良好的编码体验

  - 正常情况下，ByteBuf 比 ByteBuffer 的性能更好
  - 实现了 ReferenceCounted 引用计数接口，优化了内存的使用
  - 容量可以动态增长，如 StringBuilder 和 String
  - 在读和写这两种模式切换时，无需像 ByteBuffer 一样调用 flip 方法，更易于操作
  - ...

- ByteBuf 实现

  - ByteBuf 维护了两个不同的索引：一个是用于读取的 readerIndex ， 一个是用于写入的 writerIndex。
  - 当写入字节到 ByteBuf 后，writerIndex 增加，开始读取字节后，readerIndex 开始增加
  - 读取字节直到 readerIndex 和 writerIndex 到达同一位置（已经读取到末尾了），ByteBuf 就变为不可读
  - 当调用 ByteBuf 以 read 或 write 开头的方法时，将会增加这个 ByteBuf 的读索引或写索引，而诸如 set 或 get 的方法则不会改变索引
  - 可以指定 ByteBuf 的最大容量，如果对 ByteBuf 的写入操作导致 writerIndex 超出了最大人容量，那么程序将会 抛出一个异常，ByteBuf 的最大人容量是 Integer.MAX_VALUE

- ByteBuf 使用模式

  - 堆缓冲模式
    - 堆缓冲区(HeapByteBuf)： 最常用的 ByteBuf 模式是将数据存储在 JVM 的堆空间中，实际上是通过数组存储数据， 所以这种模式被称为支撑数组（Backing Array ）
    - 这种模式被称为支撑数组可以在没有使用池化的情况下快速分配和释放， 适合用于有遗留数据需要处理的情况

  - 直接缓冲模式
    - 直接缓冲区(DirectByteBuf)： 在 Java 中创建的对象大部分都是存储在堆区之中的，但这不是绝对的，在 NIO 的 API 中， 允许 Buffer 分配直接内存，即操作系统的内存
    - 好处： 前面在传输章节介绍过的零拷贝技术的 特点之一就是规避了多次 IO 拷贝，而现在数据直接就在直接内存中，而不是在 JVM 应用进程中，这不仅减少了拷贝次数， 是否还意味着减少了用户态与内核态的上下文切换呢
    - 缺点： 直接内存的分配和释放都较为昂贵，而且因为直接缓冲区的数据不是在堆区的，所以在某些时候可能需要将直接缓冲区的数据先拷贝一个副本到堆区， 再对这个副本进行操作

  - 复合缓冲模式
    - 复合缓冲区（CompositeByteBuf）： CompositeByteBuf 为多个 ByteBuf 提供了一个聚合视图，根据需要，向 CompositeByteBuf 中添加或删除 ByteBuf 实例，所以 CompositeByteBuf 中可能 同时包含直接缓冲区模式和堆缓冲区模式的 ByteBuf
    - 对于 CompositeByteBuf的hasArray 方法， 如果 CompositeByteBuf 中只有一个 ByteBuf 实例，那么 CompositeByteBuf 的 hasArray 方法 将直接返回这唯一一个 ByteBuf的hasArray 方法的结果，否则返回 false

- ByteBufHolder

  - ByteBuf 几乎唯一的作用就是存储数据，在实际的数据传输中，除了数据，可能还需要存储各种属性值，Http 便是一个很好的例子，除了Http Content，还包括状态码，cookie 等属性，不能把这些属性与 Content 存储在一个 ByteBuf 中， 所以Netty提供了 ByteBufHolder
  - ByteBufHolder 为 Netty 提供了高级特性的支持，如缓冲区持化，使得可以从池中借用 ByteBuf，并且在需要的时候自动释放
  - 常用方法
    - content：返回 ByteBufHolder 所持有的 ByteBuf
    - copy：返回 ByteBufHolder 的深拷贝，连它持有的 ByteBuf 也拷贝

- ByteBuf 分配

  - ByteBufAllocator
    - 了减少分配和释放内存的开销，Netty 通过 ByteBufAllocator 实现了 ByteBuf 的池化，常用方法
      - buffer：返回一个基于堆或直接内存的 ByteBuf，具体取决于实现。
      - heapBuffer：返回一个基于堆内存的 ByteBuf
      - directBuffer：返回一个基于直接内存的 ByteBuf
      - compositeBuffer：返回一个组合 ByteBuf
      - ioBuffer：返回一个用于套接字的 ByteBuf
    - Netty 提供了两种 ByteBufAllocator 的实现：PooledByteBufAllocator（默认）、UnpooledByteBufAllocator
      - PooledByteBufAllocator 池化了 ByteBuf 的实例，提高了性能并最大限度的减少内存碎片，此实现的分配内存的方法 是使用 jemalloc，这种方法分配内存的效率非常高，已被大量现代操作系统采用
      - UnpooledByteBufAllocator 不会池化 ByteBuf
  - Unpooled
    - 当 Channel 或 ChannelHandlerContext 未引用 ByteBufAllocator 时，就无法使用 ByteBufAllocator 来分配 ByteBUf，对于这种情况，Netty提供了Unpooled工具类，它提供了一系列的静态方法来分配未池化的 ByteBuf
  - ByteBufUtil
    - ByteBufUtil 是 ByteBuf 的一个工具类，它提供大量操作 ByteBuf 的方法，，其中非常重要的一个方法就是 hexDump，这个方法会以16进制的形式来表示 ByteBuf 的内容。另一个很重要的方法是equals，它被用于判断 ByteBuf 之间的相等性

- 引用计数

  - JVM 的垃圾回收有引用计数法和可达性分析这两种算法判断对象是否存活，Netty 使用了引用计数法来优化内存的使用
  - 引用计数确保了当对象的引用计数大于1时，对象就不会被释放，当计数减少至0时， 对象就会被释放，
  - 如果程序访问一个已被释放的引用计数对象，那么将会导致一个 IllegalReferenceCountException 异常
  - 在Netty中，ByteBuf 和 ByteBufHolder 都实现了 ReferenceCounted 接口



#### Bootstrap 和 ServerBootstrap（启动引导类）

- 引导类的层次结构包括一个抽象的父类（AbstractBootstrap）和两个具体的引导子类（Bootstrap、ServerBootstrap）

- AbstractBootstrap 的主要方法

  - goup( )：设置用语处理所有事件的 EventLoopGroup
  - channel( )：指定服务端或客户端的 Channel

  - channelFactory( )：如果引导没有指定 Channel，那么可以指定 ChannelFactory 来创建 Channel
  - localAddress( )：指定 Channel 需要绑定的本地地址，如果不指定，则将由系统随机分配一个地址
  - remoteAddress( )：设置 Channel 需要连接的远程地址
  - attr( )：指定新创建的 Channel 的属性值
  - handler( )：设置添加到 ChannelPipeline 中的 ChannelHandler
  - connect( )：连接到远程主机，返回 ChannelFuture，用于连接完成的回调
  - bind( )：绑定指定地址，返回 ChannelFuture，用于绑定完成的回调

- Bootstrap 

  - 客户端的启动引导类 / 辅助类

```java
EventLoopGroup group = new NioEventLoopGroup();
try {
  // 创建客户端启动引导/辅助类：Bootstrap
  Bootstrap b = new Bootstrap();
  // 指定线程模型
  b.group(group).
    ......
  // 尝试建立连接
  ChannelFuture f = b.connect(host, port).sync();
  f.channel().closeFuture().sync();
} finally {
  // 优雅关闭相关线程组资源
  group.shutdownGracefully();
}
```

  - 作为客户端，需要使用到 connect API 来连接到远程 服务端

```java
// 创建EventLoopGroup
EventLoopGroup group = new NioEventLoopGroup();
// 创建客户端引导
Bootstrap bootstrap = new Bootstrap();
// 配置各种属性，如Channel，ChannelHandler等 
bootstrap.group(group) 
  .channel(NioSocketChannel.class)
  .handler(new SimpleChannelInboundHandler<ByteBuf>() { 
    @Override
    protected void channeRead0(
      ChannelHandlerContext channelHandlerContext,
      ByteBuf byteBuf) throws Exception {
      System.out.println("Received data");
      byteBuf.clear();
    }
  });

// 作为客户端
// 连接到远程主机
ChannelFuture future = bootstrap.connect(new InetSocketAddress("www.manning.com", 80)); 
// 设置连接成功后的回调
future.addListener(new ChannelFutureListener() {
  @Override
  public void operationComplete(ChannelFuture channelFuture)throws Exception {
    if (channelFuture.isSuccess()) {
      System.out.println("Connection established");
    } else {
      System.err.println("Connection attempt failed");
      channelFuture.cause().printStackTrace();
    }
  }
});
```

- ServerBootstrap

  - 客户端的启动引导类 / 辅助类


```java
// 1.bossGroup 用于接收连接，workerGroup 用于具体的处理
EventLoopGroup bossGroup = new NioEventLoopGroup(1);
EventLoopGroup workerGroup = new NioEventLoopGroup();
try {
  //2.创建服务端启动引导/辅助类：ServerBootstrap
  ServerBootstrap b = new ServerBootstrap();
  //3.给引导类配置两大线程组,确定了线程模型
  b.group(bossGroup, workerGroup).
    ......
    // 6.绑定端口
    ChannelFuture f = b.bind(port).sync();
  // 等待连接关闭
  f.channel().closeFuture().sync();
} finally {
  //7.优雅关闭相关线程组资源
  bossGroup.shutdownGracefully();
  workerGroup.shutdownGracefully();
	}
}
```

- 作为服务端，需要使用 bind API 来 与本地地址绑定，从而接收客户端连接

```java
// 创建EventLoopGroup
NioEventLoopGroup group = new NioEventLoopGroup();
// 创建服务端引导
ServerBootstrap bootstrap = new ServerBootstrap();
// 配置各种属性，如Channel，ChannelHandler等 
bootstrap.group(group)
  .channel(NioServerSocketChannel.class) 
  .childHandler(new SimpleChannelInboundHandler<ByteBuf>() {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, yteBuf byteBuf) throws Exception {
      System.out.println("Reveived data");
      byteBuf.clear();
    }
  });

// 作为服务端
// 绑定本地地址
ChannelFuture future = bootstrap.bind(new InetSocketAddress(8080));
// 设置绑定成功后的回调
future.addListener(new ChannelFutureListener() {
  @Override
  public void operationComplete(ChannelFuture channelFuture)
    throws Exception {
    if (channelFuture.isSuccess()) {
      System.out.println("Server bound");
    } else {
      System.err.println("Bound attempt failed");
      channelFuture.cause().printStackTrace();
    }
  }
});
```

- Bootstrap 通常使用 connet( ) 连接到远程的主机和端口，作为一个 Netty TCP 协议通信中的客户端。另外，Bootstrap 也可以通过 bind( ) 绑定本地的一个端口，作为 UDP 协议通信中的一端
- ServerBootstrap 通常使用 bind( ) 绑定本地的端口上，然后等待客户端的连接
- Bootstrap 只需要配置一个线程组— EventLoopGroup，而 ServerBootstrap 需要配置两个线程组— EventLoopGroup，一个用于接收连接，一个用于具体的 IO 处理



#### Channel（网络操作抽象类）

- Channel 接口是 Netty 对网络操作抽象类，EventLoop 负责处理注册到其上的 Channel 的 I/O 操作，两者配合进行 I/O 操作
- 一旦客户端成功连接服务端，就会新建一个 Channel 同该用户端进行绑定

```java
//  通过 Bootstrap 的 connect 方法连接到服务端
public Channel doConnect(InetSocketAddress inetSocketAddress) {
  CompletableFuture<Channel> completableFuture = new CompletableFuture<>();
  bootstrap.connect(inetSocketAddress).addListener((ChannelFutureListener) future -> {
    if (future.isSuccess()) {
      completableFuture.complete(future.channel());
    } else {
      throw new IllegalStateException();
    }
  });
  return completableFuture.get();
}
```

- 比较常用的 Channel 接口实现类是 ：

  - NioServerSocketChannel（服务端）

  - NioSocketChannel（客户端）

- 这两个 Channel 可以和 BIO 编程模型中的 ServerSocket 以及 Socket 两个概念对应上



#### EventLoop（事件循环）

- 事件循环正如它的名字，处于一个循环之中，以前在编写网络程序的时候，让处理连接的逻辑 处于一个死循环之中，这样可以不断的处理客户端连接
- EventLoop 接口是 Netty 中最核心的概念
- 《Netty 实战》这本书是这样介绍它的：EventLoop 定义了 Netty 的核心抽象，用于处理连接的生命周期中所发生的事件
- 简单来说，EventLoop 的主要作用
  - 责监听网络事件并调用事件处理器进行相关 I/O 操作（读写）的处理
- Netty 的 EventLoop 采用了两个基本的API：并发和网络
  - Netty的并发包 io.netty.util.concurrent 是基于 Java 的并发包 java.util.concurrent， 主要用于提供 Executor 的支持
  - Netty 的 io.netty.channel 包提供了与客户端 Channel 的事件交互的支持
- 线程模型
  - 线程模型确定了代码执行的方式，它可能带来一些副作用以及不确定因素， 这是并发编程中最大的难点
  - 现代操作系统几乎都具有多个核心的 CPU，因此可以使用多线程技术以有效地利用系统资源，在早期的 Java 多线程编程中，使用线程的方式一般都是继承 Thread 或者实现 Runnable 以此创建新的 Thread， 这是一种比较原始且浪费资源的处理线程的方式，JDK5之后引入了 Executor API，其核心思想是使用池化技术 来重用 Thread，以此达到提高线程响应速度和降低资源浪费的目的
  - EventLoop 线程模型
    - EventLoop 将有一个永远不会改变的 Thread，Netty 会给 EventLoop 分配一个 Thread，在 EventLoop 生命周期之中的所有 IO 操作和事件都由这个 Thread 执行，即 Thread 和 EventLoop 属于 1 : 1 的关系，从而保证线程安全
    - 根据配置和 CPU 核心的不同， Netty 可以创建多个 EventLoop，且单个 EventLoop 可能会服务于多个客户端Channel
    - 事件或任务的执行总是以 FIFO 顺序执行，这样可以保证字节总是按正确的顺序被处理，消除潜在的数据损坏的可能性
- 任务调度
  - 有时候需要在指定的时间之后触发任务或者周期性的执行某一个人物，这都需要使用到任务调度
  - JDK 任务调度
    - JDK 主要有 Timer 和 ScheduledExecutorService 两种实现任务调度的方式，但是这两种原生的 API 的性能都不太适合高负载应用
  - EventLoop 任务调度
    - EventLoop 扩展了 ScheduledExecutorService， 可以通过 EventLoop 来实现任务调度
    - 使用 Channel 获取其对应的 EventLoop，然后调用 schedule 方法给其分配一个 Runnable 执行
    - Netty 的任务调度比 JDK 的任务调度性能性能要好，这主要是由于 Netty 底层的线程模型设计的非常优秀
- 线程管理
  - Netty 线程模型的卓越性能取决于当前执行任务的 Thread
  - 如果处理 Chanel 任务的线程正是支撑 EventLoop 的线程，那么与 Channel 的任务会被直接执行，否则 EventLoop 会将该任务放入任务队列之中稍后执行
  - 每个 EventLoop 都有自己的任务队列，独立于其他 EventLoop 的任务队列
- 线程分配
  - 每个 EventLoop 都注册在一个 EventLoopGroup 之中，一个 EventLoopGroup 可以包含多个 EventLoop，根据不同的传输实现， EventLoop 的创建和分配方式也不同
  - 非阻塞传输
    - 一个 EventLoop 可以处理多个 Channel，Netty 这样设计的目的就是尽可能的通过少量 Thread 来支撑大量的 Channel， 而不是每个 Channel 都分配一个 Thread
    - EventLoopGroup 负责为每个新创建的 Channel 分配一个EventLoop，一旦一个 Channel 被分配给 EventLoop，它将在整个生命周期中都使用这个EventLoop 及其 Thread 处理事件和任务
    - EventLoop 的分配方式对 ThreadLocal 的使用是很有很大影响的，因为注册在一个 EventLoop 上的 Channel 共有这一个线程，那么在这些 Channel 之间使用 ThreadLocal，其 ThreadLocal 的状态都是一样的，无法发挥 ThreadLocal 本来的作用
- 与 EventLoopGroup 关系
  - EventLoopGroup 包含多个 EventLoop（每一个 EventLoop 通常内部包含一个线程），它管理着所有的 EventLoop 的生命周期



#### ChannelHandler（消息处理器） 和 ChannelPipeline（ChannelHandler 对象链表）

- Channel 

  - Channel 接口的生命周期、状态与 ChannelHandler 是密切相关的，当这些状态发生改变时，将会生成对应的事件，ChannelPipeline 中的ChannelHandler 会及时做出处理
  - 生命周期、状态
    - ChannelUnregistered：Channel 没有注册到 EventLoop
    - ChannelRegistered：Channel 被注册到了 EventLoop
    - ChannelActive：Channel 已经连接到它的远程节点，处于活动状态，可以收发数据
    - ChannelInactive：Channel 与远程节点断开不再处于活动状态

- ChannelHandler 

  - 消息的具体处理器，主要负责处理客户端 / 服务端接收和发送的数据
  - 当 Channel 被创建时，会被自动地分配到它专属的 ChannelPipeline
  - ChannelHandler接口定义了其生命周期中的操作，当 ChanelHandler 被添加到 ChannelPipeline  或从 ChannelPipeline 中移除时，会调用这些操作
  - 生命周期、状态
    - handlerAdded：当把 ChannelHandler 添加到 ChannelPipeline 中时调用此方法
    - handlerRemoved：当把 ChannelHandler 从 ChannelPipeline 中移除的时候会调用此方法
    - exceptionCaught：当 ChannelHandler 在处理数据的过程中发生异常时会调用此方法

- ChannelInboundHandler

  - ChannelInboundHandler 接口在接受数据或者其对应的 Channel 状态发生改变时调用其生命周期的方法， ChannelInboundHandler 的生命周期和 Channel 的生命周期其实是密切相关的

  - 实现 ChannelInboundHandler 的 channelRead( ) 时，使用 ReferenceCountUtil 的 release( ) 释放 ByteBuf 可以减少内存的消耗

  - 生命周期、状态

    - ChannelRegistered：当 Channel 被注册到 EventLoop 且能够处理 IO 事件时会调用此方法
    - ChannelUnregistered：当 Channel 从 EventLoop 注销且无法处理任何IO事件时会调用此方法

    - ChannelActive：当 Channel 已经连接到远程节点或者已绑定本地 address 且处于活动状态时会调用此方法

    - ChannelInactive：当 Channel 与远程节点断开，不再处于活动状态时调用此方法
    - ChannelReadComplete：当 Channel 的某一个读操作完成时调用此方法
    - ChannelRead：当 Channel 有数据可读时调用此方法
    - ChannelWritabilityChanged：当 Channel 的可写状态发生改变时调用此方法，可以调用 Channel 的 isWritable( ) 检测 Channel 的可写性，还可以通过 ChannelConfig 来配置 write 操作相关的属性
    - userEventTriggered：当 ChannelInboundHandler 的 fireUserEventTriggered( ) 被调用时才调用此方法

- ChannelOutboundHandler

  - 出站数据将由ChannelOutboundHandler处理，它的方法将被 Channel，ChannelPipeline 以及 ChannelHandlerContext 调用
  - ChannelOutboundHandler 的大部分方法都需要一个 ChannelPromise 类型的参数，ChannelPromise 是 ChannelFuture的 一个子接口，所以 ChannelPromise 的作用和 ChannelFuture 是一样的， 用于在 ChannelOutboundHandler 的操作完成后执行的回调
  - 主要方法
    - bind：当 Channel 绑定到本地 address 时会调用此方法
    - connect：当 Channel 连接到远程节点时会调用此方法
    - disconnect：当 Channel 和远程节点断开时会调用此方法
    - close：当关闭 Channel 时会调用此方法
    - deregister：当 Channel 从它的 EventLoop 注销时会调用此方法
    - read：当从 Channel 读取数据时会调用此方法
    - flush：当Channel将数据冲刷到远程节点时调用此方法
    - write：当通过 Channel 将数据写入到远程节点时调用此方法

- 资源管理

  - 使用 ChannelInboundHandler 的 read 或 ChannelOutboundHandler 的 write 操作时，都需要保证 没有任何资源泄露并尽可能的减少资源耗费， 为了帮助诊断潜在的的资源泄露问题，Netty 提供了 ResourceLeakDetector，它将对 Netty 程序的已分配的缓冲区做大约 1% 的采样用以检测内存泄露

  - Netty 目前定义了4种泄露检测级别

    - Disabled
      - 禁用泄露检测，应当在详细测试之后才应该使用此级别
    - SIMPLE
      - 使用 1% 的默认采样率检测并报告任何发现的泄露，默认
    - ADVANCED
      - 使用默认的采样率，报告任何发现的泄露以及对应的消息的位置
    - PARANOID
      - 类似于ADVANCED，但是每次都会对消息的访问进行采样，此级别可能会对程序的性能造成影响，应该用于调试阶段

  - 可以通过JVM启动参数来设置leakDetector的级别：

    ```
    java -Dio.netty.leakDetectionLevel=ADVANCED
    ```

- ChannelPipeline

  - ChannelPipeline 是一系列 ChannelHandler 组成的拦截链，每一个新创建的 Channel 都会被分配一个新的 ChannelPipeline，Channel 和 ChannelPipeline 之间的关联是持久的

  - 一个 Channel 包含一个 ChannelPipeline，一个 ChannelPipeline 上可以有多个 ChannelHandler

  - 在 ChannelPipeline 上通过 addLast( ) 添加一个或者多个ChannelHandler，当一个 ChannelHandler 处理完之后就将数据交给下一个 ChannelHandler 

  - 当 ChannelHandler 被添加到的 ChannelPipeline 它得到一个 ChannelHandlerContext，它代表一个 ChannelHandler 和 ChannelPipeline 之间的“绑定”，ChannelPipeline 通过 ChannelHandlerContext 来间接管理 ChannelHandler 

  - ChannelPipeline 持久论

    - Netty 将 ChannelPipeline 的入站口作为头部，出站口作为尾部
    - 一个入站事件将从 ChannelPipeline 的头部（左侧）向尾部（右侧）开始传播，出站事件的传播则是与入站的传播方向相反
    - 当 ChannelPipeline 在 ChannelHandler 之间传播事件的时候，它会判断下一个 ChannelHandler 的类型 是否与当前 ChannelHandler 的类型相同，如果相同则说明它们是一个方向的事件， 如果不同则跳过该 ChannelHandler 并前进到下一个 ChannelHandler，直到它找到相同类型的ChannelHandler

  - ChannelPipeline 的 API 不仅有对 ChannelHandler 的增删改操作，还有对入站和出站操作的附加方法

  - ChannelPipeline 的入站方法

    - fireChannelRegistered：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelRegistered 方法
    - fireChannelUnregistered：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelUnregistered 方法

    - fireChannelActive：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelActive 方法

    - fireChannelInactive：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelInactive 方法
    - fireExceptionCaught：调用 ChannelPipeline 中下一个 ChannelInboundHandler的exceptionCaught 方法
    - fireUserEventTriggered：调用 ChannelPipeline 中下一个 ChannelInboundHandler的userEventTriggered 方法
    - fireChannelRead：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelRead 方法
    - fireChannelReadComplete：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelReadComplete 方法
    - fireChannelWritabilityChanged：调用 ChannelPipeline 中下一个 ChannelInboundHandler的channelWritabilityChanged 方法

  - ChannelPipeline 的出站方法

    - bind：调用 ChannelPipeline 中下一个 ChannelOutboundHandler 的 bind( )，将 Channel 与本地地址绑定
    - connect：调用 ChannelPipeline 中下一个 ChannelOutboundHandler 的 connec( )，将 Channel 连接到远程节点
    - disconnect：调用ChannelPipeline 中下一个 ChannelOutboundHandler 的 disconnect( )，将 Channel 与远程连接断开
    - close：调用ChannelPipeline 中下一个 ChannelOutboundHandler 的 close( )，将 Channel 关闭
    - deregister：调用ChannelPipeline 中下一个 ChannelOutboundHandler 的 deregister( )，将 Channel 从其对应的 EventLoop 注销
    - flush：调用ChannelPipeline 中下一个 ChannelOutboundHandler 的 flush( )，将 Channel 的数据冲刷到远程节点
    - write：调用ChannelPipeline 中下一个 ChannelOutboundHandler 的 write( )，将数据写入 Channel
    - writeAndFlush：先调用 write 方法，然后调用 flush( )，将数据写入并刷回远程节点
    - read：调用 ChannelPipeline 中下一个 ChannelOutboundHandler 的 read 方法，从 Channel 中读取数据

- ChannelHandlerContext

  - 是 ChannelHandler 和 ChannelPipeline 之间的关联，每当有 ChannelHandler 添加到 ChannelPipeline 中时，都会创建 ChannelHandlerContext
  - 主要作用是管理它所关联的 ChannelHandler 与同一个 ChannelPipeline 中的其他 ChannelHandler 之间的交互
  - 大部分方法和 Channel 和 ChannelPipeline 相似
    - 有一个重要的区别
      - 调用 Channel 或 ChannelPipeline 的方法，其影响是会沿着整个 ChannelPipeline 进行传播
      - 调用 ChannelHandlerContext 的方法，则是从其关联的 ChannelHandler 开始，并且只会传播给位于该 ChannelPipeline 中的下一个能够处理该事件的 ChannelHandler
  - 重要方法
    - alloc：获取与当前 ChannelHandlerContext 所关联的 Channel 的 ByteBufAllocator
    - handler：返回与当前 ChannelHandlerContext 绑定的 ChannelHandler
    - pipeline：返回与当前 ChannelHandlerContext 关联的 ChannelPipeline
  - 高级用法
    - 有时候需要在多个 ChannelPipeline 之间共享一个 ChannelHandler，以此实现跨管道处理（获取）数据 的功能，此时的 ChannelHandler 属于多个 ChannelPipeline，且会绑定到不同的 ChannelHandlerContext 上。
    - 在多个 ChannelPipeline 之间共享 ChannelHandler 需要使用 @Sharable，代表它是一个共享的 ChannelHandler
    - 如果一个 ChannelHandler 没有使用 @Sharable 却被用于多个 ChannelPipeline，那么将会触发异常
    - 一个 ChannelHandler 被用于多个 ChannelPipeline 肯定涉及到多线程 数据共享的问题，因此需要保证 ChannelHandler 的方法同步

```java
b.group(eventLoopGroup)
  .handler(new ChannelInitializer<SocketChannel>() {
    @Override
    protected void initChannel(SocketChannel ch) {
      ch.pipeline().addLast(new NettyKryoDecoder(kryoSerializer, RpcResponse.class));
      ch.pipeline().addLast(new NettyKryoEncoder(kryoSerializer, RpcRequest.class));
      ch.pipeline().addLast(new KryoClientHandler());
    }
  });
```



#### ChannelFuture（操作执行结果）

- Netty 是异步非阻塞的，所有的 I/O 操作都为异步的，因此，不能立刻得到操作是否执行成功
- 通过 ChannelFuture 接口的  addListener( ) 注册一个 ChannelFutureListener，当操作执行成功或者失败时，监听就会自动触发返回结果

```java
public interface ChannelFuture extends Future<Void> {
  Channel channel();

  ChannelFuture addListener(GenericFutureListener<? extends Future<? super Void>> var1);
  ......

  ChannelFuture sync() throws InterruptedException;
}
```

```java
ChannelFuture f = b.connect(host, port).addListener(future -> {
  if (future.isSuccess()) {
    System.out.println("连接成功!");
  } else {
    System.err.println("连接失败!");
  }
}).sync();
```

- 通过 ChannelFuture 的 channel( ) 获取连接相关联的 channel 

```java
Channel channel = f.channel()
```

- 通过 ChannelFuture 接口的 sync( ) 让异步的操作编程同步的


```java
// bind()是异步的，但是，你可以通过sync()将其变为同步
ChannelFuture f = b.bind(port).sync();
```



#### 编码器与解码器

- 从网络传输的角度来讲，数组总是以字节的格式在网络之中进行传输的

- 每当源主机发送数据到目标主机时，数据会从本地格式被转换成字节进行传输，这种转换被称为编码，编码的逻辑由编码器处理

- 每当目标主机接受来自源主机的数据时，数据会从字节转换为需要的格式，这种转换被称为解码，解码的逻辑由解码器处理

- 在 Netty 中，编码解码器实际上是 ChannelOutboundHandler 和 ChannelInboundHandler 的实现， 因为编码和解码都属于对数据的处理，所以，编码解码器被设计为 ChannelHandler

- 编码器

  - 在Netty中，编码器是 ChannelOutboundHandler 的实现，即处理出站数据
  - 分为两种
    - 将消息编码为字节： MessageToByteEncoder
    - 将消息编码为消息： MessageToMessageEncoder
  - MessageToByteEncoder
    - 用于将消息编码为字节，如果需要自定编码器，就需要继承它并实现它的 encode( )
    - encode( ) 是自定义编码器必须实现的方法，它被调用时会传入相应的数据和一个存储数据的 ByteBuf
    - 在 encode 被调用之后，该 ByteBuf 会被传递给 ChannelPipeline 中下一个 ChannelOutboundHandler
    - 编程模型

  ```java
  // 扩展MessageToByteEncoder
  public class ShortToByteEncoder extends MessageToByteEncoder<Short>{  
   
    @Override
    public void encode(ChannelHandlerContext ctx , Short data, ByteBuf out) throws Exception {
      out.writeShort(data);//将data写入ByteBuf   
    }
  }
  ```

  - MessageToMessageEncoder
    - 用于将一种类型的消息编码另一种类型的消息，其原型和 MessageToMessageDecoder 相似

- 解码器

  - 在 Netty 中，解码器是 ChannelInboundHandler 的实现，即处理入站数据
  - 分为两种
    - 将字节解码为 Message 消息： ByteToMessageDecoder、ReplayingDecoder
    - 将一种消息解码为另一种消息： MessageToMessageDecoder
  - ByteToMessageDecoder
    - 用于将字节解码为消息，如果想自定义解码器，就需要继承这个类并实现 decode( )
    - decode( ) 是自定解码器必须实现的方法，它被调用时会传入一个包含了数据的 ByteBuf 和一个用来添加解码消息的 List，对 decode( ) 的调用会重复进行，直至确认没有新元素被添加到该 List 或 ByteBuf 没有可读字节为止，如果 List 不为空， 那么它的内容会被传递给 ChannelPipeline 中的下一个 ChannelInboundHandler
    - 编程模型

  ```java
  // 扩展ByteToMessageDecoder
  public class ToIntegerDecoder extends ByteToMessageDecoder {  
  
    @Override
    public void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
      //检查ByteBuf是否仍有4个字节可读
      if (in.readableBytes() >= 4) {  
        out.add(in.readInt());  //从ByteBuf读取消息到List中
      }
    }
  }
  ```

  - ReplayingDecoder
    - ReplayingDecoder 扩展了 ByteToMessageDecoder，这使得不再需要检查 ByteBuf，因为 ReplayingDecoder 自定义了 ByteBuf 的实现 ReplayingDecoderByteBuf，这个包装后的 ByteBuf 在内部会自动检查是否可读
    - 虽然 ReplayingDecoderByteBuf 可以自动检查可读性，但是对于某些操作并不支持，会抛出 UnsupportedOperationException 异常
    - 编程模型

  ```java
  // 扩展ReplayingDecoder
  public class ToIntegerDecoder2 extends ReplayingDecoder<Void> {
    
    @Override
    public void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
      out.add(in.readInt());//从ByteBuf读取消息到List中
    }
  }
  ```

  - MessageToMessageDecoder

    - MessageToMessageDecoder 用于将一种类型的消息解码另一种类型的消息，如从 DTO 转为 POJO

    - 编程模型

  ```java
  public class IntegerToStringDecoder extends MessageToMessageDecoder<Integer> {
  
    @Override
    public void decode(ChannelHandlerContext ctx, Integer msg, List<Object> out) throws Exception{
      out.add(String.valueOf(msg));
    }
  }
  ```

- 编解码器
  - Netty 还提供了集编码与解码 于一身的编解码器 ByteToMessageCodec 和 MessageToMessageCodec，同时实现了 ChannelInboundHandler 和 ChannelOutboundHandler
  - 虽然使用编码解码器可以同时编码和解码数据，但这样不利于代码的可重用性。 相反，单独的编码器和解码器最大化了代码的可重用性和可扩展性，所以应该优先考虑分开使用二者



### Netty 长连接、心跳机制

- TCP 长连接和短连接
  - TCP 在进行读写之前，server 与 client 之间必须提前建立一个连接，建立连接需要三次握手，释放 / 关闭连接需要四次挥手，这两个过程是比较消耗网络资源并且有时间延迟的
  - 短连接
    - server 端 与 client 端建立连接之后，读写完成之后就关闭掉连接，如果下一次再要互相发送消息，就要重新连接
    - 优点：管理和实现都比较简单
    - 缺点：每一次的读写都要建立连接必然会带来大量网络资源的消耗，并且连接的建立也需要耗费时间
  - 长连接
    - client 向 server 双方建立连接之后，client 与 server 完成一次读写，它们之间的连接并不会主动关闭，后续的读写操作会继续使用这个连接
    - 优点：可以省去较多的 TCP 建立和关闭的操作，降低对网络资源的依赖，节约时间
    - 对于频繁请求资源的客户来说，非常适用长连接
- 心跳机制
  - 在 TCP 保持长连接的过程中，可能会出现断网等网络异常出现，异常发生的时候， client 与 server 之间如果没有交互的话，它们是无法发现对方已经掉线的。为了解决这个问题, 就需要引入心跳机制
  - 工作原理
    - 在 client 与 server 之间在一定时间内没有数据交互时，即处于 idle 状态时，客户端或服务器就会发送一个特殊的数据包给对方
    - 当接收方收到这个数据报文后，也立即发送一个特殊的数据报文，回应发送方，此即一个 PING-PONG 交互
    - 当某一端收到心跳消息后，就知道了对方仍然在线，这就确保 TCP 连接的有效性
  - TCP 实际上自带的就有长连接选项，本身是也有心跳包机制，也就是 TCP 的选项：SO_KEEPALIVE。但是，TCP 协议层面的长连接灵活性不够
  - 一般情况下都是在应用层协议上实现自定义心跳机制的，也就是在 Netty 层面通过编码实现。通过 Netty 实现心跳机制的话，核心类是 IdleStateHandler



#### Netty 零拷贝

- 零复制（英语：Zero-copy；也译零拷贝）
  - 计算机执行操作时，CPU 不需要先将数据从某处内存复制到另一个特定区域，这种技术通常用于通过网络传输文件时节省 CPU 周期和内存带宽
  - 在 OS 层面上的 Zero-copy 通常指避免在用户态（User-space）与内核态（Kernel-space）之间来回拷贝数据
  - 在 Netty 层面 ，零拷贝主要体现在对于数据操作的优化
- Netty 零拷贝
  - 使用 Netty 提供的 CompositeByteBuf 类, 可以将多个 ByteBuf 合并为一个逻辑上的 ByteBuf，避免了各个 ByteBuf 之间的拷贝
  - ByteBuf 支持 slice 操作, 因此可以将 ByteBuf 分解为多个共享同一个存储区域的 ByteBuf，避免了内存的拷贝
  - 通过 FileRegion 包装的 FileChannel.tranferTo 实现文件传输，可以直接将文件缓冲区的数据发送到目标 Channel，避免了传统通过循环 write 方式导致的内存拷贝问题



####  TCP 粘包 / 拆包

- TCP 粘包 / 拆包 就是你基于 TCP 发送数据的时候，出现了多个字符串“粘”在了一起或者一个字符串被“拆”开的问题
- 使用 Netty 自带的解码器
  - LineBasedFrameDecoder
    - 发送端发送数据包的时候，每个数据包之间以换行符作为分隔
    - LineBasedFrameDecoder 的工作原理是它依次遍历 ByteBuf 中的可读字节，判断是否有换行符，然后进行相应的截取
  - DelimiterBasedFrameDecoder
    - 可以自定义分隔符解码器
    - LineBasedFrameDecoder 实际上是一种特殊的 DelimiterBasedFrameDecoder 解码器
  - FixedLengthFrameDecoder
    - 固定长度解码器，它能够按照指定的长度对消息进行相应的拆包
  - LengthFieldBasedFrameDecoder
- 自定义序列化编解码器
  - 在 Java 中自带的有实现 Serializable 接口来实现序列化，但由于它性能、安全性等原因一般情况下是不会被使用到的
  - 通常情况下，我们使用 Protostuff、Hessian2、json 序列方式比较多，另外还有一些序列化性能非常好的序列化方式也是很好的选择
    - 专门针对 Java 语言的：Kryo，FST 等
    - 跨语言的：Protostuff（基于 protobuf 发展而来），ProtoBuf，Thrift，Avro，MsgPack 等