## IOC

#### 控制反转、依赖注入

- 在Spring中，控制反转 / IOC（ Inversion of Control ）：由Spring IOC 容器来负责对象生命周期和对象之间的关系
- DI（依赖注入）其实是 IOC 的另外一种说法
- 控制的什么被反转了
  - 获得依赖对象的方式反转了
- 最好以依赖项注入（DI）的方式编写大多数应用程序代码，这种代码是 Spring IOC 容器中提供的，具有在创建时由容器提供的自己的依赖项，并且完全不需要了解容器本身
- 优点
  - 把依赖项的构造与使用分开，解耦
  - 可以更好的对依赖项进行配置，低侵入性
  - 类似 Spring 的配置文件中进行简单配置即可替换掉依赖注入项的实现。否则，你只能修改源代码



#### IOC 原理剖析

- 本质

  - 把应用程序的类和配置元数据组装起来，以便在 ApplicationContext 创建并初始化好之后，IOC 容器直接提供了一个已经配置好并且可执行的系统或应用

- IOC 容器系列的设计与实现：BeanFactory 和 ApplicationContext

- BeanFactory

  - BeanFactory 实现是 IOC 容器的基本形式，各种 ApplicationContext 的实现是 IOC 容器的高级表现形式

  - DefaultListableBeanFactory 作为一个默认的功能完整的 IOC 容器
  - Resource是Spring 用来封装 I / O 操作的类

- ApplicationContext

  - 一个高级形态意义的 IOC 容器
  - 支持不同的信息源：继承接口 Messagesource
  - 访问资源：继承接口 ResourceLoader
  - 支持应用事件
  - 在 ApplicationContext 中提供附加功能
  - 设计原理
    - 主要功能在基类 AbstractRefreshableApplicationContext 中实现了
    - ConfigurableApplicationContext 接口主要封装配置和生命周期相关方法，这些方法几乎只用在容器启动和关闭中

- IOC容器的初始化

  - IOC 容器的初始化过程，主要就体现在 AbstractApplicationContext 的 refresh 方法中

  ```java
  /**
   * refresh() 关键代码
   */
  public void refresh() throws BeansException, IllegalStateException {
    synchronized (this.startupShutdownMonitor) {
      // 1. 为刷新流程准备好context
      prepareRefresh();
  
      // 2. 加载bean definitions配置并解析注册到BeanFactory中
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
  
      // 3. 准备context中的BeanFactory
      prepareBeanFactory(beanFactory);
  
      try {
        // 4. 设置BeanFactory的后置处理器
        postProcessBeanFactory(beanFactory);
  
        // 5. 调用BeanFactory的后置处理器，这些处理器也是context中的beans
        invokeBeanFactoryPostProcessors(beanFactory);
  
        // 6. 注册Bean的后置处理器，这些处理器在 bean创建的过程中被调用
        registerBeanPostProcessors(beanFactory);
  
        // 7. 对上下文中的消息源进行初始化
        initMessageSource();
  
        // 8. 初始化context中的事件机制
        initApplicationEventMulticaster();
  
        // 9. 初始化其他特殊的Context中的特殊的bean
        onRefresh();
  
        // 10. 检查监听beans，并且向容器注册它们
        registerListeners();
  
        // 11. 实例化剩下的所有的(non-lazy-init) singletons.
        finishBeanFactoryInitialization(beanFactory);
  
        // 12. 最后一步：发布容器事件，结束Refresh过程
        finishRefresh();
      }
  
      catch (BeansException ex) {
        // 13. 为防止Bean资源占用，销毁已经在前面过程中已经生成的单间Bean
        destroyBeans();
        // 14. 重置'active'标志
        cancelRefresh(ex);
        // Propagate exception to caller.
        throw ex;
      }
    }
  }
  ```

- IOC容器的依赖注入

  - 在没有配置 lazy-init = false 的情况下，依赖注入的过程是用户第一次向 IOC 容器索要 Bean 的时候出发的
  - 具体的触发方法
    - BeanFactory.getBean(String name)
  - 最终是由 createBean 方法执行
    - createBean 方法生成需要的 Bean，并且对 Bean 初始化进行了处理（init-method 或者 Bean后置处理器等）
    - createBean 方法的 RootBeanDefinition 对象在 AbstractBeanFactory 的 doGetBean 方法里面生成
  - createBean
    - 



#### Lifecycle

- Lifecycle 接口定义 Spring 容器的生命周期，任何 Spring Bean 都可以实现该接口

- 当 ApplicationContext 接收 启动 和 停止 信号时，Spring 容器将在容器上下文中找出所有实现了 LifeCycle 及其子类接口的类，并一一调用它们实现类对应的方法

- Spring 是通过委托给生命周期处理器 LifecycleProcessor 来实现这一点，LifecycleProcessor 继承 Lifecycle 接口，新增刷新和关闭方法

- AbstractApplicationContext 中 Lifecycle 的处理逻辑

  - AbstractApplicationContext 关联了 LifecycleProcessor，在其 refresh 的最后 finishRefresh 方法里面调用到了 onRefresh 方法触发 Lifecycle 的 start 方法

  ```java
  getLifecycleProcessor().onRefresh();
  
  // 这里只会刷新SmartLifecycle的子类，具体处理逻辑如下:
  private void startBeans(boolean autoStartupOnly) {
  		Map<String, Lifecycle> lifecycleBeans = getLifecycleBeans();
  		Map<Integer, LifecycleGroup> phases = new HashMap<>();
  		lifecycleBeans.forEach((beanName, bean) -> {
  			if (!autoStartupOnly || (bean instanceof SmartLifecycle && ((SmartLifecycle) bean).isAutoStartup())) {
    		...
        }
      }
  }
  ```

  - onRefresh 方法传入了 autoStartupOnly = true，而 SmartLifecycle 的 isAutoStartup 方法默认返回 true，故默认情况下会处理 SmartLifecycle 的子类
  - 针对 Lifecycle 的子类，需要自己手动调用 ApplicationContext 的 start / stop 方法来触发具体 Bean 的生命周期实现



#### Bean 生命周期

- 启动容器后，会对 scope 为 singleton 并且非懒加载的 Bean 进行实例化（getBean方法触发实例化）
- 按照 BeanDefinition 定义信息配置信息，注入所有属性
- 如果实现了 BeanNameAware 接口，会调用接口的 setBeanName 方法，传入该 Bean 的唯一标识，以便获取该 Bean 的唯一标识
- 如果实现了 BeanFactoryAware 接口，会调用该接口的 setBeanFactory 方法，传入该 Bean 的 BeanFactory，以便获取 Bean 的 BeanFactory
- 如果实现了 ApplicationContextAware 接口，会调用该接口的 setApplicationContext 方法，传入该 Bean 的 ApplicationContext，以便获取该 Bean 的ApplicationContext；
- 如果实现了 BeanPostProcessor 接口，则会回调该接口的 postProcessBeforeInitialization 方法
- 如果实现了 InitializingBean 接口，会调用该接口的 afterPropertiesSet 方法
- 如果配置了 init-method 方法，则执行此方法
- 如果实现了 BeanPostProcessor 接口，则会回调该接口的 postProcessAfterInitialization 方法
- 这个时候，就可以正式使用该 Bean 了，对于 scope 为 singleton的bean，Spring 的 ioc 容器中会缓存一份该 Bean 的实例，而对于 prototype 的 Bean，每次调用都会 new 一个新的对象，该对象的生命周期交给调用方管理，不由 Spring 容器管理
- 容器关闭的时候，如果bean实现了 DisposableBean 接口，则会回调该接口的 destroy 方法
- 如果 Bean 配置 destroy-method 方法，会执行此方法



#### Bean 生命周期的实例

```java
/**
 * LifeCycleBean，使用以上生命周期中各个回调方法
 */
public class LifeCycleBean implements BeanNameAware, BeanFactoryAware, ApplicationContextAware, InitializingBean, DisposableBean {
    public void setBeanName(String name) {
        System.out.println("==== 1.BeanNameAware.setBeanName: " + name);
    }

    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        System.out.println("==== 2.BeanFactoryAware.setBeanFactory: " + beanFactory);
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        System.out.println("==== 3.ApplicationContextAware.setApplicationContext: " + applicationContext);
    }

    // ==== 4.BeanPostProcessor.postProcessBeforeInitialization
    
    public void initBean() {
        System.out.println("==== 5.CommonAnnotationBeanPostProcessor.postConstruct");
    }
  
    public void afterPropertiesSet() throws Exception {
        System.out.println("==== 6.InitializingBean.afterPropertiesSet");
    }

    public void initMethod() {
        System.out.println("==== 7.init-method");
    }

    // ==== 8.BeanPostProcessor.postProcessAfterInitialization
    
    public void destroyBean() {
        System.out.println("==== 9.CommonAnnotationBeanPostProcessor.preDestroy");
    }

    public void destroy() throws Exception {
        System.out.println("==== 10.DisposableBean.destroy");
    }

    public void destroyMethod() {
        System.out.println("==== 11.destroy-method");
    }

}


/**
 * 自定义BeanPostProcessor
 */
public class BeanPostService implements BeanPostProcessor {
  
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof LifeCycleBean) {
            System.out.println("==== 4.BeanPostProcessor.postProcessBeforeInitialization: " + beanName);
        }
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof LifeCycleBean) {
            System.out.println("==== 8.BeanPostProcessor.postProcessAfterInitialization: " + beanName);
        }
        return bean;
    }

}
```

- 结果

```
// 启动
==== 1.BeanNameAware.setBeanName: lifeCycleBean
==== 2.BeanFactoryAware.setBeanFactory: org.springframework.beans.factory.support.DefaultListableBeanFactory@76f4b65: ...
==== 3.ApplicationContextAware.setApplicationContext: 
==== 4.BeanPostProcessor.postProcessBeforeInitialization: lifeCycleBean
==== 5.CommonAnnotationBeanPostProcessor.postConstruct
==== 6.InitializingBean.afterPropertiesSet
==== 7.init-method
==== 8.BeanPostProcessor.postProcessAfterInitialization: lifeCycleBean

// 结束
==== 9.CommonAnnotationBeanPostProcessor.preDestroy
==== 10.DisposableBean.destroy
==== 11.destroy-method
```



