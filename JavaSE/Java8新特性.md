## Java8新特性

#### 新特性简介

- 速度更快
- 代码更少：增加了新的语法 Lambda表达式
-  强大的 Stream API
- 便于并行
- 最大化减少空指针异常：Optional 类
- Nashorn引擎，允许在JVM上运行JS应用



#### 并行流与串行流

- 并行流就是把一个内容分成多个数据块，并用不同的线程分别处理每个数据块的流
- 相比较串行的流，并行的流可以很大程度上提高程序的执行效率
- Java 8 中将并行进行了优化，我们可以很容易的对数据进行并行操作
- Stream API 可以声明性地通过 parallel() 与 sequential() 在并行流与顺序流之间进行切换



#### 函数式 / Functional 接口

- 只包含一个抽象方法的接口，称为函数式接口

- 我们可以在一个接口上使用 @FunctionalInterface 注解，这样做可以检查它是否是一个函数式接口

- 在 java.util.function 包下定义了Java 8 的丰富的函数式接口



####  **Lambda** 表达式

- Lambda 是一个匿名函数，我们可以把 Lambda 表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）

- 使用它可以写出更简洁、更灵活的代码

- 本质：作为函数式接口的实例

- 语法

  - 在Java 8 语言中引入的一种新的语法元素和操作符： “ -> ” ， 该操作符被称为 Lambda 操作符 / 箭头操作符

  - “ -> ” 将 Lambda 分为两个部分

    1. 左侧：
       - 指定了 Lambda 的形参列表：抽象方法的的参数列表
       - 数据类型可以省略：类型推断
       - 只有一个参数，( ) 可以省略 

    2. 右侧
       - 指定了 Lambda 体，是抽象方法的实现逻辑，也即Lambda 表达式要执行的功能
       - 只有一条执行语句，{ } 可以省略
       - 只有一条 return 语句，{ }、return关键字 可以省略

- 使用（六种情况）

  - 无参、无返回值
    - Runnable r1 = ( ) -> {System.out.println("hello")};
  - 一个参数，无返回值
    - Comsumer<String> con = (String s) -> {System.out.println(s)};
  - 只需要一个参数时，小括号可以省略
    -  Comsumer<String> con = s -> {System.out.println(s)};
  - 数据类型可以省略，编译器可以推断出
    - Comsumer<String> con = ( ) -> {System.out.println(s)};
  - 多个参数，多条执行语句，可以有返回值
    - Comparator<Integer> com = (01, 02) -> {System.out.println(01); System.out.println(o2); return o1.comparrTo(02);};
  - 一条执行语句，return 与 { } 可以省略
    - Comparator<Integer> com = (01, 02) -> {return o1.comparrTo(02)};
    - Comparator<Integer> com = (01, 02) -> o1.comparrTo(02);
    - Runnable r1 = ( ) -> System.out.println("hello");

- 类型推断

  - Lambda 表达式中无需指定类型，程序依然可以编译，这是因为 javac 根据程序的上下文，在后台推断出了参数的类型



#### 方法引用与构造器引用

- 方法引用 / Method References

  - 本质：Lambda表达式，也就是函数式接口的实例

  - 要求

    - 实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的方法的参数列表和返回值类型保持一致

  - 格式

    - 使用操作符 “ :: ” 
    - 对象 / 对象 :: 方法名

  - 使用（三种情况）

    - 对象 :: 实例方法名

    - 类 :: 静态方法名

    - 类 :: 非静态方法名

- 构造器引用 / Construct Reference

  - 格式
    - ClassName :: new
  -   与函数式接口相结合，自动与函数式接口中方法兼容
  - 可以把构造器引用赋值给定义的方法，要求构造器参数列表要与接口中抽象方法的参数列表一致！且方法的返回值即为构造器对应类的对象

- 数组引用

  格式： type[ ] :: new



#### Stream API

- Stream 是 Java8 中处理集合的关键抽象概念，它可以指定对集合进行的操作：执行非常复杂的查找、过滤、映射数据...

- Stream API 对集合数据进行操作，就类似于使用 SQL 执行的数据库查询。也可以使用 Stream API 来并行执行操作

- Stream 和 Collection 集合的区别

  - Collection 

    - 是一种静态的内存数据结构：容器
    - 主要面向内存，存储在内存中

  - Stream 

    - 计算

    - 主要面向 CPU，通过 CPU 实现计算

  - Collection 讲的是数据，Stream讲的是计算

- 注意

  - Stream 自己不会存储元素

  - Stream 不会改变源对象，而是返回一个持有结果的新 Stream

    ③Stream 操作是延迟执行的，等到需要结果的时候才执行

- Stream 的操作三个步骤

  - 创建 Stream
    - 一个数据源（如：集合、数组），获取一个流
  - 中间操作
    - 一个中间操作链，对数据源的数据进行处理
  - 终止操作 / 终端操作
    - 一旦执行终止操作，就执行中间操作链，并产生结果
    - 之后，不会再被使用

- 创建 Stream

  1. 方式一

  - 通过集合
  - 方法
    - default Stream<E> stream ( )：返回一个顺序流
    - default Stream<E> parallelStream ( )：返回一个并行流

  2. 方式二

  - 通过数组
  - 方法
    - static <T> Stream<T> stream (T[ ] array)：返回一个流
  - 重载形式
    - public static IntStream stream (int[ ] array)
    - public static LongStream stream (long[ ] array)
    - public static DoubleStream stream (double[ ] array)

  3. 方式三：

  - 通过 Stream 的 of ( )
  - 方法
    - public static<T> Stream<T> of (T... values)：返回一个流
    - 可以接收任意数量的参数

  4. 方式四

  - 创建无限流
  - 方法
    -  public static<T> Stream<T> iterate (final T seed, final UnaryOperator<T> f)：迭代
    - public static<T> Stream<T> generate (Supplier<T> s)：生成

- Stream 的中间操作

  - 多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，否则中间操作不会执行任何的处理
  - 终止操作时一次性全部处理，称为“惰性求值”

  1. 筛选与切片
     - filter (Predicate p)：接收 Lambda ， 从流中排除某些元素
     - distinct ( )：筛选，通过流所生成元素的 hashCode ( ) 和 equals ( ) 去除重复元素
     - limit (long maxSize)：截断流，使其元素不超过给定数量
     - skip (long n)：跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit (n) 互补
  2. 映射
     - map (Function f)：接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素
     - mapToDouble (ToDoubleFunction f)：接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 DoubleStream
     - mapToInt (ToIntFunction f)：接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 IntStream
     - mapToLong (ToLongFunction f)：接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 LongStream
     - flatMap (Function f)：接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流
  3. 排序
     - sorted ( )：产生一个新流，其中按自然顺序排序
     - sorted (Comparator com)：产生一个新流，其中按比较器顺序排序，可以用 Lambda：(o1, o2) -> {...}

- Stream 的终止操作

  - 终端操作会从流的流水线生成结果
  - 结果可以是任何不是流的值，例如：List、Integer，甚至是 void 
  - 流进行了终止操作后，不能再次使用

  1. 匹配与查找

     - allMatch (Predicate p)：检查是否匹配所有元素

     - anyMatch (Predicate p)：检查是否至少匹配一个元素

     - noneMatch (Predicate p)：检查是否没有匹配所有元素

     - findFirst ( )：返回第一个元素

     - findAny ( )：返回当前流中的任意元素

     - count ( )：返回流中元素总数

     - max (Comparator c)：返回流中最大值

     - min (Comparator c)：返回流中最小值

     - forEach (Consumer c)：Stream API 使用内部迭代

       使用 Collection 接口需要用户去做迭代：外部迭代

  2. 归约

     - reduce (T iden, BinaryOperator b)：可以将流中元素反复结合起来，得到一个值。返回 T
     - reduce (BinaryOperator b) 可以将流中元素反复结合起来，得到一个值。返回 Optional<T>

  3. 收集

     - collect (Collector c)：将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总
     - Collector 接口中方法的实现决定了如何对流执行收集的操作：收集到 List、Set、Map)



#### Optional类

- Optional<T> 类(java.util.Optional) 是一个容器类

- 可以保存类型T的值，代表这个值存在。或只保存 null，表示这个值不存在

- 原来用 null 表示一个值不存在，现在 Optional 可以更好的表达这个概念，并且可以避免空指针异常

- 这是一个可以为null的容器对象。如果值存在则 isPresent ( ) 会返回 true，调用 get ( ) 会返回该对象

- Optional提供很多有用的方法，这样我们就不用显式进行空值检测

  - 创建 Optional类 对象的方法

    - Optional.of (T t)：创建一个 Optional 实例，t 必须非空
    - Optional.empty ( )：创建一个空的 Optional 实例
    - Optional.ofNullable (T t)：t 可以为 null

  - 判断 Optional 容器中是否包含对象
  - boolean isPresent ( )：判断是否包含对象
    - void ifPresent (Consumer<? super T> consumer)：如果有值，就执行 Consumer接口的实现代码，并且该值会作为参数传给它

  - 获取 Optional 容器的对象
  - T get ( )：如果调用对象包含值，返回该值，否则抛异常
    - T orElse (T other)：如果有值则将其返回，否则返回指定的other对象
  - T orElseGet (Supplier<? extends T> other)：如果有值则将其返回，否则返回由 Supplier 接口实现提供的对象
    - T orElseThrow (Supplier<? extends X> exceptionSupplier)：如果有值则将其返回，否则抛出由Supplier接口实现提供的异常

