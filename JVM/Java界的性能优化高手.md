## Java界的性能优化高手

#### 编译期优化

- 语法糖是个好东西，可以帮助我们进一步封装Java语言的使用方法，使用更方便，且编译后会解除语法糖
- 虽然语法糖简化了 Java 的编写，却又给语言本身蒙上一层面纱
- 常用的语法糖
  - 类型擦除
  - 自动装箱、拆箱
  - 遍历循环
  - 条件



#### 运行期优化

- *.java 源文件，经编译后生成 *.class 文件，加载到 JVM 之后，会由即时编译器（JIT）基于性能优化考虑，重新编译，生成本地机器码
- 解释器、Interperter
  - 计算机尤其是 CPU，只能执行相对较少的特定指令，这些指令称为汇编或者二进制指令
  - CPU 执行的所有程序，最终都需要转换为这些指令
  - 不同的 CPU 指令集有所区别，如果直接把程序翻译为机器指令，那么在不同平台下面，都需要重新编译一次
  - Java 为了能够实现 “一次编译，到处运行” 的效果，把源代码编译成了字节码这种中间表示形式
  - 字节码中保存的是 JVM 特有的指令集
  - 只要为不同的平台分别编写 JVM，那么就可以把同样的字节码分别在不同的平台运行了，从而达到了可移植的效果，这也是解释性语言的一个特点
  - 解释器：用来在JVM中解释运行字节码的
- 即时编译器、JIT
  - 只用解释器的话，每次都需要进行解释执行，效率比较低
    - JVM 能够将字节码编译为平台二进制代码，该编译一般在程序执行的过程中发生，处理这种编译动作的编译器称为即时编译器、JITa（Just in time）
    - HotSpot JVM 中 HotSpot 的由来：来自于它用于编译代码的方法，在一般的程序中，有些代码会频繁执行，而这部分代码是影响程序性能的关键代码，这些代码称为热点，代码执行的越频繁，就越是热点代码
    - 随着代码执行次数增多，JVM 可以监控到执行次数越多的方法，对这些代码进行优化，JIT 编译器目前主要有两类：Client Compiler（C1 编译器），Server Compiler（C2 编译器）
    - Client Compiler（C1 编译器）
      - 专用于任何类型的客户端应用程序
      - 客户端编译器会尝试尽快优化和编译代码，从而缩短启动时间
    - Server Compiler（C2 编译器）
      - 为了长时间运行的服务端应用程序设计的，因为 C2 编译器会长时间观察和分析代码，并且通过分析结果对编译后端代码进行更好地优化



#### JVM进化史

- 早期JVM
  - 只包含解释器，并不会进行即时编译
- 支持 JIT 的 JVM
  - 随着对执行效率的要求，逐渐引入了 Client Compiler 以及 Server Compiler，也就是 C1 和 C2 编译器，早期这两个编译器是不同的体系结构，所以JVM中会提供两个单独的实现程序
  - `%JAVA_HOME%/jre/bin`下面有`server`和`client`文件夹，里面分别对应是 C2 和 C1 的实现
  - 一次只能使用其中一个编译器，通过`-client`和`-server`参数指定
  - 这个时候引入了三种编译模式
    - 解释执行：该模式下表示全部代码均是解释执行，不做任何JIT编译，要开启这种模式，使用`-Xint`参数
    - 编译执行：该模式下不管是否热点代码，对所有的函数，都进行编译执行，要开启这种模式，使用`-Xcomp`参数
    - 混合执行：JVM默认的执行模式，部分函数会解释执行，部分会编译执行。如果函数调用频率高，被反复使用，就会认为是热点代码，该函数就会被编译执行
- 支持分层编译的 JVM
  - 将 C1 和 C2 编译器混合使用，以达到既有快速启动又具有尽可能好的性能的目的，从 Java7 开始就诞生了，即分层编译
  - 分层编译运行机制
    - 
  - 在 Java8 中，默认是启用分层编译的
  - 要禁用分层编译，可以使用`XX:-TieredCompilation`
  - 要使用分层编译，请务必使用 Server 模式，Client 模式不支持，会自动忽略掉



#### 逆向优化

- 编译器优化之后，如果后面运行发现优化优化不合理，进行逆向优化，退回到解释状态继续执行
- 主要表现为：某一次运行不会执行到之前编译好的本地代码了，或者出现了一些僵尸代码



#### 何时触发编译

- HotSpot 虚拟机主要通过基于计数器的热点探测方法为每个方法准备两类计数器：方法调用计数器，回边计数器

- 触发即时编译或者 OSR

  - 方法调用计数器 + 回边计数器 > 回边计数器阈值
  - 方法调用计数器 + 回边计数器 > 方法调用计数器阈值

- 方法调用计数器

  - 主要用于：统计方法调用次数

  - 如果一个方法被反复调用，那么就有可能被认定为是热点代码，从而进行编译
  - 会热度衰减
    - 默认情况下，每次 GC 时会对调用计数器进行减半的操作，导致有些方法一直达不到触发 C2 编译的阀值

- 回边计数器

  - 主要用于：统计一个方法中执行循环体代码的次数
  - 当一个循环执行到某位，或者执行到了 continue 语句的时候，就会触发一个回边指令，这个时候回边计数器值就 +1
  - 不会热度衰减

  - 如果一个循环体被反复执行，那么循环体所在的方法就有可能被认为是热点代码，从而触发编译
  - 可能在执行循环过程中就触发了编译，循环代码编译完成后，JVM 会立刻替换掉正在执行的代码，下一次循环迭代执行速度更快的编译版本，所以称为栈上替换（On Stack Replacement OSR，方法栈帧还在栈上，方法就被替换了）


