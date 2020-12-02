## AOP

#### 相关点

- 通知、Advice
  - 通知描述了切面要完成的工作以及何时执行
  - 比如我们的日志切面需要记录每个接口调用时长，就需要在接口调用前后分别记录当前时间，再取差值
  - 分类
    - 前置通知（Before）：在目标方法调用前调用通知功能
    - 后置通知（After）：在目标方法调用之后调用通知功能，不关心方法的返回结果
    - 返回通知（AfterReturning）：在目标方法成功执行之后调用通知功能
    - 异常通知（AfterThrowing）：在目标方法抛出异常后调用通知功能
    - 环绕通知（Around）：通知包裹了目标方法，在目标方法调用之前和之后执行自定义的行为

- 连接点、JoinPoint
  - 通知功能被应用的时机
  - 比如：接口方法被调用的时候就是日志切面的连接点

- 切点、Pointcut
  - 定义了通知功能被应用的范围
  - 比如：日志切面的应用范围就是所有接口，即所有controller层的接口方法。

- 切面、Aspect
  - 通知和切点的结合，定义了何时、何地应用通知功能

- 引入、Introduction
  - 在无需修改现有类的情况下，向现有的类添加新方法或属性

- 织入、Weaving
  - 把切面应用到目标对象并创建新的代理对象的过程



#### 相关注解

- @Aspect：用于定义切面
- @Before：通知方法会在目标方法调用之前执行
- @After：通知方法会在目标方法返回或抛出异常后执行
- @AfterReturning：通知方法会在目标方法返回后执行
- @AfterThrowing：通知方法会在目标方法抛出异常后执行
- @Around：通知方法会将目标方法封装起来
- @Pointcut：定义切点表达式



#### 切点表达式

- 指定了通知被应用的范围，表达式格式

```java
execution(方法修饰符 返回类型 方法所属的包.类名.方法名称(方法参数)
```

- 例子

```java
// com.shui.controller 包中所有类的 public 方法都应用切面里的通知
execution(public * com.shui.controller.*.*(..))
  
// com.shui.service 包及其子包下所有类中的所有方法都应用切面里的通知
execution(* com.shui.service.*.*(..))
  
// com.shui.service.PmsBrandService 类中的所有方法都应用切面里的通知
execution(* com.shui.service.PmsBrandService.*(..))
```

