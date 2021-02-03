## Session

#### 简介

- 一种记录浏览器状态的机制
- 保存在服务器中
- 用户使用浏览器访问服务器的时候，服务器把用户的信息以某种的形式记录在服务器
- 如果说 Cookie 是检查⽤户身上的”通⾏证“来确认⽤户的身份，那么 Session 就是通过检查服务器上的”客户明细表“来确认⽤户的身份的
- Session 相当于在服务器中建⽴了⼀份“客户明细表”



#### 为什么使用Session

- 比 Cookie 使用方便
- Session 可以存储对象，Cookie 只能存储字符串



#### Session API

- getCreationTime( )：获取 Session 被创建时间
- getId( )：获取 Session 的 id
- getLastAccessedTime( )：返回 Session 最后活跃的时间
- getServletContext( )：获取 ServletContext 对象
- setMaxInactiveInterval(int var1)：设置 Session 超时时间
- getMaxInactiveInterval( )：获取 Session 超时时间
- getAttribute(String var1)：获取 Session 属性
- Enumeration getAttributeNames( )：获取 Session 所有的属性名
- setAttribute(String var1, Object var2)：设置 Session 属性
- removeAttribute(String var1)：移除 Session 属性
- invalidate( )：销毁该 Session
- isNew( )：该 Session 是否为新的



#### Session作为域对象

- Session 有着 request 和 ServletContext 类似的方法，所以 Session也是一个域对象
- 作为一种记录浏览器状态的机制，只要 Session 对象没有被销毁，Servlet 之间就可以通过 Session 对象实现通讯
- 当要存进用户级别的数据就用 Session，也就是，只要浏览器不关闭，希望数据还在，就使用 Session 来保存

```java
// 得到Session对象
HttpSession httpSession = request.getSession();

// 设置Session属性
httpSession.setAttribute("name","看完博客就要点赞！！");
```



#### Session生命周期和有效期

- Session 在用户第一次访问服务器 Servlet，jsp 等**动态**资源就**会**被自动创建，Session 对象保存在内存里

- 如果访问 html，image 等**静态**资源 Session **不会**被创建。

- Session 生成后，只要用户继续访问，服务器就会更新 Session 的最后访问时间，无论是否对 Session 进行读写，服务器都会认为 Session 活跃了一次

- 由于会有越来越多的用户访问服务器，因此 Session 也会越来越多，为了防止内存溢出，服务器会把长时间没有活跃的 Session 从内存中删除，这个时间也就是 Session 的超时时间。

- Session 的超时时间默认是**30分钟**，有三种方式可以对 Session 的超时时间进行修改

  - 在 tomcat/conf/web.xml 文件中设置，时间值为20分钟，所有的 web 应用都有效

  ```xml
  <session-config>
    <session-timeout>20</session-timeout>
  </session-config>
  ```

  - 在单个的 web.xml 文件中设置，对单个 web 应用有效，如果有冲突，以自己的 web 应用为准

  ```xml
  <session-config>
    <session-timeout>20</session-timeout>
  </session-config>
  ```

  - 通过 setMaxInactiveInterval( ) 设置

  ```java
  // 设置Session最长超时时间为20秒，这里的单位是秒
  httpSession.setMaxInactiveInterval(60);
  ```



#### Session实现原理

- HTTP 协议是无状态的，Session 不能依据 HTTP 连接来判断是否为同一个用户
- 服务器向用户浏览器发送了一个名为JESSIONID 的 Cookie，它的值是 Session 的 id 值，所以 Session 依据 Cookie 来识别是否是同一个用户
- 总结
  - Session 可以识别不同的用户，依靠 Cookie
  - 当访问 Servlet 的时候，服务器就会创建一个 Session 对象，执行程序代码，并自动颁发个 Cookie 给用户浏览器
  - 该 Cookie 是服务器自动颁发给浏览器的
  - 该 Cookie 的 maxAge 值默认是-1，也就是说仅当前浏览器使用，不把该 Cookie 存在硬盘中



#### Session、Cookie区别

- 存储方式

  - Cookie 只能存储字符串，如果要存储非ASCII字符串还要编码
  - Session 可以存储任何类型的数据，可以把 Session 看成一个容器

- 隐私安全
  - Cookie 存储在浏览器中，对客户端是可见的，信息容易泄露出去，如果使用 Cookie，最好对 Cookie 加密
    - Session 存储在服务器上，对客户端是透明的，不存在敏感信息泄露问题
- 对服务器的负担
  - Session 是保存在服务器的 ( 内存中 )，每个用户都会产生一个 Session，如果是并发访问的用户非常多，是不能使用 Session 的，Session 会消耗大量的内存
    - Cookie 是保存在浏览器的，不占用服务器的资源。像baidu、Sina这样的大型网站，一般都是使用 Cookie 来进行会话跟踪
- 有效期
  - Cookie 保存在浏览器中，只要设置 maxAge 属性是比较大的正整数，即使关闭浏览器，Cookie 还是存在
    - Session 保存在服务器中，设置 maxInactiveInterval 属性值来确定 Session 有效期，并且 Session 依赖于名为 JSESSIONID 的 Cookie，该 Cookie 默认的 maxAge 属性为 -1。如果关闭了浏览器，该 Session 虽然没有从服务器中消亡，但失效了
- 浏览器的支持
  - 如果浏览器禁用了 Cookie，那么 Cookie 是无用的
    - 如果浏览器禁用了 Cookie，Session 可以通过 URL 地址重写来进行会话跟踪
- 跨域名
  - Cookie 可以设置 domain 属性来实现跨域名
    - Session 只在当前的域名内有效，不可跨越域名