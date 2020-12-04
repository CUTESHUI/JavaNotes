## Java对象历险记

#### 优先在EDEN区分配对象

- Eden 区空间不足，触发 Minor GC，标记可回收对象，然后 Eden 区存活对象拷贝到往 Survivor-From 区，清空 Eden 区
- 再次触发 Minor GC，扫描 Eden 区和 from 区，把存活的对象复制到 To 区，清空 Eden 区和 from区
- 如果在 Minor GC 复制存活对象到 Survivor 区时，发现 Survivor 区内存不够，则提前把对象放入老年代



#### 大对象直接进入老年代

- 如果发现需要大量连续内存空间的 Java 对象，如很长的字符串或者数组，则直接把对象放入老年代
- 可通过`-XX:PretenureSizeThreshold`参数设置大对象的最小大小，该参数只对 Serial 和 ParNew 两款收集器有效



#### 长期存活对象进入老年代

- 固定对象年龄判断
  - 默认的，存活对象在 Survivor的From 和 To 区来回交换15次后如果对象最终还是存活，就放入老年代
  - 可以通过`-XX:MaxTenuringThreshold`参数来设置对象的年龄
- 动态对象年龄判断
  - 如果发现 Survivor 中有相同年龄的对象空间总和大于 Survivor 空间的一半，那么年龄大于或者等于该年龄的对象直接晋升到老年代



#### 空间分配担保

- 如果 Survivor 区存活了很多对象，空间不够了，都需要晋升到老年代，那么就需要老年代进行分配担保，将 Survivor 无法容纳的对象直接进入老年代

- 发生 Minor GC 前，JVM 先检查老年代最大可用连续空间是否大于新生代所有对象的总空间
  - 大于：空间足够，直接 Minor GC；
  - 小于：进行一次 Full GC
- 空间担保
  - 如果老年代连续空间 > 新生代对象总大小或者历次晋升的平均大小，就会进行 MinorGC，否则进行 FullGC