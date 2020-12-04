## Java代码编译方式和JVM三种执行模式

#### 编译分类

- 前端编译

  - 描述
    - 把 Java 源文件编译为 Class 文件的过程
    - 常见的前端编译器
      - Oracle Javac
      - Eclipse JDT中的增量式编译器（ECJ）

  - 优点
    - 辅助实现了 Java 新语法：泛型、内部类等
    - 编译成 Class 文件直接给 JVM 解释器执行，省去编译时间，加快启动速度
  - 缺点
    - 几乎没有任何优化代码运行效率的措施
    - 解释器执行效率低

- 后端编译

  - 描述
    - 在 JVM 运行时，通过内置的即时编译器（Just In Time Compiler-JIT），把 Class 文件字节码编译为本地机器码
    - 常见的后端编译器
      - HotSpot 虚拟机的 C1、C2 编译器

  - 优点
    - 运行时收集监控信息，把热点代码编译为本地机器码，并进行各种优化，例如
      - 运行数据分析，把堆栈操作转换为寄存器操作
      - 消除子表达式等
    - 大大提升了执行效率
  - 缺点
    - 收集监控信息影响程序运行
    - 编译过程占用时间，使得启动速度变慢等
    - 编译过程占用内存
    - 使用较少的 diam 的程序无法从即时编译中收益

- 静态提前编译

  - 描述
    - Ahead Of Time（AOT）编译
    - 在运行期直接把Jaa源文件编译为本地机器码
  - 优点
    - 编译不占用运行时间，加快启动速度
    - 编译本地机器码直接保存到磁盘，不占用内存
  - 缺点
    - Java 语言动态性带来了额外复杂度，影响静态编译代码的质量
    - 此方式一般不如 JIT 编译的质量



#### 三种执行模式

- 解释执行模式

  - 该模式下表示全部代码均是解释执行，不做任何JIT编译
  - 这种模式会降低运行速度，通常低10倍或者更多
  - 要开启这种模式，使用`-Xint`参数

  ```java
  java -Xint -version
  java version "1.8.0_71"
  Java(TM) SE Runtime Environment (build 1.8.0_71-b15)
  Java HotSpot(TM) 64-Bit Server VM (build 25.71-b15, interpreted mode)
  ```

- 编译执行模式

  - 该模式下不管是否热点代码，对所有的函数，都进行编译执行
  - JVM 在第一次使用时就会把所有的字节码编译为本地代码，从而优化执行速度，绕开缓慢的解释器
  - 这种模式没有让 JVM 启用 JIT 编译器的全部功能
  - 要开启这种模式，使用`-Xcomp`参数

  ```java
  java -Xcomp -version
  java version "1.8.0_71"
  Java(TM) SE Runtime Environment (build 1.8.0_71-b15)
  Java HotSpot(TM) 64-Bit Server VM (build 25.71-b15, compiled mode)
  ```

- 混合模式

  - JVM 默认的执行模式，部分函数会解释执行，部分会编译执行
  - 如果函数调用频率高，被反复使用，就会认为是热点代码，该函数就会被编译执行