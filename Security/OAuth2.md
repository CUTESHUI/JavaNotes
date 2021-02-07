## OAuth2

#### 简介

- 一个行业的标准授权协议，主要用来授权第三方应用获取有限的权限
- OAuth 2.0是对 OAuth 1.0 的完全重新设计，OAuth 2.0更快，更容易实现，OAuth 1.0 已经被废弃
- 最终目的是为第三方应用颁发一个有时效性的令牌 token，使得第三方应用能够通过该令牌获取相关的资源



#### 第一种授权方式：授权码

- 第三方应用先申请一个授权码，然后再用该码获取令牌，这种方式是最常用的流程，安全性也最高

- 适用

  - 有后端的 Web 应用

- 优点

  - 授权码通过前端传送，令牌则是储存在后端，而且所有与资源服务器的通信都在后端完成
  - 这样的前后端分离，可以避免令牌泄漏

- 流程

  - A 网站提供一个链接，用户点击后就会跳转到 B 网站，授权用户数据给 A 网站使用，下面就是 A 网站跳转 B 网站的一个示意链接
    - 参数
    - response_type：要求返回授权码（code）
    - client_id：让 B 知道是谁在请求
    - redirect_uri： B 接受或拒绝请求后的跳转网址
  - scope：要求的授权范围（这里是只读）
  
  ```
  https://b.com/oauth/authorize?
    response_type=code&
    client_id=CLIENT_ID&
    redirect_uri=CALLBACK_URL&
    scope=read
```
  
- 用户跳转后，B 网站会要求用户登录，然后询问是否同意给予 A 网站授权，用户表示同意，这时 B 网站就会跳回 redirect_uri 参数指定的网址，跳转时，会传回一个授权码
  
  ```
  https://a.com/callback?code=AUTHORIZATION_CODE
```
  
  - A 网站拿到授权码以后，就可以在后端，向 B 网站请求令牌
    - 参数
    - client_id、client_secret：让 B 确认 A 的身份（client_secret 参数是保密的，因此只能在后端发请求）
    - grant_type：值是 AUTHORIZATION_CODE，表示采用的授权方式是授权码
  - code：是上一步拿到的授权码
    - redirect_uri：令牌颁发后的回调网址
  
  ```
  https://b.com/oauth/token?
    client_id=CLIENT_ID&
    client_secret=CLIENT_SECRET&
    grant_type=authorization_code&
    code=AUTHORIZATION_CODE&
  redirect_uri=CALLBACK_URL
  ```

  - B 网站收到请求以后，就会颁发令牌。具体做法是向 redirect_uri 指定的网址，发送一段 JSON 数据
    - 参数
    - access_token：令牌，A 网站在后端拿到了
  
  ```json
  {    
    "access_token":"ACCESS_TOKEN",
    "token_type":"bearer",
    "expires_in":2592000,
    "refresh_token":"REFRESH_TOKEN",
    "scope":"read",
  "uid":100101,
    "info":{...}
  }
  ```
  



#### 第二种授权方式：隐藏式

- 这种方式没有授权码这个中间步骤，所以授权码隐藏式

- 适用

  - 有些 Web 应用是纯前端应用，没有后端，这时就不能用上面的方式了，必须将令牌储存在前端

- 流程

  - A 网站提供一个链接，要求用户跳转到 B 网站，授权用户数据给 A 网站使用
    - 参数
    - response_type：token，表示要求直接返回令牌

  ```
  https://b.com/oauth/authorize?
    response_type=token&
    client_id=CLIENT_ID&
    redirect_uri=CALLBACK_URL&
    scope=read
  ```

  - 用户跳转到 B 网站，登录后同意给予 A 网站授权。这时，B 网站就会跳回 redirect_uri 参数指定的跳转网址，并且把令牌作为 URL 参数，传给 A 网站
    - token：令牌，A 网站因此直接在前端拿到令牌
    - 令牌的位置是 URL 锚点（fragment），而不是查询字符串（querystring），这是因为 OAuth 2.0 允许跳转网址是 HTTP 协议，因此存在"中间人攻击"的风险，而浏览器跳转时，锚点不会发到服务器，就减少了泄漏令牌的风险

  ```
  https://a.com/callback#token=ACCESS_TOKEN
  ```

  - 注意
    - 令牌的位置是 URL 锚点（fragment），而不是查询字符串（querystring），这是因为 OAuth 2.0 允许跳转网址是 HTTP 协议，因此存在"中间人攻击"的风险，而浏览器跳转时，锚点不会发到服务器，就减少了泄漏令牌的风险
    - 这种方式把令牌直接传给前端，是很不安全的，因此，只能用于一些安全要求不高的场景，并且令牌的有效期必须非常短，通常就是会话期间（session）有效，浏览器关掉，令牌就失效了



#### 第三种授权方式：密码式

- 适用

  - 如果高度信任某个应用，RFC 6749 也允许用户把用户名和密码直接告诉该应用，该应用就使用你的密码，申请令牌

- 流程

  - A 网站要求用户提供 B 网站的用户名和密码。拿到以后，A 就直接向 B 请求令牌
    - 参数
    - grant_type=password：授权方式，密码式
    - username 和 password 是 B 的用户名和密码

  ```
  https://oauth.b.com/token?
    grant_type=password&
    username=USERNAME&
    password=PASSWORD&
    client_id=CLIENT_ID
  ```

  - B 网站验证身份通过后，直接给出令牌，注意，这时不需要跳转，而是把令牌放在 JSON 数据里面，作为 HTTP 回应，A 因此拿到令牌

- 注意

  - 这种方式需要用户给出自己的用户名/密码，显然风险很大



#### 第四种授权方式：凭证式

- 适用

  - 没有前端的命令行应用，即在命令行下请求令牌

- 流程

  - A 应用在命令行向 B 发出请求
    - 参数
    - grant_type=client_credentials：授权方式，凭证式
    - client_id 和 client_secret 用来让 B 确认 A 的身份

  ```
  https://oauth.b.com/token?
    grant_type=client_credentials&
    client_id=CLIENT_ID&
    client_secret=CLIENT_SECRET
  ```

  - B 网站验证通过以后，直接返回令牌

- 注意

  - 这种方式给出的令牌，是针对第三方应用的，而不是针对用户的，即有可能多个用户共享同一个令牌



#### 令牌使用

- A 网站拿到令牌以后，就可以向 B 网站的 API 请求数据了，每个发到 API 的请求，都必须带有令牌
- 具体方法
  - 在请求的头信息，加上一个 Authorization 字段，令牌就放在这个字段里面

```bash
curl -H "Authorization: Bearer ACCESS_TOKEN" "https://api.b.com"
```



#### 更新令牌

- 令牌的有效期到了，如果让用户重新走一遍上面的流程，再申请一个新的令牌，很可能体验不好，而且也没有必要
- OAuth 2.0 允许用户自动更新令牌
- 具体方法是
  - B 网站颁发令牌的时候，一次性颁发两个令牌，一个用于获取数据，另一个用于获取新的令牌（refresh token 字段）
  - 令牌到期前，用户使用 refresh token 发一个请求，去更新令牌
  - 参数
  - grant_type=refresh_token：要求更新令牌
  - client_id 和 client_secret：用于确认身份
  - refresh_token：用于更新令牌的令牌

```
https://b.com/oauth/token?
  grant_type=refresh_token&
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET&
  refresh_token=REFRESH_TOKEN
```

