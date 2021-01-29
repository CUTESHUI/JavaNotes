## Spring

#### 简介

- 替代更加重量级的的企业级 Java 技术
- 简化 Java 的开发
- 基于 POJO 轻量级和最⼩侵⼊式开发
- 通过依赖注⼊和⾯向接⼝实现松耦合
- 基于切⾯和惯例进⾏声明式编程
- 通过切⾯和模板减少样板式代码



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




#### IOC控制反转

- 



#### AOP

- 

