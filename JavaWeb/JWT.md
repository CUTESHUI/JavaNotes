## JWT

#### Token种类

- 自定义的 token：开发者根据业务逻辑自定义的 token
- JWT：JSON Web Token，定义在 RFC 7519 中的一种 token 规范
- Oauth2.0：定义在 RFC 6750 中的一种授权规范，但这其实并不是一种 token，只是其中也有用到 token



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

  - 头部通常由两部分组成
    - 令牌的类型（即 JWT）
    - 正在使用的签名算法（如 HMAC SHA256 或 RSA）

- 载荷（Payload）

  - 载荷中放置了 token 的一些基本信息，以帮助接受它的服务器来理解这个 token，同时还可以包含一些自定义的信息，用户信息交换

  - 载荷的属性也分三类：

    - 预定义（Registered）

    ```
    {
      "sub": "1",
      "iss": "http://localhost:8000/auth/login",
      "iat": 1451888119,
      "exp": 1454516119,
      "nbf": 1451888119,
      "jti": "37c107e4609ddbcc9c096ea5ee76c667",
      "aud": "dev"
    }
    
    iss (issuer)：签发人
    sub (subject)：主题
    aud (audience)：受众
    exp (expiration time)：过期时间
    nbf (Not Before)：生效时间，在此之前是无效的
    iat (Issued At)：签发时间
    jti (JWT ID)：编号
    ```

    - 公有（public）
    - 私有（private）
  
- 签名（Signature）
  
- 保证 JWT 没有被篡改过
  - 原理
  - HMAC 算法是不可逆算法，类似 MD5 和 hash ，但多一个密钥，密钥（即上面的 secret）由服务端持有，客户端把 token 发给服务端后，服务端可以把其中的头部和载荷再加上事先共享的 secret 再进行一次 HMAC 加密，得到的结果和 token 的第三段进行对比，如果一样则表明数据没有被篡改
  



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

