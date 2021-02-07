## Spring

#### 简介

- 替代更加重量级的的企业级 Java 技术
- 简化 Java 的开发
- 基于 POJO 轻量级和最⼩侵⼊式开发
- 通过依赖注⼊和⾯向接⼝实现松耦合
- 基于切⾯和惯例进⾏声明式编程
- 通过切⾯和模板减少样板式代码



#### 特征

- Spring 官网列出的 6 个特征
  - 核心技术 ：依赖注入(DI)，AOP，事件(events)，资源，i18n，验证，数据绑定，类型转换，SpEL
  - 测试 ：模拟对象，TestContext框架，Spring MVC 测试，WebTestClient
  - 数据访问 ：事务，DAO支持，JDBC，ORM，编组XML
  - Web支持 : Spring MVC和Spring WebFlux Web框架
  - 集成 ：远程处理，JMS，JCA，JMX，电子邮件，任务，调度，缓存
  - 语言 ：Kotlin，Groovy，动态语言



#### 侵⼊式、非侵入式

- 侵入式：
  - 对于 EJB、Struts2 等⼀些传统的框架，通常是要实现特定的接⼝，继承特定的类才能增强功能改变了 Java 类的结构
- ⾮侵⼊式：
  - 对于Hibernate、Spring等框架，对现有的类结构没有影响，就能够增强**JavaBean**的功能



#### 对象依赖

- 对象之间的依赖关系，其实就是给对象上的属性赋值，因为对象上有其他对象的变量，因此存在依赖

- 以前

  -  直接 new 对象
    - 直接 new 对象给 service 的 userDao 属性赋值

  ```java
  class UserService{
    UserDao userDao = new UserDao();
  }
  ```

  - 写 DaoFactory，⽤字符串来维护依赖关系
    - 问题：发现 service 层紧紧耦合了 dao 层
    - 解决：在 service 层只要通过字符串就能够创建对应的 dao 层的对象

  ```java
  // dao
  public class DaoFactory {
    private static final DaoFactory factory = new DaoFactory();
    private DaoFactory(){}
    public static DaoFactory getInstance(){ return factory; }
  
    public <T> T createDao(String className,Class<T> clazz) {
      try{
        T t = (T) Class.forName(className).newInstance();
        return t;
      } catch (Exception e) {
        throw new RuntimeException(e);
      }
    }
  }
  
  
  // service
  class UserService{
  
    private CategoryDao categoryDao = DaoFactory.getInstance()
      .createDao("zhongfucheng.dao.impl.CategoryDAOImpl", CategoryDao.class);
  
    private BookDao bookDao = DaoFactory.getInstance()
      .createDao("zhongfucheng.dao.impl.BookDaoImpl", BookDao.class);
  
    private UserDao userDao = DaoFactory.getInstance()
      .createDao("zhongfucheng.dao.impl.UserDaoImpl", UserDao.class);
  
    private OrderDao orderDao = DaoFactory.getInstance()
      .createDao("zhongfucheng.dao.impl.OrderDaoImpl", OrderDao.class);
  }
  ```

  - DaoFactory 读取配置⽂件
    - 问题：要修改 Dao 的实现类，还是得修改 service 层的源代码
    - 解决：在 DaoFactory 中读取关于 daoImpl 的配置⽂件，根据配置⽂件来创建对象，创建的是哪个 daoImpl 对 service 层就是透明的

  ```java
  // dao
  public class DaoFactory {
    private UserDao userdao = null;
  
    private DaoFactory() {
      try{
        InputStream in =DaoFactory.class.getClassLoader().getResourceAsStream("dao.properties");
        Properties prop = new Properties();
        prop.load(in);
        String daoClassName = prop.getProperty("userdao");
        userdao = (UserDao)Class.forName(daoClassName).newInstance();
      }catch (Exception e) {
        throw new RuntimeException(e);
      } 
    }
    private static final DaoFactory instance = new DaoFactory();
  
    public static DaoFactory getInstance(){ return instance; }
  
    public UserDao createUserDao(){ return userdao; } }
  
  
  // service
  class UserService{
    UserDao dao = DaoFactory.getInstance().createUserDao();
  }
  ```

- 现在

  - Spring 依赖注⼊
    - 通过构造函数
    - 通过 set ⽅法给属性注⼊值
    - p名称空间
    - ⾃动装配
    - 注解

  ```java
  // dao
  public class UserDao {
    public void save() {
      System.out.println("DB:保存⽤户");
    } 
  }
  
  
  // service
  public class UserService {
    
    private UserDao userDao;
    
    public void save() {
      userDao.save();
    } 
  }
  
  
  // action
  public class UserAction {
    
    private UserService userService;
    
    public String execute() {
      userService.save();
      return null; } 
  }
  ```



#### IOC

- 一种设计思想：将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理
- IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value），Map 中存放的是各种对象
- 优点
  - 这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来
  -  IoC 容器就像是一个工厂一样，当需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。
  - 在实际项目中一个 Service 类可能有几百甚至上千个类作为它的底层，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，如果利用 IoC 的话，只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度
- Spring 时代一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来
- 装配 Bean ⽅式
  - Spring4.x 开始有4种⽅式
    - XML 配置
    - 注解
    - JavaConfifig
    - 基于 Groovy DSL 配置(这种很少⻅)
- 依赖注⼊⽅式
  - 有3种⽅式
    - 属性注⼊ --> 通过 setter( ) 注⼊
    - 构造函数注⼊
    - ⼯⼚⽅法注⼊
- <bean>对象之间关系
  - 有三种关系：
    - 依赖-->挺少⽤的（使⽤depends-on就是依赖关系了-->前置依赖，依赖的 Bean 需要初始化之后，当前 Bean 才会初始化)
    - 继承-->可能会⽤到（指定 abstract 和 parent 来实现继承关系)
    - 引⽤-->最常⻅
- Bean的作⽤域
  - 单例 Singleton
  - 多例 prototype
  - 与 Web 应⽤环境相关的 Bean 作⽤域
    - reqeust
    - session
  - 使⽤ Web 应⽤环境相关的 Bean 作⽤域，需要⼿动配置代理
    - 因为默认的 Bean 是单例的，为了适配 Web 应⽤环境相关的 Bean 作⽤域，每个 request 都需要⼀个对象，因此需要返回⼀个代理对象出去



#### cglib代理

- 叫⼦类代理，从内存中构建出⼀个⼦类来扩展⽬标对象的功能
- ⼀个强⼤的⾼性能的代码⽣成包，它可以在运⾏期扩展 Java 类与实现 Java 接⼝，⼴泛的被许多 AOP 的框架使⽤，例如 Spring AOP 和 dynaop，为他们提供⽅法的 interception
- 代理的类不能为 fifinal，否则报错，在内存中构建⼦类来做扩展，当然不能为 fifinal，有 fifinal 就不能继承
- ⽬标对象的⽅法如果为 fifinal / static, 那么就不会被拦截，即不会执⾏⽬标对象额外的业务⽅法

```java
// 需要实现MethodInterceptor接⼝
public class ProxyFactory implements MethodInterceptor{
  // 维护⽬标对象
  private Object target;
  public ProxyFactory(Object target){ this.target = target; }
  // 给⽬标对象创建代理对象
  public Object getProxyInstance(){
    // 1. ⼯具类
    Enhancer en = new Enhancer();
    // 2. 设置⽗类
    en.setSuperclass(target.getClass());
    // 3. 设置回调函数
    en.setCallback(this);
    // 4. 创建⼦类(代理对象)
    return en.create();
  }
  @Override
  public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
    System.out.println("开始事务.....");
    // 执⾏⽬标对象的⽅法
    // Object returnValue = method.invoke(target, args);
    proxy.invokeSuper(object, args);
    System.out.println("提交事务.....");
    return returnValue; } 
}


public class App {
  public static void main(String[] args) {
    UserDao userDao = new UserDao();
    UserDao factory = (UserDao) new ProxyFactory(userDao).getProxyInstance();
    factory.save();
  }
}
```



#### AOP

- aspect object programming

- ⾯向切⾯的编程

  - 对很多功能都有的重复的代码抽取，再在运⾏的时候往业务⽅法上动态植⼊切⾯类代码
- 可以实现 “业务代码” 与 “关注点代码” 分离
  - 关注点代码，指重复执⾏的代码
- 分离好处
  
  - 关注点代码写⼀次即可
    - 开发者只需要关注核⼼业务
  - 运⾏时期，执⾏核⼼业务代码时候动态植⼊关注点代码，代理
  - 切⾯
  - 关注点形成的类，就叫切面、切⾯类
  - 切⼊点

    - 执⾏⽬标对象⽅法，动态植⼊切⾯代码
  - 可以通过切⼊点表达式，指定拦截哪些类的哪些⽅法； 给指定的类在运⾏的时候植⼊切⾯类代码
  
-   切⼊点表达式
    - 指定哪些类的哪些⽅法被拦截

```java
// 保存⼀个⽤户
public void add(User user) {
  Session session = null;
  Transaction trans = null;
  try {
    session = HibernateSessionFactoryUtils.getSession(); // 【关注点代码】
    trans = session.beginTransaction(); // 【关注点代码】
    session.save(user); // 核⼼业务代码
    trans.commit(); //…【关注点代码】
  } catch (Exception e) { 
    e.printStackTrace();
    if(trans != null){
      trans.rollback(); //..【关注点代码】
    }
  } finally{
    HibernateSessionFactoryUtils.closeSession(session); //..【关注点代码】
  }
}

// 切面、切面类
public class AOP {
  public void begin() {
    System.out.println("开始事务");
  }
  public void close() {
    System.out.println("关闭事务");
  }
}
```

- 注解API

  - @Aspect 
    - 指定⼀个类为切⾯类
  - @Pointcut("execution(\* cn.itcast.e_aop_anno..(..))") 
    - 指定切⼊点表达式
  - @Before("pointCut_( )") 
    - 前置通知
    - ⽬标⽅法之前执⾏
  - @After("pointCut_( )") 
    - 后置通知
    - ⽬标⽅法之后执⾏，始终执⾏
  - @AfterReturning("pointCut_( )") 
    - 返回后通知
    - 执⾏⽅法结束前执⾏，异常不执⾏
  - @AfterThrowing("pointCut_( )") 
    - 异常通知
    - 出现异常时候执⾏
  - @Around("pointCut_( )") 
    - 环绕通知
    - 环绕⽬标⽅法执⾏

```java
// 前置通知 : 在执⾏⽬标⽅法之前执⾏
@Before("pointCut_()")
public void begin(){
  System.out.println("开始事务/异常");
}

// 后置/最终通知：在执⾏⽬标⽅法之后执⾏ 【⽆论是否出现异常最终都会执⾏】
@After("pointCut_()")
public void after(){
  System.out.println("提交事务/关闭");
}

// 返回后通知： 在调⽤⽬标⽅法结束后执⾏ 【出现异常不执⾏】
@AfterReturning("pointCut_()")
public void afterReturning() {
  System.out.println("afterReturning()");
}

// 异常通知： 当⽬标⽅法执⾏异常时候执⾏此关注点代码
@AfterThrowing("pointCut_()")
public void afterThrowing(){
  System.out.println("afterThrowing()");
}

// 环绕通知：环绕⽬标⽅式执⾏
@Around("pointCut_()")
public void around(ProceedingJoinPoint pjp) throws Throwable{
  System.out.println("环绕前....");
  pjp.proceed(); // 执⾏⽬标⽅法
  System.out.println("环绕后....");
}
```

- 注解表达式优化

```java
// 优化前
@Before("execution(* aa.*.*(..))")
public void begin() {
  System.out.println("开始事务");
}

@After("execution(* aa.*.*(..))")
public void close() {
  System.out.println("关闭事务");
}

// 优化后
@Component
@Aspect //指定为切⾯类
public class AOP {
  // 指定切⼊点表达式，拦截哪个类的哪些⽅法
  @Pointcut("execution(* aa.*.*(..))")
  public void pt() {
  }
  
  @Before("pt()")
  public void begin() {
    System.out.println("开始事务");
  }
  
  @After("pt()")
  public void close() {
    System.out.println("关闭事务");
  }
}
```

- 使⽤引介 / 引⼊功能为 Bean 引⼊新⽅法
  - 当引⼊接⼝⽅法被调⽤时，代理对象会把此调⽤委托给实现了新接⼝的某个其他对象
  - 简单来说，⼀个 Bean 的实现被拆分到多个类中

```java
public interface Waiter {
  // 向客⼈打招呼
  void greetTo(String clientName);
  // 服务
  void serveTo(String clientName);
}

public class NaiveWaiter implements Waiter {
  public void greetTo(String clientName) {
    System.out.println("NaiveWaiter:greet to " + clientName + "...");
  }
  @NeedTest
  public void serveTo(String clientName) {
    System.out.println("NaiveWaiter:serving " + clientName + "...");
  }
}

public interface Seller {
  // 卖东⻄
  int sell(String goods, String clientName);
}

public class SmartSeller implements Seller {
  // 卖东⻄
  public int sell(String goods,String clientName) {
    System.out.println("SmartSeller: sell "+goods +" to "+clientName+"...");
    return 100; } 
}

// 现在想让这个服务员可以充当售货员的⻆⾊，可以卖东⻄
@Aspect
public class EnableSellerAspect {
  // value 指定服务员具体的实现
  // defaultImpl 指定售货员具体的实现
  @DeclareParents(value = "com.smart.NaiveWaiter", defaultImpl = SmartSeller.class) 
  public Seller seller; // 要实现的⽬标接⼝
}

public class Test {
  public static void main(String[] args) {
    ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("com/shui/aspectj/basic/beans.xml");
    Waiter waiter = (Waiter) ctx.getBean("waiter");
    // 调⽤服务员原有的⽅法
    waiter.greetTo("Java3y");
    waiter.serveTo("Java3y");
    // 通过引介/引⼊切⾯已经将waiter服务员实现了Seller接⼝，所以可以强制转换
    Seller seller = (Seller) waiter;
    seller.sell("⽔军", "Java3y");
  }
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
  
  <aop:aspectj-autoproxy/>
  <bean id="waiter" class="com.smart.NaiveWaiter"/>
  <bean class="com.smart.aspectj.basic.EnableSellerAspect"/>
  
</beans>
```

- 总结
  - AOP 底层是动态代理，动态代理分成了 JDK 动态代理和 CGLib 动态代理，如果被代理对象没有接⼝，那默认使⽤的是CGLIB代理，也可以直接配置使⽤ CBLib 代理
  - 如果是单例的话，最好使⽤ CGLib 代理，CGLib 代理对象运⾏速度要⽐ JDK的 代理对象快
  - AOP 只能对⽅法进⾏拦截，它的层⾯上是⽅法级别的，经典的⽅式、注解⽅式、XML配置⽅式使⽤ Spring AOP 的原理都是⼀样的，只不过形式变了⽽已
  - 注解的⽅式使⽤ Spring AOP 了解⼏个切点表达式，⼏个增强 / 通知的注解就可以
  - 引介 / 引⼊切⾯也算是⼀个⽐较亮点的地⽅，可以⽤代理的⽅式为某个对象实现接⼝，从⽽能够使⽤接口下的⽅法，这种⽅式是⾮侵⼊式的
  - 要增强的⽅法还可以接收与被代理⽅法⼀样的参数、绑定被代理⽅法的返回值这些功能



#### Spring事务

- 分类

  - 编程式事务，通过代码的⽅式来实现事务
  - 声明式事务，通过配置的⽅式来实现事务

- 在 Spring 实现相对简单⼀些，⽽声明式事务因为封装了⼤量的东⻄，所以声明式事务实现要难得多

- 编程式事务接口

  - TransactionDefifinition

    - 定义了 Spring 兼容的事务属性，⽐如事务隔离级别、事务传播、事务超时、是否只读状态

  - TransactionStatus
  - 代表了事务的具体运⾏状态，获取事务运⾏状态的信息，也可以通过该接⼝间 接回滚事务等操作
  -  PlatformTransactionManager
  - 事务管理器接⼝(定义了⼀组⾏为，具体实现交由不同的持久化框架来完成---类⽐JDBC
  
- 声明式事务接口

  - TransactionStatus
  - PlatformTransactionManager
  - TransactionProxyFactoryBean
    - ⽣成代理对象
  - TransactionInterceptor
    - 实现对象的拦截
  - TransactionAttrubute
    - 事务配置的数据