## SpringSecurity

#### 简介

- Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方案
- 关于安全方面的两个主要区域是“认证”和“授权”（或者访问控制）
- 一般来说，Web 应用的安全性包括用户认证（Authentication）和用户授（Authorization）两个部分，这两点也是 Spring Security 重要核心功能



#### 用户认证授权

- 用户认证

  - 验证某个用户是否为系统中的合法主体，也就是说用户能否访问该系统
  - 一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程
  - 简单来说，系统认为用户是否能登录
- 用户授权
  - 验证某个用户是否有权限执行某个操作
  - 在一个系统中，不同用户  所具有的权限是不同的，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的权限
    - 比如对一个文件来说，有的用户只能进行读取，而有的用户可以进行修改
  - 简单来说，系统判断用户是否有权限去做某些事情



#### 特点

- 和 Spring 无缝整合
- 全面的权限控制
- 专门为 Web 开发而设计
  - 旧版本不能脱离 Web 环境使用
  - 新版本对整个框架进行了分层抽取，分成了核心模块和 Web 模块，单独 引入核心模块就可以脱离 Web 环境
- 重量级



#### 权限管理相关概念

-  主体
  - principal
  - 使用系统的用户或设备或从其他系统远程登录的用户等等
  - 谁使用系统谁就是主体
-  认证
  - authentication
  - 权限管理系统确认一个主体的身份，允许主体进入系统
  - “主体”证明自己是谁，登录操作
- 授权
  - authorization
  - 将操作系统的“权力”“授予”“主体”，这样主体就具备了操作系统中特定功能的能力
  - 简单来说，授权就是给用户分配权限



#### 原理

- SpringSecurity 采用的是责任链的设计模式，它有一条很长的过滤器链，所以 SpringSecurity 本质是一个过滤器链
- 从启动中可以获取到过滤器链

```
org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter
org.springframework.security.web.context.SecurityContextPersistenceFilter 
org.springframework.security.web.header.HeaderWriterFilter
org.springframework.security.web.csrf.CsrfFilter
org.springframework.security.web.authentication.logout.LogoutFilter 
org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter 
org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter 
org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter
org.springframework.security.web.savedrequest.RequestCacheAwareFilter
org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter
org.springframework.security.web.authentication.AnonymousAuthenticationFilter 
org.springframework.security.web.session.SessionManagementFilter 
org.springframework.security.web.access.ExceptionTranslationFilter 
org.springframework.security.web.access.intercept.FilterSecurityInterceptor
```

- SpringSecurity 过滤器
  - WebAsyncManagerIntegrationFilter
    - 将 Security 上下文与 Spring Web 中用于处理异步请求映射的 WebAsyncManager 进行集成。
  - SecurityContextPersistenceFilter
    - 在每次请求处理之前将该请求相关的安全上下文信息加载到 SecurityContextHolder 中
    - 在该次请求处理完成之后，将SecurityContextHolder 中关于这次请求的信息存储到一个“仓储”中
    - 将SecurityContextHolder 中的信息清除，例如在 Session 中维护一个用户的安全信息就是这个过滤器处理的
  - HeaderWriterFilter
    - 用于将头信息加入响应中
  -  CsrfFilter
    - 用于处理跨站请求伪造
  - LogoutFilter
    - 用于处理退出登录
  - UsernamePasswordAuthenticationFilter
    - 用于处理基于表单的登录请求，从表单中获取用户名和密码。默认情况下处理来自 /login 的请求
    - 从表单中获取用户名和密码时，默认使用的表单 name 值为 username 和 password
    - 这两个值可以通过设置这个过滤器的 usernameParameter 和 passwordParameter 两个参数的值进行修改
  - DefaultLoginPageGeneratingFilter
    - 如果没有配置登录页面，那系统初始化时就会配置这个过滤器，并且用于在需要进行登录时生成一个登录表单页面
  - BasicAuthenticationFilter
    - 检测和处理 http basic 认证
  - RequestCacheAwareFilter
    - 用来处理请求的缓存
  - SecurityContextHolderAwareRequestFilter
    - 主要是包装请求对象 request。
  - AnonymousAuthenticationFilter
    - 检测 SecurityContextHolder 中是否存在Authentication 对象，如果不存在为其提供一个匿名 Authentication
  - SessionManagementFilter
    - 管理 session 的过滤器
  - ExceptionTranslationFilter
    - 处理 AccessDeniedException 和 AuthenticationException 异常。
  - FilterSecurityInterceptor
    - 可以看做过滤器链的出口。
  - RememberMeAuthenticationFilter
    - 当用户没有登录而直接访问资源时，从 cookie 里找出用户的信息
    - 如果 Spring Security 能够识别出用户提供的 remember me cookie，用户将不必填写用户名和密码，直接登录进入系统，该过滤器默认不开启



#### 基本流程

- 采取过滤链实现认证与授权，只有当前过滤器通过，才能进入下一个过滤器

- 认证过滤器可以使用 Spring Security 提供的认证过滤器，也可以自定义过滤器，例如：短信验证

- 认证过滤器要在 configure(HttpSecurity http)方法中配置，没有配置不生效

- 重点过滤器

  - UsernamePasswordAuthenticationFilter 过滤器
    - 该过滤器会拦截前端提交的 POST 方式的登录表单请求，并进行身份认证

  - ExceptionTranslationFilter 过滤器
    - 该过滤器不需要配置，对于前端提交的请求会直接放行，捕获后续抛出的异常并进行处理
    - 例如：权限访问限制

  - FilterSecurityInterceptor 过滤器
    - 该过滤器是过滤器链的最后一个过滤器，根据资源权限配置来判断当前请求是否有权限访问对应的资源
    - 如果访问受限会抛出相关异常，并由 ExceptionTranslationFilter 过滤器进行捕获和处理



#### 认证流程

- 认证流程是在 UsernamePasswordAuthenticationFilter 过滤器中处理



#### 权限访问流程



#### UserDetailsService

- 当什么也没有配置，账号和密码是由 Spring Security 定义生成的
- 实际项目中账号和密码都是从数据库中查询出来的
- 要通过自定义逻辑控制认证逻辑，只需要实现 UserDetailsService 接口
- API

```java
// 获取登录用户所有权限
Collection<? extends GrantedAuthority> getAuthorities();

// 获取密码
String getPassword();

// 获取用户名
String getUsername();

// 判断账户是否过期
boolean isAccountNonExpired();

// 判断账户是否被锁定
boolean isAccountNonLocked();

// 凭证{密码}是否过期
boolean isCredentialsNonExpired();

// 当前用户是否可用
boolean isEnabled();
```



#### PasswordEncoder

- API

```java
// 把参数按照特定的解析规则进行解析
String encode(CharSequence rawPassword);

// 验证从存储中获取的编码密码与编码后提交的原始密码是否匹配
// 如果密码匹配，则返回 true
// 如果不匹配，则返回 false
// 第一个参数表示需要被解析的密码, 第二个参数表示存储的密码
boolean matches(CharSequence rawPassword, String encodedPassword);

// 表示如果解析的密码能够再次进行解析且达到更安全的结果则返回 true，否则返回false。默认返回 false。
default boolean upgradeEncoding(String encodedPassword) {
  return false; 
}
```

- BCryptPasswordEncoder 
  - SpringSecurity 官方推荐的密码解析器，平时多使用这个解析器
  - 对 bcrypt 强散列方法的具体实现
  - 基于 Hash 算法实现的单向加密
  - 可以通过 strength 控制加密强度，默认 10



#### CSRF

- 跨站请求伪造、Cross-site request forgery
- 也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的 Web 应用程序上执行非本意的操作的攻击方法
- 跟跨网站脚本（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任
- 跨站请求攻击，简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去运行
- 利用了 web 中用户身份验证的一个漏洞：简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的
- 从 SpringSecurity 4.0 开始，默认情况下会启用 CSRF 保护，以防止 CSRF 攻击应用程序，Spring Security CSRF 会针对 PATCH、POST、PUT、DELETE 方法进行防护
-  SpringSecurity 实现 CSRF 的原理
  - 生成 csrfToken 保存到 HttpSession 或者 Cookie 中
  - 请求到来时，从请求中提取 csrfToken，和保存的 csrfToken 做比较，进而判断当前请求是否合法
  - 主要通过 CsrfFilter 过滤器来完成



#### 微服务认证与授权实现思路

- 认证授权过程分析
  - 如果基于 Session
    - SpringSecurity 会对 cookie 里的 sessionid 进行解析
    - 找到服务器存储的 session 信息
    - 然后判断当前用户是否符合请求的要求
  - 如果基于 token
    - 解析出 token
    - 然后将当前请求加入到 SpringSecurity 管理的权限信息中去
  - 如果系统的模块众多，每个模块都需要进行授权与认证，那么选择基于 token 的形式进行授权与认证
    - 用户根据用户名密码认证成功，然后获取当前用户角色的一系列权限值，并以用户名为 key，权限列表为 value 的形式存入 redis 缓存中
    - 根据用户名相关信息生成 token 返回，浏览器将 token 记录到 cookie 中
    - 每次调用 api 接口都默认将 token 携带到 header 请求头中
    - Spring-security 解析 header 头获取 token 信息，解析 token 获取当前用户名
    - 根据用户名就可以从 redis 中获取权限列表，这样 Spring-security 就能够判断当前请求是否有权限访问

