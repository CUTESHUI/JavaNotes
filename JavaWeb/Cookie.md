## Cookie

#### 简介

- Cookie 是由 W3C 组织提出，最早由 netscape 社区发展的⼀种机制
- ⽹⻚之间的交互是通过 HTTP 协议传输数据的，⽽ HTTP 协议是⽆状态的协议，⽆状态的协议是什么意思呢？⼀旦数据提交完后，浏览器和服务器的连接就会关闭，再次交互的时候需要重新建⽴新的连接。服务器⽆法确认⽤户的信息，于是，W3C 提出了：给每⼀个⽤户都发⼀个通⾏证，⽆论谁访问的时候都需要携带通⾏证，这样服务器就可以从通⾏证上确认⽤户的信息，通⾏证就是 Cookie



#### Cookie API

- Cookie类⽤于创建⼀个Cookie对象

- response 接⼝中定义了⼀个 addCookie( )，⽤于在其响应头中增加⼀个相应的 Set-Cookie 头字段

  request 接⼝中定义了⼀个 getCookies( )，⽤于获取客户端提交的 Cookie

- Cookie(String name, String value)

- setValue( )

- getValue( )

- setMaxAge( )

- getMaxAge( )

- setPath( )

- getPath( )

- setDomain( )

- getDomain⽅法( )

- getName⽅法( )



#### 例子

```java
// 设置response的编码
response.setContentType("text/html;charset=UTF-8");

// 创建Cookie对象，指定名称和值
Cookie Cookie = new Cookie("username", "zhongfucheng");

// 设置Cookie的时间
Cookie.setMaxAge(1000);

// 向浏览器给⼀个Cookie
response.addCookie(Cookie);
response.getWriter().write("我已经向浏览器发送了⼀个Cookie");
```



#### 细节

- 不可跨域名性

  - 浏览器判断⼀个⽹站是否能操作另⼀个⽹站的 Cookie 的依据是域名
  - ⼀般来说，当访问 baidu 的时候，浏览器只会把 baidu 颁发的 Cookie 带过去，⽽不会带上google 的 Cookie

- 保存中文

  - 中⽂属于 Unicode 字符，英⽂数据 ASCII 字符，中⽂占4个字符或者3个字符，英⽂占2个字符，Cookie 使⽤ Unicode 字符时需要对 Unicode字符进⾏编码

  ```java
  response.setContentType("text/html;charset=UTF-8");
  PrintWriter printWriter = response.getWriter();
  
  String name = "饼干";
  
  // 对Unicode字符进⾏编码
  Cookie Cookie = new Cookie("name", URLEncoder.encode(name, "UTF-8"));
  Cookie.setMaxAge(2000);
  
  response.addCookie(Cookie);
  printWriter.write("我颁发了⼀个Cookie，值保存的是中⽂数据");
  ```

  - Cookie 保存在硬盘的中⽂数据是经过编码的，那在取出 Cookie 的时候要对中⽂数据进⾏解码

  ```java
  Cookie[] cookies = request.getCookies();
  for (int i = 0; cookies != null && i < cookies.length; i++) {
    String name = cookies[i].getName();
    
    // 经过URLEncoding就要URLDecoding
    String value = URLDecoder.decode(cookies[i].getValue(), "UTF-8");
    
    printWriter.write(name + "------" + value);
  }
  ```

- 有效期

  - Cookie 的有效期是通过 setMaxAge( ) 来设置
    - 如果 MaxAge > 0，浏览器会把 Cookie 写到硬盘中，只要还在 MaxAge 秒之前，登陆⽹站时该Cookie就有效，不论关闭了浏览器还是电脑
    - 如果 MaxAge < 0，Cookie 是临时的，仅在本浏览器内有效，关闭浏览器 Cookie 就失效了，Cookie 不会写到硬盘中，Cookie 默认值是-1
    - 如果 MaxAge = 0，表示删除该Cookie，Cookie 机制没有提供删除 Cookie 对应的⽅法，把 MaxAge 设置为0等同于删除 Cookie

- 修改和删除

  - Cookie机制没有提供修改 Cookie 的⽅法
  - Cookie 存储的⽅式类似于 Map 集合
  - Cookie 的名称相同，通过 response 添加到浏览器中，以同样的名字设置，会覆盖原来的 Cookie

  ```java
  String name = "饼干2";
  
  // 对Unicode字符进⾏编码
  Cookie Cookie = new Cookie("name", URLEncoder.encode(name, "UTF-8"));
  
  // 删除时，⼀定不要忘记添加到浏览器中
  Cookie.setMaxAge(0);
  response.addCookie(Cookie);
  
  printWriter.write("我删除了该Cookie");
  ```

  - 删除，修改Cookie时，新建的 Cookie 除了value、maxAge 之外的所有属性都要与原 Cookie 相同，否则浏览器将视为不同的 Cookie，不予覆盖

- 域名

  - Cookie 的 domain 属性决定运⾏访问 Cookie 的域名，domain的值规定为 “.域名”

- 路径

  - Cookie 的 path 属性决定允许访问 Cookie 的路径
  - Cookie 发布出来，整个⽹⻚的资源都可以使⽤

- 安全属性

  - HTTP 协议不仅仅是⽆状态的，⽽且是不安全的
  - 如果不希望 Cookie 在⾮安全协议中传输，可以设置 Cookie 的 secure 属性为 true，浏览器只会在 HTTPS 和 SSL 等安全协议中传输
  - 设置 secure 属性不会将 Cookie 的内容加密，最好使⽤md5算法加密



#### 应用

- 显示⽤户上次访问的时间
  - 每次登陆的时候，取到 Cookie 保存的值，再更新下 Cookie 的值
  - 访问 Serlvet 有两种情况
    - 第⼀次访问
    - 已经访问过

```java
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-ddHH:mm:ss");

response.setContentType("text/html;charset=UTF-8");
PrintWriter printWriter = response.getWriter();

// 获取⽹⻚上所有的Cookie
Cookie[] cookies = request.getCookies();

// 判断Cookie的值是否为空，默认为空
String cookieValue = null;
for (int i = 0; cookies != null && i < cookies.length; i++) {
  // 获取到以time为名的Cookie
  if (cookies[i].getName().equals("time")) {
    printWriter.write("您上次登陆的时间是：");
    cookieValue = cookies[i].getValue();
    printWriter.write(cookieValue);
    
    // 更新
    cookies[i].setValue(simpleDateFormat.format(new Date()));
    response.addCookie(cookies[i]);
    break;
  }
}

// 如果Cookie的值是空的，那么就是第⼀次访问
if (cookieValue == null) {
  // 创建⼀个Cookie对象，⽇期为当前时间
  Cookie Cookie = new Cookie("time", simpleDateFormat.format(new Date()));
  // 设置Cookie的⽣命期
  Cookie.setMaxAge(20000);
  // response对象回送Cookie给浏览器
  response.addCookie(Cookie);
  printWriter.write("您是第⼀次登陆！");
}

```

- 显示上次浏览过的商品