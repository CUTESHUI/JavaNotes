## Spring注解

#### @SpringBootApplication

- 这个注解是 Spring Boot 项目的基石，创建 SpringBoot 项目之后会默认在主类加上
- @SpringBootApplication 看作是 @Configuration、@EnableAutoConfiguration、@ComponentScan 注解的集合
  - @EnableAutoConfiguration：启用 SpringBoot 的自动配置机制
  - @ComponentScan： 扫描被 @Component (@Service、@Controller) 注解的 bean，注解默认会扫描该类所在的包下所有的类
  - @Configuration：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

#### @Autowired

- 自动导入对象到类中，被注入进的类同样要被 Spring 容器管理




#### @Component、@Repository、@Service、

- 一般使用  @Autowired 注解让 Spring 容器帮我们自动装配 bean
- 想把类标识成可用于 @Autowired 注解自动装配的 bean 的类，可以采用以下注解实现：
  - @Component：通用的注解，可标注任意类为  Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用 @Component 注解标注
  - @Repository：对应持久层即 Dao 层，主要用于数据库相关操作
  - @Service：对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层
  - @Controller：对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面



#### @RestController

- @RestController 注解是 @Controller 和 @ResponseBody的合集，表示这是个控制器 bean，并且是将函数的返回值直接填入 HTTP 响应体中，是 REST 风格的控制器
- 单独使用  @Controller 不加 @ResponseBody 的话一般使用在要返回一个视图的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况
- @Controller + @ResponseBody 返回 JSON 或 XML 形式数据



#### @Scope

- 声明 Spring Bean 的作用域
- 四种常见的 Spring Bean 的作用域
  - singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的
  - prototype : 每次请求都会创建一个新的 bean 实例
  - request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效
  - session : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效



#### @Configuration

- 一般用来声明配置类，可以使用 @Component 注解替代，不过使用 @Configuration 注解声明配置类更加语义化

---

### 处理常见的 HTTP 请求类型

- 5 种常见的请求类型
  - **GET** ：请求从服务器获取特定资源。举个例子：`GET /users`（获取所有学生）
  - **POST** ：在服务器上创建一个新的资源。举个例子：`POST /users`（创建学生）
  - **PUT** ：更新服务器上的资源（客户端提供更新后的整个资源）。举个例子：`PUT /users/12`（更新编号为 12 的学生）
  - **DELETE** ：从服务器删除特定的资源。举个例子：`DELETE /users/12`（删除编号为 12 的学生）
  - **PATCH** ：更新服务器上的资源（客户端提供更改的属性，可以看做作是部分更新），使用的比较少，这里就不举例子了
- GET 请求
  - @GetMapping("users") = @RequestMapping(value="/users",method=RequestMethod.GET)
- POST 请求
  - @PostMapping("users") = @RequestMapping(value="/users",method=RequestMethod.POST)
- PUT 请求
  - @PutMapping("/users/{userId}") = @RequestMapping(value="/users/{userId}",method=RequestMethod.PUT)
- DELETE 请求
  - @DeleteMapping("/users/{userId}") = @RequestMapping(value="/users/{userId}",method=RequestMethod.DELETE)
- PATCH 请求
  - 一般实际项目中，我们都是 PUT 不够用了之后才用 PATCH 请求去更新数据

---

### 前后端传值

#### @PathVariable 

- @PathVariable 用于获取路径参数，路径上{} 就用



####  @RequestParam

- @RequestParam 用于获取查询参数，都可以用
- 常用于GetMapping
- 请求头
- String Integer 等



#### @RequestBody

- 用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且Content-Type 为 application/json 格式的数据
- 接收到数据之后会自动将数据绑定到 Java 对象上去
- 系统会使用 HttpMessageConverter 或者自定义的 HttpMessageConverter 将请求的 body 中的 json 字符串转换为 java 对象

- 常用于PostMapping

- 请求体
- 集合、DTO、Entity



- 一个请求方法只可以有一个@RequestBody，但是可以有多个@RequestParam 和 @PathVariable

---

### 读取配置信息

#### @value

- 使用  @Value("${property}") 读取比较简单的配置信息



#### ConfigurationProperties

- 通过 @ConfigurationProperties读取配置信息并与 bean 绑定



```yaml
wuhan2020: 2020年初武汉爆发了新型冠状病毒，疫情严重，但是，我相信一切都会过去！武汉加油！中国加油！

library:
  location: 湖北武汉加油中国加油
  books:
    - name: 天才基本法
      description: 二十二岁的林朝夕在父亲确诊阿尔茨海默病这天，得知自己暗恋多年的校园男神裴之即将出国深造的消息——对方考取的学校，恰是父亲当年为她放弃的那所。
    - name: 时间的秩序
      description: 为什么我们记得过去，而非未来？时间“流逝”意味着什么？是我们存在于时间之内，还是时间存在于我们之中？卡洛·罗韦利用诗意的文字，邀请我们思考这一亘古难题——时间的本质。
    - name: 了不起的我
      description: 如何养成一个新习惯？如何让心智变得更成熟？如何拥有高质量的关系？ 如何走出人生的艰难时刻？
```

```java
@Value("${wuhan2020}")
String wuhan2020;
```

```java
@Component
@ConfigurationProperties(prefix = "library")
class LibraryProperties {
    @NotEmpty
    private String location;
    private List<Book> books;

    @Setter
    @Getter
    @ToString
    static class Book {
        String name;
        String description;
    }
  	......
}
```

---

### 参数校验

- 使在前端对数据进行校验的情况下，我们还是要对传入后端的数据再进行一遍校验，避免用户绕过浏览器直接通过一些 HTTP 工具直接向后端请求一些违法数据
-  所有的注解，推荐使用 JSR 注解，即 javax.validation.constraints，而不是 org.hibernate.validator.constraints

- 一些常用的字段验证的注解
  - @NotEmpty：被注释的字符串的不能为 null 也不能为空
  - @NotBlank：被注释的字符串非 null，并且必须包含一个非空白字符
  - @Null：被注释的元素必须为 null
  - @NotNull：被注释的元素必须不为 null
  - @AssertTrue：被注释的元素必须为 true
  - @AssertFalse：被注释的元素必须为 false
  - @Pattern(regex=,flag=)：被注释的元素必须符合指定的正则表达式
  - @Email：被注释的元素必须是 Email 格式。
  - @Min(value)：被注释的元素必须是一个数字，其值必须大于等于指定的最小值
  - @Max(value)：被注释的元素必须是一个数字，其值必须小于等于指定的最大值
  - @DecimalMin(value)：被注释的元素必须是一个数字，其值必须大于等于指定的最小值
  - @DecimalMax(value)：被注释的元素必须是一个数字，其值必须小于等于指定的最大值
  - @Size(max=, min=)：被注释的元素的大小必须在指定的范围内
  - @Digits (integer, fraction)：被注释的元素必须是一个数字，其值必须在可接受的范围内
  - @Past：被注释的元素必须是一个过去的日期
  - @Future：被注释的元素必须是一个将来的日期
  - ...

---

### 全局处理 Controller 层异常

#### @ControllerAdvice

- 注解定义全局异常处理类



#### @ExceptionHandler

- 注解声明异常处理方法

---

### 事务

#### @Transactional

- 一般用在可以作用在`类`或者`方法`上
  - 作用于类
    - 当把 @Transactional 注解放在类上时，表示所有该类的public 方法都配置相同的事务属性信息
  - 作用于方法
    - 当类配置了 @Transactional，方法也配置了@Transactional，方法的事务会覆盖类的事务配置信息

- Exception 分为运行时异常 RuntimeException 和非运行时异常
- 在 @Transactional 注解中如果不配置 rollbackFor 属性，那么事物只会在遇到 RuntimeException 的时候才会回滚，加上 rollbackFor=Exception.class，可以让事物在遇到非运行时异常时也回滚

---

###  json 数据处理

#### @JsonIgnoreProperties

- 作用在类上用于过滤掉特定字段不返回或者不解析



#### @JsonIgnore

- 一般用于类的属性上



#### @JsonFormat

- 一般用来格式化 json 数据