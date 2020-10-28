## SpringCloudSecurity_OAuth2

#### 简介

- OAuth2是一个关于授权的开放标准、
- 核心思路是通过各类认证手段（具体什么手段OAuth2不关心）认证用户身份，并颁发 token，使得第三方应用可以使用该令牌在限定时间、限定范围访问指定资源



#### 基本概念

- resource owner: 拥有被访问资源的用户
- user-agent: 一般来说就是浏览器
- client: 第三方应用
- Authorization server: 认证服务器，用来进行用户认证并颁发token
- Resource server：资源服务器，拥有被访问资源的服务器，需要通过token来确定是否有权限访问