## SpringMVC

#### 运行流程

- 用户发送请求至前端控制器 DispatcherServlet
- DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器
- 处理器映射器根据请求 url 找到具体的处理器，生成处理器对象及处理器拦截器 (如果有则生成) 一并返回给 DispatcherServlet
-  DispatcherServlet 通过 HandlerAdapter 处理器适配器调用处理器
- 执行处理器 (Controller，也叫后端控制器)
- Controller 执行完成返回 ModelAndView
- HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet
- DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析
- ViewReslover 解析后返回具体 View
- DispatcherServlet 对 View 进行渲染视图（即将模型数据填充至视图中）
- DispatcherServlet 响应用户



#### 九大组件

```java
protected void initStrategies(ApplicationContext context) {
	
  // 用于处理上传请求。处理方法是将普通的request包装成MultipartHttpServletRequest，后者可以直接调用getFile方法获取File.
	initMultipartResolver(context);
  
	// SpringMVC主要有两个地方用到了Locale：一是ViewResolver视图解析的时候；二是用到国际化资源或者主题的时候。
	initLocaleResolver(context); 
  
	// 用于解析主题。SpringMVC中一个主题对应一个properties文件，里面存放着跟当前主题相关的所有资源、
	// 如图片、css样式等。SpringMVC的主题也支持国际化， 
	initThemeResolver(context);
  
	// 用来查找Handler的。
	initHandlerMappings(context);
  
	// 从名字上看，它就是一个适配器。Servlet需要的处理方法的结构却是固定的，都是以request和response为参数的方法。
	// 如何让固定的Servlet处理方法调用灵活的Handler来进行处理呢？这就是HandlerAdapter要做的事情
	initHandlerAdapters(context);
  
	// 其它组件都是用来干活的。在干活的过程中难免会出现问题，出问题后怎么办呢？
	// 这就需要有一个专门的角色对异常情况进行处理，在SpringMVC中就是HandlerExceptionResolver。
	initHandlerExceptionResolvers(context);
  
	// 有的Handler处理完后并没有设置View也没有设置ViewName，这时就需要从request获取ViewName了，
	// 如何从request中获取ViewName就是RequestToViewNameTranslator要做的事情了。
	initRequestToViewNameTranslator(context);
  
	// ViewResolver用来将String类型的视图名和Locale解析为View类型的视图。
	// View是用来渲染页面的，也就是将程序返回的参数填入模板里，生成html（也可能是其它类型）文件。
	initViewResolvers(context);
  
	// 用来管理FlashMap的，FlashMap主要用在redirect重定向中传递参数。
	initFlashMapManager(context); 

}
```



#### **自定义 SpringMVC 配置**

- 与自定义 SpringMVC 相关的类和注解主要有如下四个：
  - WebMvcConfigurerAdapter
  - WebMvcConfigurer
  - WebMvcConfigurationSupport
  - @EnableWebMvc

- WebMvcConfigurerAdapter

  - 这是在 Spring Boot 1.x 中自定义 SpringMVC 时继承的一个抽象类

  - 实现了 WebMvcConfigurer 接口

  - 空方法

    ```java
    /**
     * An implementation of {@link WebMvcConfigurer} with empty methods allowing
     * subclasses to override only the methods they're interested in.
     * @deprecated as of 5.0 {@link WebMvcConfigurer} has default methods (made
     * possible by a Java 8 baseline) and can be implemented directly without the
     * need for this adapter
     */
    public abstract class WebMvcConfigurerAdapter implements WebMvcConfigurer {
        //各种 SpringMVC 配置的方法
    }
    ```

  - 从 Spring5 开始，由于使用 Java8，而 Java8 中的接口允许存在 default 方法，因此官方建议直接实现 WebMvcConfigurer 接口，而不是继承 WebMvcConfigurerAdapter 

- WebMvcConfigurer

  -  是 Spring Boot 2.x 中实现自定义配置的方案
  -  是一个接口
  - 接口中的方法和 WebMvcConfigurerAdapter 中定义的空方法其实一样，所以用法上，基本上没有差别
  - 从 Spring Boot 1.x 切换到 Spring Boot 2.x ，只需要把继承类改成实现接口即可

- WebMvcConfigurationSupport

  - 方法和前面两个类中的方法基本一样

  - 继承 WebMvcConfigurationSupport 这种操作一般只在 Java 配置的 SSM 项目中使用，Spring Boot 中基本上不会这么写

    - 为什么?
    - Spring Boot 中，SpringMVC 相关的自动化配置是在 WebMvcAutoConfiguration 配置类中实现的，是有生效条件的

    ```java
    @Configuration
    @ConditionalOnWebApplication(type = Type.SERVLET)
    @ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
    @ConditionalOnMissingBean(WebMvcConfigurationSupport.class) // 条件
    @AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
    @AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
    		ValidationAutoConfiguration.class })
    public class WebMvcAutoConfiguration {
    }
    ```

    - 条件：当不存在 WebMvcConfigurationSupport 的实例时，这个自动化配置才会生生效

- @EnableWebMvc

  - 作用：启用 WebMvcConfigurationSupport
  - 在 Spring Boot 中，不建议使用 @EnableWebMvc ，因为它一样会导致 Spring Boot 中的 SpringMVC 自动化配置失效

- 总结
  - Spring Boot 1.x 中，自定义 SpringMVC 配置可以通过继承 WebMvcConfigurerAdapter 来实现
  - Spring Boot 2.x 中，自定义 SpringMVC 配置可以通过实现 WebMvcConfigurer 接口来完成
  - 如果在 Spring Boot 中使用继承 WebMvcConfigurationSupport 来实现自定义 SpringMVC 配置，或者在 Spring Boot 中使用了 @EnableWebMvc 注解，都会导致 Spring Boot 中默认的 SpringMVC 自动化配置失效。
  - 在纯 Java 配置的 SSM 环境中，如果要自定义 SpringMVC 配置，有两种办法，第一种就是直接继承自 WebMvcConfigurationSupport 来完成 SpringMVC 配置，还有一种方案就是实现 WebMvcConfigurer 接口来完成自定义 SpringMVC 配置，如果使用第二种方式，则需要给 SpringMVC 的配置类上额外添加 @EnableWebMvc 注解，表示启用 WebMvcConfigurationSupport，这样配置才会生效
  - 在纯 Java 配置的 SSM 中，如果你需要自定义 SpringMVC 配置，离不开 WebMvcConfigurationSupport ，所以在这种情况下建议通过继承 WebMvcConfigurationSupport 来实现自动化配置



