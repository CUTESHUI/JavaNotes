## File类和IO流

#### File 类

- java.io.File类
  - 声明在 java.io 包下
  - File 类的一个对象，代表一个文件、文件目录 / 文件夹
  - File 能新建、删除、重命名文件和目录
  -  File 不能访问文件内容本身，需要访问文件内容本身，则需要使用输入 / 输出流
  - 想要在 Java 程序中表示一个真实存在的文件或目录，那么必须有一个 File 对象
  - Java 程序中的一个File对象，可能没有一个真实存在的文件或目录 
  - File对象可以作为参数传递给流的构造器

- 处理数据时，一定要先明确数据源，与数据目的地
  - 数据源可以是文件，可以是键盘
  - 数据目的地可以是文件、显示器或者其他设备

- 路径
  - 绝对路径：是一个固定的路径，从盘符开始
  - 相对路径：是相对于某个位置开始
- 路径分隔符
  - windows和DOS系统默认使用 “ \ ” 来表示
  - UNIX和URL使用 “/ ” 来表示
  - Java程序支持跨平台运行，因此路径分隔符要慎用。为了解决这个隐患，File类提供了一个常量
    - public static final String separator 
    - 根据操作系统，动态的提供分隔符
- 常用构造器
  - public File (String pathname) 
    - 以pathname为路径创建File对象
    - 可以是绝对路径或者相对路径
    - 如果pathname是相对路径，则默认的当前路径在系统属性user.dir中存储
  - public File (String parent, String child)
    - 以parent为父路径，child为子路径创建File对象
  - public File (File parent, String child)
    - 根据一个父File对象和子文件路径创建File
  
- 方法

  - File 类的获取功能

    - public String getAbsolutePath ( )：获取绝对路径
    
    - public String getPath ( )：获取路径
    
    - public String getName ( )：获取名称
    
    - public String getParent ( )：获取上层文件目录路径。若无，返回null
    
    - public long length ( )：获取文件长度（即：字节数）。不能获取目录的长度
    
    - public long lastModified ( ) ：获取最后一次的修改时间，毫秒值
    
    - public String[ ] list ( ) ：获取指定目录下的所有文件或者文件目录的名称数组
    
    - public File[ ] listFiles ( ) ：获取指定目录下的所有文件或者文件目录的File数组
    
  - File 类的重命名功能

    - public boolean renameTo (File dest)：把文件重命名为指定的文件路径

  - File 类的判断功能

    - public boolean isDirectory ( )：判断是否是文件目录

    - public boolean isFile ( ) ：判断是否是文件
    - public boolean exists( ) ：判断是否存在
    - public boolean canRead ( ) ：判断是否可读
    - public boolean canWrite ( ) ：判断是否可写
    - public boolean isHidden ( ) ：判断是否隐藏

  - File 类的创建功能

    - public boolean createNewFile ( ) ：创建文件。若文件存在，则不创建，返回false

    - public boolean mkdir ( ) ：创建文件目录。如果此文件目录存在，就不创建了。如果此文件目录的上层目录不存在，也不创建 
    - public boolean mkdirs ( ) ：创建文件目录。如果上层文件目录不存在，一并创建

    注意事项：如果你创建文件或者文件目录没有写盘符路径，那么，默认在项目路径下

  - File类的删除功能

    - public boolean delete ( )：删除文件或者文件夹
- 删除注意事项：Java中的删除不走回收站。 要删除一个文件目录，请注意该文件目录内不能包含文件或者文件目录
  




#### IO 流

- I/O是 Input/Output 的缩写

-  I/O技术是非常实用的技术，用于处理设备之间的数据传输。如读/写文件，网络通讯等

-  Java程序中，对于数据的输入/输出操作以“ 流(stream) ”的方式进行

-  java.io包下提供了各种“流”类和接口，用以获取不同种类的数据，并通过标准的方法输入或输出数据

- 原理

  - 输入：读取外部数据（磁盘、光盘等存储设备的数据）到程序 / 内存中
  - 输出：将程序 / 内存数据输出到磁盘、光盘等存储设备中
  - 我们是站在程序 / 内存的角度

- 流的分类

  - 按操作数据单位不同分为

    - 字节流 (8 bit)
      1. 输入：InputStream
      2. 输出：OutputStream
    - 字符流 (16 bit)
      1. 输入：Reader
      2. 输出：Writer

  - 按数据流的流向不同分为

    - 输入流
    - 输出流

  - 按流的角色的不同分为

    - 节点流：直接从数据源或目的地读写数据。直接作用在流上

    - 处理流：不直接连接到数据源或目的地，而是“连接”在已存在的流（节点流或处理流）之上，通过对数据的处理为程序提

      供更为强大的读写功能。包在已有流之上

- InputStream

  - 方法
    - int read ( )：从输入流中读取数据的下一个字节。返回 0 到 255 范围内的 int 字节值。如果因为已经到达流末尾而没有可用的字节，则返回值 -1
    -  int read (byte[ ] b)：从此输入流中将最多 b.length 个字节的数据读入一个 byte 数组中。如果因为已经到达流末尾而没有可用的字节，则返回值 -1。否则以整数形式返回实际读取的字节数 
    - int read (byte[ ] b, int off,int len)：将输入流中最多 len 个数据字节读入 byte 数组。尝试读取 len 个字节，但读取的字节也可能小于该值。以整数形式返回实际读取的字节数。如果因为流位于文件末尾而没有可用的字节，则返回值 -1。 
    - public void close ( ) throws IOException：关闭此输入流并释放与该流关联的所有系统资源。

- Reader

  - 方法
    - int read ( )：读取单个字符。作为整数读取的字符，范围在 0 到 65535 之间 (0x00-0xffff)（2个字节的Unicode码），如果已到达流的末尾，则返回 -1。每次一个个读
    - int read (char[ ] cbuf)：将字符读入数组。如果已到达流的末尾，则返回 -1。每次读数组长度个
    - int read (char[ ] cbuf,int off,int len)：将字符读入数组的某一部分。存到数组cbuf中，从off处开始存储，最多读len个字符。如果已到达流的末尾，则返回 -1。否则返回本次读取的字符数
    - public void close ( ) throws IOException：关闭此输入流并释放与该流关联的所有系统资源

- OutputStream

  - 方法
    -  void write (int b) ：将指定的字节写入此输出流。write 的常规协定是：向输出流写入一个字节。要写入的字节是参数 b 的八个低位。b 的 24 个高位将被忽略。 即写入0~255范围的
    - void write (byte[ ] b)：将 b.length 个字节从指定的 byte 数组写入此输出流。write(b) 的常规协定是：应该与调用 write(b, 0, b.length) 的效果完全相同
    - void write (byte[ ] b,int off,int len)：将指定 byte 数组中从偏移量 off 开始的 len 个字节写入此输出流
    - public void flush ( ) throws IOException：刷新此输出流并强制写出所有缓冲的输出字节，调用此方法指示应将这些字节立即写入它们预期的目标
    - public void close ( ) throws IOException：关闭此输出流并释放与该流关联的所有系统资源

- Writer

  - 方法
    - void write (int c)：写入单个字符。要写入的字符包含在给定整数值的 16 个低位中，16 高位被忽略。 即写入0 到 65535 之间的Unicode码
    - void write (char[ ] cbuf)：写入字符数组
    - void write (char[ ] cbuf,int off,int len)：写入字符数组的某一部分。从off开始，写入len个字符
    - void write (String str)：写入字符串
    - void write (String str,int off,int len)：写入字符串的某一部分 
    - void flush ( )：刷新该流的缓冲，则立即将它们写入预期目标
    - public void close ( ) throws IOException：关闭此输出流并释放与该流关联的所有系统资源

- 字符流 char 读、Reader

  1. File 类的实例化
  2. 流的实例化
  3. 读操作
  4. 关闭资源

- 字符流 char 写、Writer

  1. File 类的实例化
  2. 流的实例化
  3. 写操作
  4. 关闭资源

  - 输出的操作，对应的文件可以不存在，如果不存在，在输出的过程中会自动创建此文件
  - 如果使用的构造器：FileWriter (file, false) / FileWriter (file)，对原有文件覆盖
  - 如果使用的构造器：FileWriter (file, true) / FileWriter (file)，对原有文件追加

- 节点流 / 文件流的注意点

  - 定义文件路径时，注意：可以用 “/” 或者 “\\”
  - 在写入一个文件时，如果使用构造器FileOutputStream(file)，则目录下有同名文件将被覆盖
  - 如果使用构造器FileOutputStream(file,true)，则目录下的同名文件不会被覆盖，在文件内容末尾追加内容。
  - 在读取文件时，必须保证该文件已存在，否则报异常。 
  - 字节流操作字节，比如：.mp3，.avi，.rmvb，mp4，.jpg，.doc，.ppt
  - 字符流操作字符，操作普通文本文件。最常见的文本文件：.txt，.java，.c，.cpp 等语言的源代码



- **缓冲流**

  - 处理流的一种
  
  - 属于 字节流
  
  - 为了提高数据读写的速度，Java API 提供了带缓冲功能的流类，在使用这些流类时，会创建一个内部缓冲区数组，缺省使用8192个字节(8Kb)的缓冲区
  - 缓冲流要“套接”在相应的节点流之上，根据数据操作单位可以把缓冲流分为
    - BufferedInputStream、BufferedOutputStream
    - BufferedReader、BufferedWriter
  - 当读取数据时，数据按块读入缓冲区，其后的读操作则直接访问缓冲区
  - 当使用BufferedInputStream读取字节文件时，BufferedInputStream会一次性从文件中读取8192个(8Kb)，存在缓冲区中，直到缓冲区装满了，才重新从文件中读取下一个8192个字节数组 
  - 向流中写入字节时，不会直接写到文件，先写到缓冲区中直到缓冲区写满，BufferedOutputStream才会把缓冲区中的数据一次性写到文件里。使用 flush( ) 可以强制将缓冲区的内容全部写入输出流
  - 关闭流的顺序和打开流的顺序相反。只要关闭最外层流即可，关闭最外层流也会相应关闭内层节点流
  - flush ( )的使用：手动将buffer中内容写入文件
  - 如果是带缓冲区的流对象的 close( )，不但会关闭流，还会在关闭流之前刷新缓冲区，关闭后不能再写出



- **转换流**
- 处理流的一种
  
  - 属于 字符流
- 转换流提供了在字节流和字符流之间的转换
  - Java API 提供了两个转换流

    1. InputStreamReader：将 InputStream —> Reader

       - 需要和 InputStream “套接”

       - 构造器

         - public InputStreamReader (InputStream in)

         - public InputSreamReader (InputStream in, String charsetName)
       - 如： Reader isr = new InputStreamReader (System.in, ”gbk”);
  
  2. OutputStreamWriter：将 Writer —> OutputStream
       - 需要和OutputStream“套接”
       - 构造器 
       - public OutputStreamWriter (OutputStream out)
         - public OutputSreamWriter (OutputStream out, String charsetName)
  - 字节流中的数据都是字符时，转成字符流操作更高效
  - 很多时候我们使用转换流来处理文件乱码问题。实现编码和解码的功能



- 打印流

  - 实现将基本数据类型的数据格式转化为字符串输出
  - 打印流：PrintStream 和 PrintWriter
    - 提供了一系列重载的 print( ) 和 println ( )方法，用于多种数据类型的输出
    - 不会抛出IOException异常
    - 有自动flush功能
    - PrintStream 打印的所有字符，都使用平台的默认字符编码，转换为字节
    - 写入字符，应该使用 PrintWriter 类
    - ystem.out 返回的是 PrintStream 的实例

- 数据流

  - 为了方便地操作Java语言的 基本数据类型 和 String的数据，可以使用数据流

  - 数据流有两个类

    - DataInputStream 和 DataOutputStream

    - 分别“套接”在 InputStream 和 OutputStream 子类的流上

  - DataInputStream 中的方法

    - boolean readBoolean( ) 
    - byte readByte ( )
    - char readChar ( ) 
    - float readFloat ( )
    - double readDouble ( ) 
    - short readShort ( )
    - long readLong ( ) int readInt ( )
    - String readUTF ( ) 
    - void readFully (byte[ ] b)

  - DataOutputStream 中的方法
  - 将上述的方法的 read 改为相应的 write 即可
  - 读取不同数据类型时，读的顺序和之前写的顺序保持一致



- **对象流**

  - 用于存储和读取基本数据类型数据或 对象的处理流

  - 可以把 Java 中的对象写入到数据源中，也能把对象从数据源中还原回来

  - 序列化

    - 序列化
      1. 用 ObjectOutputStream 类保存基本类型数据或对象的机制
      2. 将内存中的 Java 对象保存到磁盘中、通过网络传输出去

    - 反序列化
      1. 用ObjectInputStream 类读取基本类型数据或对象的机制
      2. 讲磁盘或网络传输过来的对象，还原为内存中的一个 Java对象
    - 不能序列化 static 和 transient 修饰的成员变量

  - 对象的序列化

    - 对象序列化机制允许把内存中的 Java对象 —> 与平台无关的二进制流

      从而允许把这种二进制流，持久地保存在磁盘上、通过网络将这种二进制流传输到另一个网络节点

      当其它程序获取了这种二进制流，就可以恢复成原来的 Java对象

    - 序列化的好处在于可将任何实现了 Serializable 接口的对象 —>字节数据，使其在保存和传输时可被还原

    - 序列化是 RMI（Remote Method Invoke – 远程方法调用）过程的参数和返回值都必须实现的机制，而 RMI 是 JavaEE 的基础。因此序列化机制是 JavaEE 平台的基础

    - 如果需要让某个对象支持序列化机制，则必须让对象所属的类及其属性是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一。否则，会抛出 NotSerializableException异常

      1. Serializable

      2. Externalizable

    - 实现Serializable接口的类

      1. 提供一个表示序列化版本标识符的静态变量：private static final long serialVersionUID;
      2. serialVersionUID 用来表明类的不同版本间的兼容性。简言之，其目的是以序列化对象进行版本控制，有关各版本反序列化时是否兼容
      3. 如果类没有显示定义这个静态常量，它的值是Java运行时环境根据类的内部细节自动生成的。若类的实例变量做了修改，serialVersionUID 可能发生变化。故建议，显式声明

    - 简单来说，Java 的序列化机制是通过在运行时判断类的 serialVersionUID 来验证版本一致性的

      在进行反序列化时，JVM会把传来的字节流中的 serialVersionUID 与本地相应实体类的serialVersionUID进行比较

      如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。(InvalidCastException)

  - 使用对象序列化

    - 若某个类实现了 Serializable 接口，该类的对象就是可序列化的：

      1. 创建一个 ObjectOutputStream

      2. 调用 ObjectOutputStream 对象的 writeObject(对象) 输出可序列化对象

         注意写出一次，操作flush( ) 一次

    - 反序列化
      1. 创建一个 ObjectInputStream
      2. 调用 readObject ( ) 读取流中的对象 Object o = ObjectInputStream.readObject ( );

    - 强调：如果某个类的属性不是基本数据类型或 String 类型，而是另一个引用类型，那么这个引用类型必须是可序列化的，否则拥有该类型的 Field 的类也不能序列化



- 随机存取文件流
  - RandomAccessFile 声明在java.io包下，但直接继承于java.lang.Object类
  - 实现了DataInput、DataOutput这两个接口，也就意味着这个类可以读、可以写
  - RandomAccessFile 类支持 “随机访问” 的方式，程序可以直接跳到文件的任意地方来读、写文件
    - 支持只访问文件的部分内容
    - 可以向已存在的文件后追加内容
  - RandomAccessFile 对象包含一个记录指针，用以标示当前读写处的位置
  - RandomAccessFile 类对象可以自由移动记录指针：
    - long getFilePointer ( )：获取文件记录指针的当前位置
    - void seek (long pos)：将文件记录指针定位到 pos 位置
  - 构造器
    - public RandomAccessFile (File file, String mode) 
    - public RandomAccessFile (String name, String mode) 
  - 创建 RandomAccessFile 类实例需要指定一个 mode 参数，该参数指定 RandomAccessFile 的访问模式
    - r： 以只读方式打开
    - rw：打开以便读取和写入
    - rwd：打开以便读取和写入；同步文件内容的更新
    - rws：打开以便读取和写入；同步文件内容和元数据的更新
  - 如果模式为 r。不会创建文件，而是会去读取一个已经存在的文件，如果读取的文件不存在则会出现异常
  -  如果模式为 rw。如果文件不存在则会去创建文件，如果存在则不会创建
  - 我们可以用RandomAccessFile这个类，来实现一个多线程断点下载的功能，用过下载工具的朋友们都知道，下载前都会建立两个临时文件，一个是与被下载文件大小相同的空文件，另一个是记录文件指针的位置文件，每次暂停的时候，都会保存上一次的指针，然后断点下载的时候，会继续从上一次的地方下载，从而实现断点下载或上传的功能



- 字符集

  - 编码表的由来
    - 计算机只能识别二进制数据，早期由来是电信号。为了方便应用计算机，让它可以识别各个国家的文字。就将各个国家的文字用数字来表示，并一一对应，形成一张表，这就是编码表
  - 常见的编码表
    - ASCII：美国标准信息交换码。用一个字节的7位可以表示
    -  ISO8859-1：拉丁码表。欧洲码表用一个字节的8位表示
    - GB2312：中国的中文编码表。最多两个字节编码所有字符
    - GBK：中国的中文编码表升级，融合了更多的中文文字符号。最多两个字节编码
    - Unicode：国际标准码，融合了目前人类使用的所有字符。为每个字符分配唯一的字符码。所有的文字都用两个字节来表示
    - UTF-8：变长的编码方式，可用1-4个字节来表示一个字符



#### Java NIO

- Java NIO (New IO，Non-Blocking IO)是从Java 1.4版本开始引入的一套新的IO API，可以替代标准的Java IO API

- NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同，NIO支持面向缓冲区的(IO是面向流的)、基于

  通道的IO操作

  NIO将以更加高效的方式进行文件的读写操作

- Java API中提供了两套NIO

  - 标准输入输出NIO
  - 网络编程NIO

- java.nio.channels.Channel

  - FileChannel:处理本地文件
  - SocketChannel：TCP网络编程的客户端的Channel
  - ServerSocketChannel：TCP网络编程的服务器端的Channel
  - DatagramChannel：UDP网络编程中发送端和接收端的Channel

- 同时，NIO.2在java.nio.file包下还提供了Files、Paths工具类，Files包含

  了大量静态的工具方法来操作文件；Paths则包含了两个返回Path的静态

  工厂方法。

   Paths 类提供的静态 get() 方法用来获取 Path 对象：

  static Path get(String first, String … more) : 用于将多个字符串串连成路径

  static Path get(URI uri): 返回指定uri对应的Path路径



#### IO流与网络编程

- 网络基础

  - 计算机网络

    把分布在不同地理区域的计算机与专门的外部设备用通信线路互连成一个规模大、功能强的网络系统，从而使众多的计算机可以

    方便地互相传递信息、共享硬件、软件、数据信息等资源

  - 网络编程的目的
    
  - 直接或间接地通过网络协议与其它计算机实现数据交换，进行通讯
    
  -  网络编程中有三个主要的问题
    - 如何准确地定位网络上一台或多台主机
    - 如何定位主机上的特定的应用
    - 找到主机后如何可靠高效地进行数据传输

- 如何实现网络中的主机互相通信

  - 通信双方地址

    - IP

    - 端口号

  - 一定的规则 / 网络通信协议
  - OSI参考模型：模型过于理想化，未能在因特网上进行广泛推广
    - TCP/IP 参考模型：事实上的国际标准

- IP

  - 唯一的标识 Internet 上的计算机（通信实体）

  - 特点：不易记忆

  - 本地回环地址 / hostAddress：127.0.0.1 

  - 主机名 / hostName：localhost

  - IP地址分类方式1

    1. IPV4
       - 32位，4个字节
       - 大概42亿，30亿都在北美，亚洲4亿，2011年初已经用尽
       - 以点分十进制表示
       - 比如：192.168.0.1

    2. IPV6
       - 128位，16个字节
       - 写成8个无符号整数，每个整数用四个十六进制位表示，数之间用冒号/ ：分
       - 比如：3ffe:3201:1401:1280:c8ff:fe4d:db39:1984

  - IP地址分类方式2
    1. 公网地址 / 万维网使用
    2. 私有地址 / 局域网使用
       - 192.168.开头的就是私有址址
       - 范围为192.168.0.0 — 192.168.255.255
       - 专门为组织机构内部使用

- 端口号

  - 标识正在计算机上运行的进程（程序）

  - 不同的进程有不同的端口号

  - 被规定为一个 16 位的整数 0~65535

  - 端口分类：

    1. 公认端口

       - 0 — 1023

       - 被预先定义的服务通信占用
       - 如：HTTP占用端口80，FTP占用端口21，Telnet占用端口23 

    2. 注册端口

       - 1024 — 49151
       - 分配给用户进程或应用程序
       - 如：Tomcat占用端口8080，MySQL占用端口3306，Oracle占用端口1521

    3. 动态 / 私有端口

       - 49152 — 65535。

- InetAddress类

  - Internet上的主机有两种方式表示地址：

    - 域名 / hostName：www.atguigu.com

    - IP 地址 / hostAddress：202.108.35.210

  - InetAddress类主要表示IP地址，两个子类

    - Inet4Address
    - Inet6Address

  - InetAddress 类 对 象 含 有 一 个 Internet 主 机 地 址 的 域 名 和 IP 地 址 ：www.atguigu.com 和 202.108.35.210

  - 域名容易记忆，当在连接网络时输入一个主机的域名后，域名服务器 / DNS 负责将域名转化成 IP地址，这样才能和主机建立连接

  - InetAddress类没有提供公共的构造器，而是提供了如下几个静态方法来获取 InetAddress 实例

    - public static InetAddress getLocalHost ( )

    - public static InetAddress getByName (String host)

  - InetAddress 提供了如下几个常用的方法
  
    - public String getHostAddress ( )：返回 IP 地址字符串（以文本表现形式）
  
    - public String getHostName ( )：获取此 IP 地址的主机名
    - public boolean isReachable (int timeout)：测试是否可以达到该地址
  
- TCP / IP 协议簇

  - 传输层协议，有两个非常重要的协议

    - 传输控制协议TCP / Transmission Control Protocol

    - 用户数据报协议UDP(User Datagram Protocol)。 

  - TCP / IP 两个主要协议：传输控制协议 / TCP、网络互联协议 / IP，实际上是一组协议，包括多个具有不同功能且互为关联的协
  
  - IP / Internet Protocol 协议是网络层的主要协议，支持网间互连的数据通信
  
  - TCP/IP协议模型从更实用的角度出发，形成了高效的四层体系结构：物理链路层、IP层、传输层、应用层
-  TCP 协议

  - 使用TCP协议前，须先建立TCP连接，形成传输数据通道
  - 传输前，采用“**三次握手**”方式点对点通信，是可靠的
  - TCP协议进行通信的两个应用进程：客户端、服务端。
  - 在连接中可进行大数据量的传输
  - 传输完毕，需释放已建立的连接，效率低

- UDP协议
  - 将数据、源、目的封装成数据包，不需要建立连接
  - 每个数据报的大小限制在64K内 
  - 发送不管对方是否准备好，接收方收到也不确认，是不可靠的 
  - 可以广播发送
  - 发送数据结束时无需释放资源，开销小，速度快

- Socket

  - 利用套接字 / Socket 开发网络应用程序早已被广泛的采用，以至于成为事实上的标准 

  - 网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标识符套接字

  - 通信的两端都要有 Socket，是两台机器间通信的端点

  - 网络通信其实就是 Socket间的通信

  - Socket允许程序把网络连接当成一个流，数据在两个Socket间通过IO传输。

  - 主动发起通信的应用程序：客户端

  - 等待通信请求的：服务端

  - Socket分类

    - 流套接字 / stream socket：使用 TCP 提供可依赖的字节流服务

    - 数据报套接字 / datagram socket：使用 UDP 提供“尽力而为”的数据报服务

  - Socket 类的常用构造器
    - public Socket (InetAddress address, int port)：创建一个流套接字并将其连接到指定 IP 地址的指定端口号
    -  public Socket (String host, int port)：创建一个流套接字并将其连接到指定主机上的指定端口号

  - Socket 类的常用方法
    - public InputStream getInputStream ( )：返回此套接字的输入流。可以用于接收网络消息
    -  public OutputStream getOutputStream ( )：返回此套接字的输出流。可以用于发送网络消息
    - public InetAddress getInetAddress ( )：此套接字连接到的远程 IP 地址；如果套接字是未连接的，则返回 null
    - public InetAddress getLocalAddress ( )：获取套接字绑定的本地地址。 即本端的IP地址
    - public int getPort()此套接字连接到的远程端口号；如果尚未连接套接字，则返回 0
    - public int getLocalPort ( )：返回此套接字绑定到的本地端口。 如果尚未绑定套接字，则返回 -1。即本端的端口号 
    - public void close ( )：关闭此套接字。套接字被关闭后，便不可在以后的网络连接中使用（即无法重新连接或重新绑定）。需要创建新的套接字对象。 关闭此套接字也将会关闭该套接字的 InputStream 和OutputStream 
    - public void shutdownInput ( )：如果在套接字上调用 shutdownInput() 后从套接字输入流读取内容，则流将返回 EOF（文件结束符）。 即不能在从此套接字的输入流中接收任何数据
    - public void shutdownOutput ( )：禁用此套接字的输出流。对于 TCP 套接字，任何以前写入的数据都将被发送，并且后跟 TCP 的正常连接终止序列。 如果在套接字上调用 shutdownOutput() 后写入套接字输出流，则该流将抛出 IOException。 即不能通过此套接字的输出流发送任何数据

- 基于 Socket 的 TCP网络编程

  - 客户端、client
    1. 创建 Socket (intAddress, int port)
    2. 让1的实例调用 getInputStream ( ) / getInputStream ( )  获得输入流 / 输出流
    3. 读写操作
    4. 关闭资源
  - 服务端、server
    1. 创建 ServerSocket (int port)
    2. 让1的实例调用 accept ( ) 监听
    3. 让2的实例调用  getInputStream ( ) / getInputStream ( )  获得输入流 / 输出流
    4. 读写操作
    5. 关闭资源

- 基于 Socket 的 UDP网络编程

  - 类 DatagramSocket 和 DatagramPacket 实现了基于 UDP 协议网络程序
  - UDP数据报通过数据报套接字 DatagramSocket 发送和接收，系统不保证UDP数据报一定能够安全送到目的地，也不能确定什么时候可以抵达
  - DatagramPacket 对象封装了UDP数据报，在数据报中包含了发送端的IP地址和端口号以及接收端的IP地址和端口号
  - UDP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接收方的连接。如同发快递包裹一样

  - DatagramSocket 类的常用方法
    - public DatagramSocket (int port)：创建数据报套接字并将其绑定到本地主机上的指定端口。套接字将被绑定到通配符地址，IP 地址由内核来选择
    - public DatagramSocket (int port, InetAddress laddr)：创建数据报套接字，将其绑定到指定的本地地址。本地端口必须在 0 到 65535 之间（包括两者）。如果 IP 地址为 0.0.0.0，套接字将被绑定到通配符地址，IP 地址由内核选择 
    - public void close ( )：关闭此数据报套接字
    - public void send(DatagramPacket p)：从此套接字发送数据报包。DatagramPacket 包含的信息指示：将要发送的数据、其长度、远程主机的 IP 地址和远程主机的端口号
    - public void receive (DatagramPacket p)：从此套接字接收数据报包。当此方法返回时，DatagramPacket的缓冲区填充了接收的数据。数据报包也包含发送方的 IP 地址和发送方机器上的端口号。 此方法在接收到数据报前一直阻塞。数据报包对象的 length 字段包含所接收信息的长度。如果信息比包的长度长，该信息将被截短
    - public InetAddress getLocalAddress ( )：获取套接字绑定的本地地址
    - public int getLocalPort ( )：返回此套接字绑定的本地主机上的端口号
    - public InetAddress getInetAddress ( )：返回此套接字连接的地址。如果套接字未连接，则返回 null
    - public int getPort ( )：返回此套接字的端口。如果套接字未连接，则返回 -1
  - DatagramPacket 类的常用方法
    - public DatagramPacket (byte[] buf,int length)：构造 DatagramPacket，用来接收长度为 length 的数据包。 length 参数必须小于等于 buf.length
    - public DatagramPacket (byte[ ] buf, int length, InetAddress address, int port)：构造数据报包，用来将长度为 length 的包发送到指定主机上的指定端口号。length参数必须小于等于 buf.length 
    - public InetAddress getAddress ( )：返回某台机器的 IP 地址，此数据报将要发往该机器或者是从该机器接收到的
    - public int getPort ( )：返回某台远程主机的端口号，此数据报将要发往该主机或者是从该主机接收到的
    - public byte[] getData ( )：返回数据缓冲区。接收到的或将要发送的数据从缓冲区中的偏移量 offset 处开始，持续 length 长度
    - public int getLength ( )：返回将要发送或接收到的数据的长度

- URL编程

  - URL / Uniform Resource Locator：统一资源定位符，它表示 Internet 上某一资源的地址

  - 它是一种具体的URI，即URL可以用来标识一个资源，而且还指明了如何 locate 这个资源

  - 通过 URL 我们可以访问 Internet 上的各种网络资源，比如最常见的 www，ftp 站点

  - 浏览器通过解析给定的 URL 可以在网络上查找相应的文件或其他资源

  - URL的基本结构由5部分组成 

    - <传输协议>://<主机名>:<端口号>/<文件名>#片段名?参数列表

    - 例如 

      http://192.168.1.100:8080/helloworld/index.jsp#a?username=shkstart&password=123

    - #片段名：即锚点，例如看小说，直接定位到章节

    - 参数列表格式：参数名=参数值&参数名=参数值....

  - 为了表示URL，java.net 中实现了类 URL。我们可以通过下面的构造器来始化一个 URL 对象

    - public URL (String spec)：通过一个表示URL地址的字符串可以构造一个URL对象

      例如：URL url = new URL ("http://www. atguigu.com/"); 

    - public URL(URL context, String spec)：通过基 URL 和相对 URL 构造一个 URL 对象

      例如：URL downloadUrl = new URL(url, “download.html")

    - public URL(String protocol, String host, String file);

      例如：new URL("http", "www.atguigu.com", “download. html");

    - public URL(String protocol, String host, int port, String file); 

      例如: URL gamelan = new URL("http", "www.atguigu.com", 80, “download.html");

    -  URL类的构造器都声明抛出非运行时异常，必须要对这一异常进行处理，通常是用 try-catch 语句进行捕获

  - 一个URL对象生成后，其属性是不能被改变的，但可以通过它给定的方法来获取这些属性

    - public String getProtocol ( )：获取该URL的协议名
    - public String getHost ( )：获取该URL的主机名
    - public String getPort ( )：获取该URL的端口号
    - public String getPath ( )：获取该URL的文件路径
    - public String getFile ( )：获取该URL的文件名
    - public String getQuery ( )：获取该URL的查询名

  - 针对 HTTP协议的 URLConnection类

    - URL的方法 openStream()：能从网络上读取数据

    - 若希望输出数据，例如向服务器端的 CGI （公共网关接口-Common Gateway Interface-的简称，是用户浏览器和服务器端的应用程序进行连接的接口）程序发送一些数据，则必须先与URL建立连接，然后才能对其进行读写，此时需要使用URLConnection 。 

    - URLConnection：表示到URL所引用的远程对象的连接。当与一个URL建立连接时，首先要在一个 URL 对象上通过方法 openConnection ( ) 生成对应的 URLConnection对象。如果连接过程失败，将产生IOException

    - 举例

      URL netchinaren = new URL ("http://www.atguigu.com/index.shtml"); 

      URLConnectonn u = netchinaren.openConnection( ); 

  - URI、URL、URN的区别

    - URI，是uniform resource identifier，统一资源标识符，用来唯一的标识一个资源
    - URL，是uniform resource locator，统一资源定位符，它是一种具体的URI，即URL可以用来标识一个资源，而且还指明了如何locate这个资源
    - URN，是uniform resource name，统一资源命名，是通过名字来标识资源，比如mailto:java-net@java.sun.com
    - 也就是说，URI是以一种抽象的，高层次概念定义统一资源标识，而URL和URN则是具体的资源标识的方式。URL和URN都是一种URI