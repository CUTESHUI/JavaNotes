## Java集合

#### Java 集合框架概述

- 一方面， 面向对象语言对事物的体现都是以对象的形式，为了方便对多个对象的操作，就要对对象进行存储

- 另一方面，使用 Array / 数组 存储对象方面具有一些弊端，而 Java 集合就像一种容器，可以动态地把多个对象的引用放入容器中

- 集合数组都是对多个数据进行存储的结构，简称 Java容器

  - 此时的存储，主要指内存层面的
  - 不涉及到持久层面的 ( txt、avi、数据库等 )

- **数组**在存储数据方面的特点

  - 数组初始化以后，长度就确定了
  - 数组声明的类型，就决定了进行元素初始化时的类型

- **数组**在存储数据方面的弊端

  - 数组初始化以后，长度就不可变了，不便于扩展
  - 数组中提供的属性和方法少，不便于进行添加、删除、插入等操作，且效率不高
  - 无法直接获取存储元素的个数
  - 数组存储的数据是有序的、可以重复的

- Java 集合类可以用于存储数量不等的多个对象，还可用于保存具有映射关系的关联数组

- Java 集合可分为 Collection 和 Map 两种体系

  - Collection接口：单列数据，用来存储一个一个的对象

    - List：元素有序、可重复的集合、“ 动态 ” 数组

    - Set：元素无序、不可重复的集合、高中讲的 “ 集合 ”


  - Map接口：双列数据，用来存储一对一对的对象，保存具有映射关系 “ key-value对 ” 的集合、高中讲的函数 y = f (x)



#### Collection 接口

- Collection 接口是 List、Set 和 Queue 接口的父接口
- 该接口里定义的方法，可用于操作 Set 集合、 List集合、Queue 集合
- JDK不提供此接口的任何直接实现，而是提供更具体的子接口 ( 如：Set 和 List ) 实现 
- 在 Java5 之前，Java 集合会丢失容器中所有对象的数据类型，把所有对象都当成 Object 类型处理
- 从 JDK 5.0 增加了泛型以后，Java 集合可以记住容器中对象的数据类型

- 方法

  1. 添加
     - add (Object obj )
     - addAll (Collection coll ) 

  2. 获取有效元素的个数
     - int size( )

  3. 清空集合
     - void clear( )

  4. 是否是空集合 
     - boolean isEmpty( )

  5. 是否包含某个元素
     -  boolean contains (Object obj )：是通过元素的 equals( ) 来判断是否是同一个对象，通常自定义类需要重写equals( )
     - boolean containsAll (Collection c )：也是调用元素的 equals 方法来比较的，拿两个集合的元素挨个比较
  6. 删除
     - boolean remove (Object obj ) ：通过元素的equals方法判断是否是要删除的那个元素，只会删除找到的第一个元素
     - boolean removeAll (Collection coll)：取当前集合的差集
  7. 取两个集合的交集
     - boolean retainAll (Collection c)：把 交集 的结果存在当前集合中，不影响 c 

  8. 集合是否相等
     - boolean equals (Object obj)

  9. 转成对象数组
     - Object[ ] toArray( )

  10. 获取集合对象的哈希值
      - hashCode( )

  11. 遍历
      - iterator( )

- 在Collection 接口的实现类的对象中添加数据 obj 时，一定要重写 obj 所在类的 equals( )



- **Iterator 迭代器接口**

  - Iterator 对象，称为迭代器 (设计模式的一种)，主要用于遍历 Collection 集合中的元素

  -  GOF给迭代器模式的定义为：提供一种方法访问一个容器(container)对象中各个元素，而又不需暴露该对象的内部细节，迭代器模式，就是为容器而生

  - Iterator 仅用于遍历集合，并非一个容器

  - Iterator 本身并不装数据对象，如果需要创建 Iterator 对象，则必须有一个被迭代的集合

  -  集合对象每次调用 iterator( ) 都得到一个全新的迭代器对象，默认游标都在集合的第一个元素之前

  - 方法

    - boolean hasNext( )：判断迭代器里是否还有数据
    - next( )：返回出一个迭代器里的数据

  - Iterator 可以删除集合的元素，但是是遍历过程中通过迭代器对象的 remove( )，不是集合对象的 remove( )

  - Iterrator 遍历了一次后，hasNext( ) 在上次已经到最后。之后还想再遍历，需要重新声明一下 Iterator对象 (也就是让hasNext( )回到最初 )去遍历

  - foreach 循环遍历集合元素

    - 增强 for 循环

    - Java 5.0 提供了 foreach 循环迭代访问 Collection和数组
    - 遍历操作不需获取 Collection 或数组长度，无需使用索引访问元素
    - 遍历集合的底层调用 Iterator完成操作
    - for ( 集合元素类型 局部变量 : 集合对象)
    - for ( Object o : collection )
    - 原理：从集合中取一个元素赋给局部变量，然后输出 / 操作局部变量，再取一个元素...

    

- **List 接口**

  - List 集合类中元素有序、可重复
  - List 集合中的每个元素都有其对应的顺序索引，也就有对应的方法
  - List 容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素
  - JDK API 中 List 接口的实现类常用的有：ArrayList、LinkedList、Vector
  - ArrayList、LinkedList、Vector 的异同
    - 相同
      1. 三个类都实现了List接口
      2. 存储有序，可重复的数据
    - 不同
      1. ArrayList
         - 作为 List接口的主要实现类
         - 线程不安全，效率高
         - 底层使用 Object[ ] elementDate 存储
         - 扩容 1.5 倍
      2. LinkedList 
         - 作为 List接口的其次实现类
         - 底层使用双向链表存储
         - 对于频繁的插入、删除，效率比ArrayList高
      3. Vector
         -  作为 List接口的古老实现类
         - 线程安全，效率低
         - 底层使用 Object[ ] elementDate存储
         - 扩容 2 倍
  - 常用方法
    - void add (int index, Object ele)：在 index 位置插入ele元素
    - boolean addAll (int index, Collection eles)：从 index 位置开始将eles中的所有元素添加进来
    - Object get (int index)：获取指定 index 位置的元素
    - int indexOf (Object obj)：返回 obj 在集合中首次出现的位置，不存在就返回 -1
    - int lastIndexOf (Object obj)：返回 obj 在当前集合中末次出现的位置
    - Object remove (int index)：移除指定 index位置的元素，并返回此元素
    - Object set (int index, Object ele)：设置指定 index位置的元素为ele
    - List subList (int fromIndex, int toIndex)：返回从 fromIndex 到 toIndex位置的子集合
  - 方法总结：增、删、改、查、遍历 ( 普通遍历、增强for遍历、迭代器 )

  

- **ArrayList**

  - ArrayList 在 JDK1.8 之前与之后的实现区别？

    - JDK1.7：ArrayList 像饿汉式，直接创建一个初始容量为10的数组
    - JDK1.8：ArrayList 像懒汉式，一开始创建一个长度为0的数组{ }，当添加第一个元素时再创建一个始容量为10的数组。懒加载，延迟了数组的创建，节省了内存



- **LinkedList**

  - 对于频繁的插入或删除元素的操作，建议使用LinkedList类，效率较高
  - 新增方法：
    - void addFirst (Object obj) 
    - void addLast (Object obj) 
    - Object getFirst ( )
    - Object getLast ( )
    - Object removeFirst ( )
    - Object removeLast ( )

  - 底层是双向链表，内部没有声明数组
  - 定义内部类Node，作为 LinkedList 中保存数据的基本结构
  - 定义Node类型的 first 和 last 用于记录首末元素
  - Node除了保存数据，还定义了两个变量：
    - prev变量记录前一个元素的位置
    - next变量记录下一个元素的位置



- **Set 接口**

  - set 集合类中元素无序、不可重复

  - set 接口没有提供额外的方法，使用的都是父接口 Collection 的方法

  - Set 判断两个对象是否相同不是使用 == 运算符，而是根据 equals( ) 方法

  - JDK API 中 List 接口的实现类常用的有：HashSet、LinkedHashSet、TreeSet

  - HashSet、LinkedHashSet、TreeSet 的异同

    1. HashSet
       - 作为 Set 接口的主要实现类
       - 线程不安全，效率高
       - 可以存储 null
    2. LinkedHashSet
       - 作为 HashSet 的子类
       - 遍历 Set 元素时，可以按照添加的顺序遍历
    3. TreeSet
       - 底层使用二叉树 / 红黑树存储
       - 自然排序，定制排序

  - 无序、不可重复

    以 HashSet为例

    1. 无序
       - 不等于随机性
       - 相较于 List，存储的数据在底层数组中并非按数组索引的顺序添加
       - 根据数据的 哈希值 确定
    2. 不可重复
       - 保证添加的元素按照 equals( ) 判断时，不能返回 true，因为相同是 true

  - 添加元素的过程，以 HashSet为例

    - 我们向 HashSet 中添加元素 a，首先调用 a 所在类的 hashCode( )，得出 a 的哈希值

    - 此哈希值通过某种算法，得出其在 HashSet 底层数组中存放的位置 / 索引位置

    - 判断此位置上是否有元素

      - 如果没有，a 添加成功

      - 如果有其他元素b / 以链表形式存在的多个元素，首先比较 a 与 b 的哈希值
        - 如果哈希值不同，a 添加成功
        - 如果哈希值相同，调用 a 所在类的 equals( )
          - 如果返回 true，a 失败
          - 如果返回 false，a 添加成功

    - 对于添加成功的情况，a 与已经存在的其他元素，以链表的形式存储

    - JDK7， a 放入数组中，指向原来的元素

    - JDK8，原来的元素在数组中，指向 a

  - 为什么重写 equals( )，也一定要重写 hashcode( )

    - 假设任何两个 object 的 hashcode 都是不同的，也就是 hash function是有效的
    - 那么在这个条件下，如果有两个相等的 object ，不重写 hashcode( )，那算出来的哈希值都不一样，就会去到不同的 buckets ，也就迷失在茫茫人海中，再也无法相认了。
      1. hashcode( ) 决定了 key 放在这个桶里的编号，也就是在数组里的 index；
      2. equals( ) 是用来比较两个 object 是否相同的。

  - 向 Set 中添加数据，一定要重写数据所在类的 equals( )、hashcode( )

  - 重写 hashCode( ) 的基本原则

    - 在程序运行时，同一个对象多次调用 hashCode( ) 应该返回相同的值
    - 当两个对象的 equals( ) 方法比较返回 true 时，这两个对象的 hashCode( ) 的返回值也应相等

  - 重写 equals( ) 的基本原则

    - 相等的对象必须具有相等的散列码，所以一定要重写 hashcode( )
    - 重写 equals( ) 的时候一般都需要同时复写hashCode方法
    - 通常参与计算 hashCode( ) 的对象的属性也应该参与到 equals( ) 中进行计算



- **HashSet**
  - HashSet 是基于 HashMap 来实现的，底层采用 Hashmap 的 key 来储存元素
  - 基本操作都是 O(1) 的时间复杂度
  - HashSet 底层是 数组+链表，初始容量为16，当如果使用率超过0.75 ( 16*0.75 = 12 ) 就会扩大容量为原来的2倍 ( 16扩容为32，依次为64、128 ...)



- **LinkedHashSet**
  - 是 HashSet 的子类
  - 据元素的 hashCode 值来决定元素的存储位置
  - 使用双向链表维护元素的次序，这使得元素看起来是以插入顺序保存的
  - LinkedHashSet 插入性能略低于 HashSet，但在迭代访问 Set 里的全部元素时有很好的性能



- **TreeSet**

  - TreeSet 是 SortedSet 接口的实现类 
- TreeSet 可以确保集合元素处于排序状态
  - TreeSet底层使用 红黑树结构存储数据
- 向 TreeSet 添加的数据，要求是相同类的对象
  - 新增的方法：

    - Comparator comparator ( )

    - Object first ( )

    - Object last ( )

    - Object lower (Object e)

    - Object higher (Object e)

    - SortedSet subSet (fromElement, toElement) 

    - SortedSet headSet (toElement) 

    - SortedSet tailSet (fromElement)  

  - TreeSet 两种排序方法：自然排序和定制排序
- 默认情况下，TreeSet 采用自然排序
  - 在自然排序中，比较两个对象是否相同的标准：comparable 的 compareTo( ) 返回0，而不是equals( ) 
  - 在定制排序中，比较两个对象是否相同的标准：comparator 的 compare( ) 返回0，而不是equals( ) 



#### Map

- 双列数据，存储 key—value对的数据
- 类似高中的函数 y = f(x)

- Map 中的 key 和 value 都可以是任何引用类型的数据

- Map 中的 key 用 Set 来存放，不允许重复

- 同一个 Map 对象所对应的类，必须重写 hashCode( ) 和 equals( )

- 常用String类作为 Map 的 “ 键 ”

-  key 和 value 之间存在单向一对一关系，即通过指定的 key 总能找到唯一的、确定的 value

- Map接口的常用实现类：HashMap、TreeMap、LinkedHashMap、Properties

  1. HashMap

     - 作为 Map 的主要实现类

     - 线程不安全，效率高
     - 可以存储 null 的 key、value
     - JDK 7 之前：数组+链表
     - JDK 8 之后：数组+链表+红黑树

  2. linkedHashMap

     - 是HashMap的子类
     - 遍历 Map 元素时，可以按照添加的顺序遍历
     - 添加了一对指针，指向前一个和后一个
     - 对于频繁的遍历，效率比 HashMap 高

  3. TreeMap

     - 按添加的 key—value 进行排序，实现排序遍历
     - 红黑树
     - 自然排序、定制排序

  4. HashTable

     - 作为 Map 的古老实现类
     - 线程安全，效率低
     - 不可以存储 null 的 key、value

  5. Properties：

     - 常用来处理配置文件
     - key / value 都是 String 类型

- key、value的理解

  - key

    - 不可重复、无序
    - 用 Set 存储：HashMap 用 HashSet，LinkedHashMap 用 LinkedHashSet
    - key 所在类要重写 equals ( )、hashcode ( )，TreeMap不用

  - value

    - 可以重复、无序
    - 用 Collection 存储
    - value 所在类要重写 equals ( )，找 value 要先找 key，所以 key 重写hashcode( )就行

  - Entry

    - 不可重复、无序
    - 用 Set 存储

    - 一个key—value / 键值对，构成一个 Entry 对象

  - key—value，一一对应

- 方法

  - 添加、删除、修改操作
    - Object put (Object key, Object value)：将指定key-value添加到(或修改)当前map对象中
    - void putAll (Map m)：将 m 中的所有 key-value 对存放到当前 map 中 
    - Object remove (Object key)：移除指定 key 的 key-value 对，并返回 value
    - void clear ( )：清空当前 map 中的所有数据

  - 元素查询的操作
    - Object get (Object key)：获取指定 key 对应的 value，不存在的key 返回null
    - boolean containsKey (Object key)：是否包含指定的 key
    - boolean containsValue (Object value)：是否包含指定的 value
    - int size ( )：返回 map 中 key-value 对的个数
    - boolean isEmpty ( )：判断当前 map 是否为空
    - boolean equals (Object obj)：判断当前map和参数对象obj是否相等

  - 元视图操作的方法 / 遍历
    - Set keySet ( )：返回所有 key 构成的 Set 集合
    - Collection values ( )：返回所有 value 构成的 Collection 集合
    - Set entrySet( )：返回所有 key-value 对构成的Set集合



- **HashMap**

  - 底层实现原理

    JDK 7 之前

    1. 内部存储结构
       - 数组 + 链表

    2. 当实例化一个 HashMap

       - new HashMap 时在底层创建一个长度为16的数组

       - 系统会创建一个长度为 Capacity=16 的 Entry[ ] table，这个长度在哈希表中被称为容量 / Capacity
       - 在这个数组中可以存放元素的位置称之为 “桶” (bucket)，每个bucket都有自己的索引，系统可以根据索引快速的查找bucket 中的元素
       -  每个bucket中存储一个元素，即一个Entry 对象，但每一个 Entry 对象可以带一个引用变量，用于指向下一个元素，因此，在一个桶中，就有可能生成一个 Entry链。而且新添加的元素作为链表的 head。 

    3. 添加元素的过程

       - 向 HashMap 中添加 entry1 (key，value)，

       - 首先计算entry1中 key (所在类的 hashCode( ) 返回) 的哈希值，将其高16位异或低十六位得到最终的哈希值，得到在底层Entry[ ] 中要存储的位置 i

         - 如果位置 i 上没有元素 / 没有冲突，则 entry1 添加成功

         - 如果位置 i 上已经存在一个或多个 entry / 发生冲突 ，通过循环的方法，依次比较 entry1 的key 和其他的 entry 的 key
           - 如果彼此 hash 值不同，则 entry1 添加成功
           - 如果 hash 值不同，调用 entey1 的key 的equals ( )
             - 如果返回值为 true，则使用 entry1 的 value，替换 equals ( ) 为 true 的 entry 的 value
             - 如果遍历一遍后，发现所有的 equals( ) 返回都为 false，则 entry1 添加成功。
             - entry1 指向原有的 entry元素，以链表的形式存储

    4. 扩容

       - 当HashMap中的元素越来越多的时候，hash冲突的几率也就越来越高，因为数组的长度是固定的
       - 所以为了提高查询的效率，就要对HashMap的数组进行扩容
       - 在HashMap数组扩容之后，最消耗性能的点就出现了：原数组中的数据必须重新计算在新数组中的位置，并放进去，这就是resize

    5. 什么时候扩容

       - 当HashMap中的元素个数 > 数组大小 (数组总大小 length，不是数组中个数size) * loadFactor 时 ， 就 会 进 行 数 组 扩 容 ， 
       - loadFactor 的默认值 (DEFAULT_LOAD_FACTOR)为 **0.75**，这是一个折中的取值
       - 默认情况下，数组大小(DEFAULT_INITIAL_CAPACITY)为 **16**
       - 当 HashMap 中元素个数超过**16 *  0.75 = 12**（这个值就是代码中的 threshold值，也叫做临界值) ，且要放的位置非空的时候，就把数组的大小扩展为 2 * 16 = 32，即扩大一倍，然后重新计算每个元素在数组中的位置
       - 这是一个非常消耗性能的操作，所以如果我们已经预知 HashMap 中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能

    JDK 8 之后

    1. 内部存储结构
       - 数组 + 链表 + 红黑树

    2. 当实例化一个 HashMap

       - 会初始化 initialCapacity=16 和 loadFactor=0.75
       - new HashMap 时没有在底层创建一个长度为16的数组
       - 在put第一对映射关系时，也就是首次调用 put( ) 时，系统才会创建一个长度为 initialCapacity 的 Nod类型的数组，这个长度在哈希表中被称为容量(Capacity)
       - 在这个数组中可以存放元素的位置我们称之为 “桶”(bucket)，每个 bucket 都有自己的索引，系统可以根据索引快速的查找bucket中的元素 
       - 每个 bucket 中存储一个元素，即一个Node对象
         - 每一个Node对象可以带一个引用变量 next，用于指向下一个元素
         - 所以，在一个桶中，就有可能生成一个Node链
       - 也可能是一个一个TreeNode对象
         - 每一个TreeNode对象可以有两个叶子结点 left 和 right
         - 所以，在一个桶中，就有可能生成一个TreeNode树。
         - 而新添加的元素作为链表的last，或树的叶子结点。

    3. HashMap 什么时候进行扩容和树形化

       - 当HashMap 中的元素个数超过数组大小 (数组总大小length,不是数组中个数size) * loadFactor 时 ， 就会进行数组扩容
       - loadFactor 的默认值 (DEFAULT_LOAD_FACTOR)为 **0.75**，这是一个折中的取值
       - 默认情况下，数组大小 (DEFAULT_INITIAL_CAPACITY)为 **16**
       - 当HashMap 中元素个数超过 **16*0.75=12**（这个值就是代码中的threshold值，也叫做临界值) 的时候，就把数组的大小扩展为 2 * 16 = 32，即扩大一倍，然后重新计算每个元素在数组中的位置
       - 这是一个非常消耗性能的操作，所以如果我们已经预知 HashMap 中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能
       - 当HashMap 的其中一个链的对象个数 >  8
         - 如果 capacity < 64，那么 HashMap 会先扩容解决
         - 如果 capacity > 64，那么这个链会变成树，结点类型由 Node 变成 TreeNode类型。
         - 当映射关系被移除后，下次 resize 方法时判断树的结点个数 < 6，会把树再转为链表。

    4. JDK1.8相较于之前的变化：

       - HashMap map = new HashMap( );  //默认情况下，先不创建长度为16的数组

       - 当首次调用 map.put( ) 时，再创建长度为16的数组
       - 数组为Node类型，在 JDK7 中为 Entry类型
       - 形成链表结构时，新添加的 key-value 对在链表的尾部（七上八下）
       - 当数组指定索引位置的链表长度 >8时，且map中的数组的长度 > 64时，此索引位置上的所有key-value对使用红黑树进行存储。

  - HashMap源码中的重要常量

    - DEFAULT_INITIAL_CAPACITY : HashMap 的默认容量，16

    - MAXIMUM_CAPACITY ： HashMap 的最大支持容量，2^30

    - DEFAULT_LOAD_FACTOR：HashMap 的默认加载因子

    - TREEIFY_THRESHOLD：Bucket 中链表长度大于该默认值，转化为红黑树

    - UNTREEIFY_THRESHOLD：Bucket 中红黑树存储的Node小于该默认值，转化为链表

    - MIN_TREEIFY_CAPACITY：桶中的Node被树化时最小的hash表容量

      当桶中Node的数量大到需要变红黑树时，若hash表容量小于MIN_TREEIFY_CAPACITY时，此时应执行 resize 扩容操作这个 MIN_TREEIFY_CAPACITY 的值至少是 TREEIFY_THRESHOLD 的4倍。

    - table：存储元素的数组，总是 2^n

    - entrySet：存储具体元素的集

    - size：HashMap 中存储的键值对的数量

    - modCount：HashMap 扩容和结构改变的次数。

    - threshold：扩容的临界值，= 容量 * 填充因子

    - loadFactor：填充因子 / 负载因子

  - 面试题

    1. 负载因子值的大小，对HashMap的影响
       - 负载因子的大小决定了 HashMap 的数据密度
       - 负载因子越大密度越大，发生碰撞的几率越高，数组中的链表越容易长，造成查询或插入时的比较次数增多，性能会下降
       - 负载因子越小，就越容易触发扩容，数据密度也越小，意味着发生碰撞的几率越小，数组中的链表也就越短，查询和插入时比较的次数也越小，性能会更高。但是会浪费一定的内容空间。而且经常扩容也会影响性能，建议初始化预设大一点的空间。
       - 按照其他语言的参考及研究经验，会考虑将负载因子设置为0.7~0.75，此时平均检索长度接近于常数
    2. 谈谈你对 HashMap 中put / get方法的认识？
    3. 谈谈 HashMap 的扩容机制？默认大小是多少？
    4. 什么是负载因子(或填充比)？
    5. 什么是吞吐临界值(或阈值、threshold)？



- **TreeMap**

  - TreeMap 存储 Key-Value 对时，需要根据 key-value 对进行排序

  - TreeMap 可以保证所有的 Key-Value 对处于有序状态。 

  - TreeSet 底层使用红黑树结构存储数据

  - TreeMap 的 Key 的排序：

    1. 自然排序
       - TreeMap 的所有的 Key 必须实现 Comparable 接口
       - 所有的 Key 应该是同一个类的对象，否则将会抛出 ClasssCastException

    2. 定制排序
       - 创建 TreeMap 时，传入一个 Comparator 对象，该对象负责对 TreeMap 中的所有 key 进行排序
       - 此时不需要 Map 的 Key 实现Comparable 接口

  - TreeMap判断两个key相等的标准：两个 key 通过 compareTo( ) 或者compare( ) 返回 0



- **HashTable**
  - Hashtable是个古老的 Map 实现类，JDK1.0就提供了
  - 线程安全
  -  Hashtable 实现原理和 HashMap 相同，功能相同
  - 底层都使用哈希表结构，查询速度快，很多情况下可以互用 
  - 不允许使用 null 作为 key 和 value
  - 不能保证其中 Key-Value 对的顺序
  - Hashtable判断两个 key 相等、两个 value 相等的标准，与 HashMap 一致



- **Properties**
  - Properties 类是 Hashtable 的子类，该对象用于处理属性文件
  - 由于属性文件里的 key、value 都是字符串类型，所以 Properties 里的 key 和 value 都是字符串类型
  - 存取数据时，建议使用 setProperty (String key,String value)、getProperty (String key)



#### Collections 工具类

- Collections 是一个操作 Set、List、Map 等集合的工具类
- Collections 中提供了一系列静态的方法对集合元素进行排序、查询和修改等操作，还提供了对集合对象设置不可变、对集合对象实现同步控制等方法
- 排序操作**：**（均为static方法）
  - reverse (List)：反转 List 中元素的顺序
  - shuffle (List)：对 List 集合元素进行随机排序
  - sort (List)：根据元素的自然顺序对指定 List 集合元素按升序排序
  - sort (List，Comparator)：根据指定的 Comparator 产生的顺序对 List 集合元素进行排序
  - swap (List，int， int)：将指定 list 集合中的 i 处元素和 j 处元素进行交换
- 查找、替换
  - Object max (Collection)：根据元素的自然顺序，返回给定集合中的最大元素
  - Object max (Collection，Comparator)：根据 Comparator 指定的顺序，返回给定集合中的最大元素
  - Object min (Collection)
  - Object min (Collection，Comparator)
  - int frequency (Collection，Object)：返回指定集合中指定元素的出现次数
  - void copy (List dest,List src)：将src中的内容复制到dest中 
  - boolean replaceAll (List list，Object oldVal，Object newVal)：使用新值替换List 对象的所有旧值