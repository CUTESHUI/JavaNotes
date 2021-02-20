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



#### 任务之间的顺序执行



#### 异常处理

- 方法

```java
public CompletableFuture<T> exceptionally(Function<Throwable, ? extends T> fn);
public <U> CompletionStage<U> handle(BiFunction<? super T, Throwable, ? extends U> fn);
```

- exceptionally

```java
CompletableFuture.supplyAsync(() -> "resultA")
    .thenApply(resultA -> resultA + " resultB")
    .thenApply(resultB -> resultB + " resultC")
    .thenApply(resultC -> resultC + " resultD");

// 假设A有异常
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {throw new RuntimeException();})
        .exceptionally(ex -> "errorResultA")
        .thenApply(resultA -> resultA + " resultB")
        .thenApply(resultB -> resultB + " resultC")
        .thenApply(resultC -> resultC + " resultD");

System.out.println(future.join());
```

```
errorResultA resultB resultC resultD
```

- handle

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "resultA")
  .thenApply(resultA -> resultA + " resultB")
  // 任务 C 抛出异常
  .thenApply(resultB -> {throw new RuntimeException();})
  // 处理任务 C 的返回值或异常
  .handle(new BiFunction<Object, Throwable, Object>() {
    @Override
    public Object apply(Object re, Throwable throwable) {
      if (throwable != null) {
        return "errorResultC";
      }
      return re;
    }
  })
  .thenApply(resultC -> resultC + " resultD");

System.out.println(future.join());
```



#### 取两个任务的结果

- 怎么让任务 A 和任务 B 同时执行，然后取它们的结果进行后续操作
- 这里强调的是任务之间的并行工作，没有先后执行顺序
- Future

```java
ExecutorService executorService = Executors.newCachedThreadPool();

Future<String> futureA = executorService.submit(() -> "resultA");
Future<String> futureB = executorService.submit(() -> "resultB");

String resultA = futureA.get();
String resultB = futureB.get();
```

- CompletableFuture

```java
CompletableFuture<String> cfA = CompletableFuture.supplyAsync(() -> "resultA");
CompletableFuture<String> cfB = CompletableFuture.supplyAsync(() -> "resultB");

cfA.thenAcceptBoth(cfB, (resultA, resultB) -> {});
cfA.thenCombine(cfB, (resultA, resultB) -> "result A + B");
cfA.runAfterBoth(cfB, () -> {});
```

