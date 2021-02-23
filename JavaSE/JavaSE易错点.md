## JavaSE易错点

#### 正确使用 equals 方法

- Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的对象来调用 equals

```java
String str = null;
if (str.equals("SnailClimb")) {
  ...
} else {
  ..
}  

// 这样就不会抛出异常
"SnailClimb".equals(str);
```

- 更推荐使用 `java.util.Objects#equals`(JDK7 引入的工具类)

```java
Objects.equals(null,"SnailClimb");
```

- 注意
  - 每种原始类型都有默认值，如 int 默认值为 0，boolean 的默认值为 false，null 是任何引用类型的默认值，不严格的说是所有 Object 类型的默认值
  - 可以使用 == 或者 != 操作来比较null值，但是不能使用其他算法或者逻辑操作，在Java中 null == null 将返回true
  - 不能使用一个值为 null 的引用类型变量来调用非静态方法，否则会抛出异常



#### 整型包装类值的比较

- 所有整型包装类对象值的比较必须使用 equals

```java
Integer x = 3;
Integer y = 3;
System.out.println(x == y); // true

Integer a = new Integer(3);
Integer b = new Integer(3);
System.out.println(a == b); //false
System.out.println(a.equals(b)); //true
```



#### BigDecimal

- 《阿里巴巴Java开发手册》中提到：浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类型不能用 equals 来判断
- 具体原理和浮点数的编码方式有关

```java
float a = 1.0f - 0.9f;
float b = 0.9f - 0.8f;
System.out.println(a); // 0.100000024
System.out.println(b); // 0.099999964
System.out.println(a == b); // false
```

- 精度丢失，解决这个问题一种很常用的方法是：使用使用 BigDecimal 来定义浮点数的值，再进行浮点数的运算操作

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");

BigDecimal x = a.subtract(b); 
BigDecimal y = b.subtract(c); 

System.out.println(x); // 0.1 
System.out.println(y); // 0.1 
System.out.println(Objects.equals(x, y)); // true
```

- BigDecimal 大小比较
  - a.compareTo(b) 
  -   a < `b`，返回 -1
  -   a > `b`，返回 1
  -   a = `b`，返回 0

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
System.out.println(a.compareTo(b)); // 1
```

- BigDecimal 保留几位小数
  - 通过 setScale( ) 设置保留几位小数以及保留规则
  - 保留规则有挺多种，不需要记，IDEA会提示

```java
BigDecimal m = new BigDecimal("1.255433");
BigDecimal n = m.setScale(3, BigDecimal.ROUND_HALF_DOWN);
System.out.println(n); // 1.255
```

- 注意
  - BigDecimal 主要用来操作（大）浮点数，BigInteger 主要用来操作大整数（超过 long 类型）
  - BigDecimal 的实现利用到了 BigInteger，不同的是 BigDecimal 加入了小数位的概念



####  Map 迭代取值

- 当循环中只需要 Map 的主键时，迭代 keySet( ) 
- 当需要主键和取值时，迭代 entrySet( ) 是更高效的做法，比先迭代 keySet( ) 后再去 get 取值性能更佳

```java
Map<String, String> map = ...;
for (Map.Entry<String, String> entry : map.entrySet()) {
    String key = entry.getKey();
    String value = entry.getValue();
    ...
}
```



#### 使用 Collection.isEmpty( ) 检测空

- 任何 Collection.isEmpty( ) 的时间复杂度都是 O(1) ，但是某些 Collection.size( ) 的时间复杂度可能是 O(n) 

```java
if (collection.isEmpty()) {
    ...
}
```

- 如果需要还需要检测 null ，可采用 CollectionUtils.isEmpty(collection)、CollectionUtils.isNotEmpty(collection)

```java
if (CollectionUtils.isEmpty(collection)) {
    ...
}
```



#### 集合

- 不要把集合对象传给自己
  - 由于某些方法要求参数在执行期间保持不变，因此将集合传递给自身可能会导致异常行为

```java
List<String> list = new ArrayList<>();
list.add("Hello");
list.add("World");
if (list.containsAll(list)) { // 无意义,总是返回true
    ...
}
list.removeAll(list); // 性能差, 直接使用clear()
```

- 集合初始化尽量指定大小
  - java 的集合有大小限制的，每次扩容的时间复杂度很有可能是 O(n) ，所以尽量指定可预知的集合大小，能减少集合的扩容次数

```java
int[] arr = new int[]{1, 2, 3};
List<Integer> list = new ArrayList<>(arr.length);
for (int i : arr) {
    list.add(i);
}
```

- List 的随机访问
  - 数组的随机访问效率更高
  - 如果想随机访问其中的数据，并不知道该数组内部实现是链表还是数组，可以判断它是否实现 RandomAccess 接口

```java
List<Integer> list = otherService.getList();
if (list instanceof RandomAccess) {
    // 内部数组实现，可以随机访问
    System.out.println(list.get(list.size() - 1));
} else {
    // 内部可能是链表实现，随机访问效率低
}
```

- 频繁调用 Collection.contains 方法请使用 Set
  - 在 java 集合类库中，List 的 contains 方法普遍时间复杂度是 O(n) 
  - 如果在代码中需要频繁调用 contains 方法查找数据，可以先将 list 转换成 HashSet 实现，将 O(n) 的时间复杂度降为 O(1) 

```java
ArrayList<Integer> list = otherService.getList();
Set<Integer> set = new HashSet(list);
for (int i = 0; i <= Integer.MAX_VALUE; i++) {
    // 时间复杂度O(1)
    set.contains(i);
}
```

