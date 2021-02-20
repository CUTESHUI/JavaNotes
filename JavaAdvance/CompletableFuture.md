## CompletableFuture

#### 简介

- Java8 带来的一个非常好用的用于异步编程的类



#### 实例化

- 通过构造函数来实例化

```java
CompletableFuture<String> cf = new CompletableFuture<String>();
```

- 这个实例此时还没有什么用，没有实际的任务，那就结束这个任务

```java
// 可以选择在当前线程结束，也可以在其他线程结束
cf.complete("coding...");
```

-  CompletableFuture 是一个 Future
  - 用  String result = cf.get( ) 就能阻塞获取值
  - join( ) 和 get( )是一样的，区别在于 join( ) 抛出的是 unchecked Exception
- 通过任务来实例化
  - runAsync( ) 接收的是 Runnable 的实例，意味着它没有返回值
  - supplyAsync( ) 对应的是有返回值的情况
  - 这两个方法的带 executor 的变种，表示让任务在指定的线程池中执行，不指定的话，通常任务是在 ForkJoinPool.commonPool( ) 线程池中执行

```java
CompletableFuture.runAsync(Runnable runnable);
CompletableFuture.runAsync(Runnable runnable, Executor executor);

CompletableFuture.supplyAsync(Supplier<U> supplier);
CompletableFuture.supplyAsync(Supplier<U> supplier, Executor executor)
```

