## Ajax

#### 简介

- **AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）**
- AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术
- Google Suggest 使用 Ajax 创造出动态性极强的 web 界面：当您在谷歌的搜索框输入关键字时，JavaScript 会把这些字符发送到服务器，然后服务器会返回一个搜索建议的列表

- 传统的网页( 即不用Ajax技术的网页)，想要更新内容或者提交一个表单，都需要重新加载整个网页
- 使用Ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以**实现异步局部更新**
- 使用Ajax，用户可以创建接近本地桌面应用的直接、高可用、更丰富、更动态的Web用户界面



#### Ajax可以做

- 注册时，输入用户名自动检测用户是否已经存在
- 登陆时，提示用户名密码错误
- 删除数据行时，将行ID发送到后台，后台在数据库中删除，数据库删除成功后，在页面DOM中将数据行也删除
- ....等等



#### Ajax 应用和传统 Web应用有什么不同

- 传统的 web 前端与后端的交互

  - 浏览器直接访问 Tomcat 的 Servlet来获取数据
  - Servlet **通过转发**把数据发送给浏览器

- 使用 Ajax 之后

  - 浏览器是先把请求发送到 XMLHttpRequest 异步对象之中

  - 异步对象将请求进行封装，以HTTP协议的方式发送给服务器
  - XMLHttpRequest 异步对象会不停监听服务器状态的变化，得到服务器返回的数据，就**以流的方式**把数据返回给浏览器

  - 因为不是转发的方式，所以是无刷新就能够获取服务器端的数据



#### Ajax的实现流程

- 创建 XMLHttpRequest 对象，也就是创建一个异步调用对象.
- 创建一个新的HTTP请求，并指定该HTTP请求的方法、URL及验证信息
- 设置响应 HTTP请求状态变化的函数
- 发送HTTP请求
- 获取异步调用返回的数据
- 使用 JavaScript 和 DOM实现局部刷新



#### Ajax的优点和缺点？

- 优点
  - 最大的一点是页面无刷新，用户的体验非常好。
  - 使用异步方式与服务器通信，具有更加迅速的响应能力
  - 可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，Ajax的原则是“按需取数据”，可以最大程度的减少冗余请求，和响应对服务器造成的负担
  - 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序
- 缺点
  - Ajax 不支持浏览器 back按钮。
  - 安全问题，Ajax 暴露了与服务器交互的细节
  - 对搜索引擎的支持比较弱
  - 破坏了程序的异常机制
  - 不容易调试



#### 异步加载 JS

- 异步加载的方案： 动态插入 script 标签
- 通过 Ajax 去获取 js 代码，然后通过 eval 执行
- script 标签上添加 defer 或者 async 属性
- 创建并插入 iframe，让它异步执行 js



#### 如何解决跨域问题

- 什么是跨域
  - 协议、域名、端口都相同才同域，否则跨域
- 出于安全考虑，服务器不允许 Ajax 跨域获取数据，但是可以跨域获取文件内容
  - 所以基于这一点，可以动态创建 script标签，使用标签的 src 属性访问 js文件的形式获取 js脚本，并且这个js脚本中的内容是函数调用，该函数调用的参数是服务器返回的数据，为了获取这里的参数数据，需要事先在页面中定义回调函数，在回调函数中处理服务器返回的数据，**JSONP**
  - 在后端上配置可跨域，**CORS方式**
  - 前端 Ajax请求的是本地接口，本地接口接收到请求后向实际的接口请求数据，然后再将信息返回给前端，**代理方式**



#### Ajax 解决浏览器缓存问题？

- 在 Ajax 发送请求前加上 anyAjaxObj.setRequestHeader("If-Modified-Since","0")、anyAjaxObj.setRequestHeader("Cache-Control","no-cache")。
- 在 URL 后面加上一个随机数： "fresh=" + Math.random();。
- 在 URL 后面加上时间戳："nowtime=" + new Date().getTime();。
- 如果是使用jQuery，直接 $.ajaxSetup({cache:false})，这样页面的所有 Ajax都会执行这条语句就是不需要保存缓存记录



#### jQuery.ajax

- Ajax 的核心是 XMLHttpRequest对象(XHR)。XHR为向服务器发送请求和解析服务器响应提供了接口，能够以异步方式从服务器获取新数据。
- jQuery 提供多个与 AJAX 有关的方法
- 通过 jQuery AJAX 方法，能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON – 同时您能够把这些外部数据直接载入网页的被选元素中
- jQuery Ajax 本质就是 XMLHttpRequest，对他进行了封装，方便调用

```
 jQuery.ajax(...)
      部分参数：
          url：请求地址
          type：请求方式，GET、POST（1.9.0之后用method）
        	headers：请求头
          data：要发送的数据
    			contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
          async：是否异步
        	timeout：设置请求超时时间（毫秒）
      		beforeSend：发送请求前执行的函数(全局)
        	complete：完成之后执行的回调函数(全局)
        	success：成功之后执行的回调函数(全局)
          error：失败之后执行的回调函数(全局)
        	accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        	dataType：将服务器端返回的数据转换成指定类型
          "xml": 将服务器端返回的内容转换成xml格式
          "text": 将服务器端返回的内容转换成普通文本格式
          "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        	"script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
          "json": 将服务器端返回的内容转换成相应的JavaScript对象
        	"jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
```

