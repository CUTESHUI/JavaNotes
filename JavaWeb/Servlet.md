## Servlet





#### 生命周期

- 加载Servlet
  - 当 Tomcat 第一次访问 Servlet 的时候，Tomcat 会负责创建 Servlet 的实例
- 初始化
  - 当 Servlet 被实例化后，Tomcat会调用 init() 方法初始化这个对象
- 处理服务
  - 当浏览器访问 Servlet 的时候，Servlet 会调用 service() 方法处理请求
- 销毁
  - 当 Tomcat 关闭时或者检测到 Servlet 要从 Tomcat 删除的时候会自动调用 destroy() 方法，让该实例释放掉所占的资源。一个 Servlet 如果长时间不被使用的话，也会被 Tomcat 自动销毁
- 卸载
  - 当 Servlet 调用完 destroy() 方法后，等待垃圾回收。如果有需要再次使用这个 Servlet，会重新调用 init() 方法进行初始化操作

- 总结
  - 只要访问 Servlet，service() 就会被调用
  - init() 只有第一次访问 Servlet 的时候才会被调用
  - destroy() 只有在 Tomcat 关闭的时候才会被调用



#### HttpServlet

- 实现 Servlet 接口，要实现5个方法
- HttpServlet 已经实现了 Servlet 接口的所有方法，编写 Servlet 时，只需要 extend HttpServlet，重写你需要的方法即可，并且它在原有 Servlet 接口上添加了一些与 HTTP 协议处理方法，它比 Servlet 接口的功能更为强大

- 一般开发的时候，都是重写 doGet() 和 doPost() 



#### Servlet的细节

- 一个已经注册的 Servlet 可以被多次映射
- Servlet 映射的 URL 可以使用通配符
  - 通配符有两种格式
    - .扩展名
    - 正斜杠（/）开头并以“/*”结尾

- Servlet 是单例的
  - 浏览器多次对 Servlet 的请求，一般情况下，服务器只创建一个 Servlet 对象，也就是说，Servlet 对象一旦创建了，就会驻留在内存中，为后续的请求做服务，直到服务器关闭
- 每次访问请求对象和响应对象都是新的
- 线程安全问题
  - 当多个用户访问 Servlet 的时候，服务器会为每个用户创建一个线程
  - 当多个用户并发访问 Servlet 共享资源的时候就会出现线程安全问题
  - 原则
    - 如果一个变量需要多个用户共享，则应当在访问该变量的时候，加同步机制 synchronized (对象){}
    - 如果一个变量不需要共享，则直接在 doGet() 或者 doPost()定义.这样不会存在线程安全问题



#### 在web访问任何资源都是在访问Servlet

- web.xml 配置了一个缺省 Servlet，凡是在 web.xml 文件中找不到匹配的元素的 URL，它们的访问请求都将交给缺省 Servlet 处理
- 缺省 Servlet 用于处理所有其他 Servlet 都不处理的访问请求

- 总结
  - 无论在 web 中访问什么资源，都是在访问 Servlet
  - 没有手工配置缺省 Servlet 的时候，你访问静态图片，静态网页，缺省 Servlet 会在 web 站点中寻找该图片或网页，如果有就返回给浏览器，没有就报404错误



#### ServletConfig对象

- 通过此对象可以读取 web.xml 中配置的初始化参数，侵略性小



#### ServletContext对象

- 当 Tomcat 启动的时候，就会创建一个 ServletContext 对象
- 它代表着当前 web 站点
- 作用
  - ServletContext 既然代表着当前 web 站点，那么所有 Servlet 都共享着一个 ServletContext对 象，所以 Servlet 之间可以通过 ServletContext 实现通讯
  - ServletConfig 获取的是配置的是单个 Servlet 的参数信息， ServletContext 可以获取的是配置整个 web 站点的参数信息
  - 利用 ServletContext 读取 web 站点的资源文件
  - 实现 Servlet 的转发

- Servlet 之间实现通讯
  - ServletContext 对象被称之为 域对象：可以简单理解成一个容器（类似于Map集合）
  - 实现 Servlet 之间通讯就要用到 ServletContext 的 setAttribute(String name,Object obj) 方法
    -  第一个参数是关键字
    - 第二个参数是你要存储的对象

- 获取 web 站点配置的信息
  - 想要让所有的 Servlet 都能够获取到连接数据库的信息，不可能在 web.xml 文件中每个 Servlet 中都配置一下，这样代码非常啰嗦冗余



#### request、response对象

- Tomcat 收到客户端的 http 请求，会针对每一次请求，分别创建一个代表请求的 request 对象、和代表响应的 response 对象
- request 对象代表 http 请求，获取浏览器提交过来的数据，找 request 对象
- response 对象代表 http 响应，向浏览器输出数据，找 response 对象



#### HttpServletResponse

- 



#### HttpServletRequest

- HttpServletRequest 对象代表客户端的请求，当客户端通过 HTTP 协议访问服务器时，HTTP 请求头中的所有信息都封装在这个对象中，开发人员通过这个对象的方法，可以获得客户这些信息

- 常用方法

  - 获取客户机（浏览器）信息

    - getRequestURL：返回客户端发出请求时的完整URL
    - getRequestURI：返回请求行中的资源名部分
    - getQueryString ：返回请求行中的参数部分
    - getPathInfo：返回请求URL中的额外路径信息。额外路径信息是请求URL中的位于Servlet的路径之后和查询参数之前的内容，它以“/”开头
    - getRemoteAddr：返回发出请求的客户机的IP地址
    - getRemoteHost：返回发出请求的客户机的完整主机名
    - getRemotePort：返回客户机所使用的网络端口号
    - getLocalAddr：返回WEB服务器的IP地址
    - getLocalName：返回WEB服务器的主机名

  - 获取客户机请求头

    - getHeader
    - getHeaders
    - getHeaderNames

  - 获取客户机请求参数（客户端提交的数据

    - getParameter

      getParameterValues（String name）

      getParameterNames

      getParameterMap

- 应用

  - 防盗链
    - 获取 Referer 这个消息头，判断 Referer 是不是从我的首页来的，如果不是从我的首页来的，跳转回我的首页

- 转发和重定向的区别

  - 实际发生位置不同，地址栏不同
    - 转发：发生在服务器
    - 转发是由服务器进行跳转的，在转发的时候，浏览器的地址栏是没有发生变化的。实现转发只是一次的 http 请求，一次转发中 request 和 response 对象都是同一个。这也解释为什么可以使用 request 作为域对象进行 Servlet 之间的通讯
    - 重定向：发生在浏览器
    - 重定向是由浏览器进行跳转的，在重定向的时候，浏览器的地址会发生变化的。实现重定向的原理是由 response 的状态码和 Location 头组合而实现的。这是由浏览器进行的页面跳转实现重定向会发出两个http请求，request域对象是无效的，因为它不是同一个request对象

  - 能够去往的 URL 的范围不一样
    - 转发：服务器跳转，只能去往当前 web 应用的资源
    - 重定向：浏览器跳转，可以去往任何的资源
  - 传递的数据类型不同
    - 转发：的 request 对象可以传递各种类型的数据，包括对象
    - 重定向：只能传递字符串
  - 跳转的时间不同
    - 转发：执行到跳转语句时就会立刻跳转
    - 重定向：整个页面执行完之后才执行跳转

