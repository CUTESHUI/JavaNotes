## Java最大栈深度有多大

#### 测试线程栈大小对栈深度的影响

```java
public class StackTest {

    private int count = 0;

    public void recursiveCalls(String a){
        count++;
        System.out.println("stack depth: " + count);
        recursiveCalls(a);
    }

    public void test(){
        try {
            recursiveCalls("a");
        } catch (Exception e) {
            System.out.println(e);
        }
    }

    public static void main(String[] args) {
        new StackTest().test();
    }

}
```

- 设置启动参数

```java
-Xms256m -Xmx256m -Xmn128m -Xss256k
```

- 结果

```java
stack depth: 1556
Exception in thread "main" java.lang.StackOverflowError
```

- 调整-Xss线程栈大小为 512k，输出内容

```java
stack depth: 3249
Exception in thread "main" java.lang.StackOverflowError
```



#### 结论

- 随着线程栈的大小越大，能够支持越多的方法调用，也即是能够存储更多的栈帧
- 局部变量表内容越多，那么栈帧就越大，栈深度就越小
- 评审写代码的时候，发现堆栈溢出，可以查看下对应类的本地变量表，是不是太多了，可不可以优化代码，或者加大线程栈的大小，以增加栈的深度