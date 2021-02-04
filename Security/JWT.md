## JWT

#### Token种类

- 自定义 token
  - 开发者根据业务逻辑自定义的 token
- Oauth2.0
  - 定义在 RFC 6750 中的一种授权规范，但这其实并不是一种 token，只是其中也有用到 token
- JWT
  - JSON Web Token，定义在 RFC 7519 中的一种 token 规范



#### Token 认证的优势

- 无状态
  - token 自身包含了身份验证所需要的所有信息，使得我们的服务器不需要存储 Session 信息，这显然增加了系统的可用性和伸缩性，大大减轻了服务端的压力
  - 由于 token 的无状态，也导致了它最大的缺点：当后端在 token 有效期内废弃一个 token 或者更改它的权限的话，不会立即生效，一般需要等到有效期过后才可以
  - 当用户 Logout 的话，token 也还有效。除非，我们在后端增加额外的处理逻辑
- 有效避免了 CSRF 攻击
  - 一般情况下我们使用 JWT 的话，在我们登录成功获得 token 之后，一般会选择存放在 local storage 中
  - 通过某些方式会给每个发到后端的请求加上这个 token，这样就不会出现 CSRF 漏洞的问题。因为，即使有个你点击了非法链接发送了请求到服务端，这个非法请求是不会携带 token 的，所以这个请求将是非法的
- 适合移动端应用
  - 使用 Session 进行身份认证的话，需要保存一份信息在服务器端，而且这种方式会依赖到 Cookie（需要 Cookie 保存 SessionId），所以不适合移动端
  - 使用 token 进行认证的话，只要 token 可以被客户端存储就能够使用，而且 token 可以跨语言使用
- 单点登录友好
  - 使用 Session 进行身份认证的话，实现单点登录，需要我们把用户的 Session 信息保存在一台电脑上，但会遇到常见的 Cookie 跨域的问题
  - 使用 token 进行认证的话， token 被保存在客户端，不会存在跨域问题



#### Token 认证常见问题以及解决办法

- 注销登录等场景下 token 还有效
  - 

- token 的续签问题

  - token 有效期一般都建议设置的不太长，那么 token 过期后如何认证，如何实现动态刷新 token，避免用户经常需要重新登录

  - 解决方法

    1. 在 Session 认证中一般的做法：假如 session 的有效期30分钟，如果 30 分钟内用户有访问，就把 session 有效期被延长30分钟

    2. 类似于 Session 认证中的做法
       - 这种方案满足于大部分场景
       - 假设服务端给的 token 有效期设置为30分钟，服务端每次进行校验时，如果发现 token 的有效期马上快过期了，服务端就重新生成 token 给客户端
       - 客户端每次请求都检查新旧  token，如果不一致，则更新本地的token
       - 这种做法的问题是仅仅在快过期的时候请求才会更新 token ，对客户端不是很友好
    3. 每次请求都返回新 token
       - 思路很简单
       - 开销会比较大

    4. Token 有效期设置到半夜 
       - 折衷的方案
       - 保证了大部分用户白天可以正常登录
       - 适用于对安全性要求不高的系统

    5. 用户登录返回两个 token
       - 第一个是 acessToken，它的过期时间 token 本身的过期时间比如半个小时，
       - 另一个是 refreshToken，它的过期时间更长一点比如为1天
       - 客户端登录后，将 accessToken和refreshToken 保存在本地，每次访问将 accessToken 传给服务端。服务端校验 accessToken 的有效性，如果过期的话，就将 refreshToken 传给服务端
       - 如果有效，服务端就生成新的 accessToken 给客户端
       - 否则，客户端就重新登录即可
       - 该方案的不足是：需要客户端来配合；用户注销的时候需要同时保证两个 token 都无效；重新请求获取 token 的过程中会有短暂 token 不可用的情况（可以通过在客户端设置定时器，当accessToken 快过期的时候，提前去通过 refreshToken 获取新的accessToken）



#### JWT组成

- 一个 JWT token 是一个字符串，它由三部分组成，头部、载荷与签名，中间用 . 分隔，例如：`xxxxx.yyyyy.zzzzz`
  
- 头部（header）

  - 一个描述 JWT 元数据的 JSON 对象

  - 由两部分组成
    - alg (algorithm)：签名使用的算法
    - typ (type)：令牌类型
  - 最后，使用 Base64 URL 算法将 JSON  对象转换为字符串保存

  ```json
  {
    "alg": "HS256",
    "typ": "JWT"
  }
  ```

- 载荷（Payload）

  - 载荷中放置了 token 的一些基本信息，以帮助接受它的服务器来理解这个 token，同时还可以包含一些自定义的信息，用户信息交换

  - 由七部分可以选择

    - iss (issuer)：签发人
  - sub (subject)：主题
    - aud (audience)：受众
    - exp (expiration time)：过期时间
    - nbf (Not Before)：生效时间，在此之前是无效的
    - iat (Issued At)：签发时间
    - jti (JWT ID)：编号
  
  - 属性也分三类：
  
    - 预定义（Registered）
  
    ```json
    {
      "sub": "1",
      "iss": "http://localhost:8000/auth/login",
      "iat": 1451888119,
      "exp": 1454516119,
      "nbf": 1451888119,
      "jti": "37c107e4609ddbcc9c096ea5ee76c667",
      "aud": "dev"
  }
    ```
    
    - 公有（public）
    - 私有（private）



#### 签名

- Signature
- 通过指定的算法生成哈希，以确保数据不会被篡改
- 算法
  - 首先，需要指定一个密码（secret）。该密码仅仅为保存在服务器中，并且不能向用户公开
  - 使用标头中指定的签名算法（默认情况下为 HMAC SHA256）生成签名
  - 在计算出签名哈希后，JWT 头，有效载荷和签名哈希的三个部分组合成一个字符串，每个部分用"."分隔，构成整个 JWT 对象
- 公式
  - HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(claims), secret)
- 保证 JWT 没有被篡改过
  - HMAC 算法是不可逆算法，类似 MD5 和 hash ，但多一个密钥，密钥（即上面的 secret）由服务端持有，客户端把 token 发给服务端后，服务端可以把其中的头部和载荷再加上事先共享的 secret 再进行一次 HMAC 加密，得到的结果和 token 的第三段进行对比，如果一样则表明数据没有被篡改



#### Base64URL

- JWT 头和有效载荷序列化的算法都用到了 Base64URL
- 该算法和常见 Base64 算法类似，稍有差别
- 算法
  - 作为令牌的 JWT 可以放在 URL 中（例如 api.example/?token=xxx）
  - Base64 中用的三个字符是"+"，"/"和"="，由于在 URL 中有特殊含义因此 Base64URL 中对他们做了替换
  - "="去掉，"+"用"-"替换，"/"用"_"替换



#### JWT使用

- 使用方式
  - 加到 url 中：?token=你的token
  - 加到 header 中，建议用这种，因为在 https 情况下更安全：Authorization:Bearer 你的token
- 在客户端存储的三种方式
  - LocalStorage
  - SessionStorage
  - Cookie [不能设置 HTTPonly]
  - 最推荐的是第三种，因为第一二种存在跨域读取限制，而 Cookie 使用不同的跨域策略



#### JWT安全问题

- 



#### JWT使用场景

- 

