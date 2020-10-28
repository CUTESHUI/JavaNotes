## Spring入门

#### Spring诞生

1. 创建Spring的目的就是用来**替代更加重量级的的企业级Java技术**

2. 简化Java的开发

- 基于POJO轻量级和**最小侵入式开发**
- 通过 依赖注入 和面向接口实现**松耦合**
- **基于切面**和惯例进行声明式编程
- 通过切面和模板**减少样板式代码** 



#### 侵入式、非侵入式概念

1. 侵入式

对于EJB、Struts2等一些传统的框架，**通常是要实现特定的接口，继承特定的类才能增强功能**，改变了java类的结构。



2. 非侵入式

对于Hibernate、Spring等框架，**对现有的类结构没有影响**，就能够增强JavaBean的功能。



#### 松耦合

我们在写程序的时候，都是面向接口编程，通过DaoFactroy等方法来实现松耦合。

注 ：DAO层和Service层通过DaoFactory来实现松耦合。

如果Serivce层直接new DaoBook()，那么DAO和Service就紧耦合( Service层依赖紧紧依赖于Dao )。



Spring给我们更加合适的方法( **IOC容器 控制反转** )来实现松耦合，并且更加灵活、功能更加强大。



#### 切面编程

切面编程也就是**AOP编程**，**动态代理 **就是一种切面编程了。

AOP编程可以简单理解成：在执行某些代码前，执行另外的代码。

Spring也为我们提供更好地方式来实现面向切面编程！



#### Spring模块

Spring可以分为6大模块：

- **Spring Core** 

   spring的核心功能：IOC容器，解决对象创建及依赖关系。

- **Spring Web**  

  Spring对web模块的支持

- 1. 可以与struts整合，让 struts 的 action 创建交给 spring

- 2. spring mvc模式

- **Spring DAO**  

  Spring 对JDBC操作的支持

- **Spring ORM**  

  spring对orm的支持

  可以与 hibernate 整合

- 2. 可以使用spring 的对 hibernate 操作的封装

- **Spring AOP**  

  切面编程

- **SpringEE**  

  spring 对 javaEE 其他模块的支持



