## SpringCloudSecurity_OAuth2

#### 简介

- OAuth2是一个关于授权的开放标准、
- 核心思路是通过各类认证手段（具体什么手段OAuth2不关心）认证用户身份，并颁发 token，使得第三方应用可以使用该令牌在限定时间、限定范围访问指定资源



#### 基本概念

- resource owner：拥有被访问资源的用户
- user-agent：一般来说就是浏览器
- client：第三方应用
- Authorization server：认证服务器，用来进行用户认证并颁发token
- Resource server：资源服务器，拥有被访问资源的服务器，需要通过token来确定是否有权限访问



#### 四种授权模式

- Authorization Code（授权码模式）
  - 正宗的OAuth2的授权模式
  - 客户端先将用户导向认证服务器，登录后获取授权码，然后进行授权，最后根据授权码获取访问令牌
- Implicit（简化模式）
  - 和授权码模式相比，取消了获取授权码的过程，直接获取访问令牌
- Resource Owner Password Credentials（密码模式）
  - 客户端直接向用户获取用户名和密码，之后向认证服务器获取访问令牌
- Client Credentials（客户端模式）
  - 客户端直接通过客户端认证（比如client_id和client_secret）从认证服务器获取访问令牌



#### 授权码模式

- 客户端将用户导向认证服务器
- 用户在认证服务器进行登录并授权
- 认证服务器返回授权码给客户端
- 客户端通过授权码和跳转地址向认证服务器获取访问令牌
- 认证服务器发放访问令牌（有需要带上刷新令牌）



#### 密码模式

- 客户端从用户获取用户名和密码
- 客户端通过用户的用户名和密码访问认证服务器
- 认证服务器返回访问令牌（有需要带上刷新令牌）



#### Oauth2的使用

- 添加 UserService 实现 UserDetailsService 接口，用于加载用户信息

```java
@Service
public class UserService implements UserDetailsService {
    private List<User> userList;
    @Autowired
    private PasswordEncoder passwordEncoder;

    @PostConstruct
    public void initData() {
        String password = passwordEncoder.encode("123456");
        userList = new ArrayList<>();
        userList.add(new User("tom", password, AuthorityUtils.commaSeparatedStringToAuthorityList("admin")));
        userList.add(new User("blue", password, AuthorityUtils.commaSeparatedStringToAuthorityList("client")));
        userList.add(new User("red", password, AuthorityUtils.commaSeparatedStringToAuthorityList("client")));
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        List<User> findUserList = userList.stream().filter(user -> user.getUsername().equals(username)).collect(Collectors.toList());
        if (!CollectionUtils.isEmpty(findUserList)) {
            return findUserList.get(0);
        } else {
            throw new UsernameNotFoundException("用户名或密码错误");
        }
    }
}
```

- 添加认证服务器配置，使用 @EnableAuthorizationServer 注解开启

```java
@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private UserService userService;

    /**
     * 使用密码模式需要配置
     */
    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) {
        endpoints.authenticationManager(authenticationManager)
                .userDetailsService(userService);
    }

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
                .withClient("admin")	//配置client_id
                .secret(passwordEncoder.encode("admin123456"))	//配置client_secret
                .accessTokenValiditySeconds(3600)			//配置访问token的有效期
                .refreshTokenValiditySeconds(864000)	//配置刷新token的有效期
                .redirectUris("http://www.baidu.com")	//配置redirect_uri，用于授权成功后跳转
                .scopes("all")	//配置申请的权限范围
                .authorizedGrantTypes("authorization_code","password");	//配置grant_type，表示授权类型
    }
}
```

- 添加资源服务器配置，使用 @EnableResourceServer 注解开启

```java
@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .requestMatchers()
                .antMatchers("/user/**");	//配置需要保护的资源路径
    }
}
```

- 添加 SpringSecurity 配置，允许认证相关路径的访问及表单登录

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.csrf()
                .disable()
                .authorizeRequests()
                .antMatchers("/oauth/**", "/login/**", "/logout/**")
                .permitAll()
                .anyRequest()
                .authenticated()
                .and()
                .formLogin()
                .permitAll();
    }
}
```

- oauth2中存储令牌的方式

```java
/**
 * 使用redis存储token的配置
 */
@Configuration
public class RedisTokenStoreConfig {

    @Autowired
    private RedisConnectionFactory redisConnectionFactory;

    @Bean
    public TokenStore redisTokenStore (){
        return new RedisTokenStore(redisConnectionFactory);
    }
}
```

```java
@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private UserService userService;

    @Autowired
    @Qualifier("redisTokenStore")
    private TokenStore tokenStore;

    /**
     * 使用密码模式需要配置
     */
    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) {
        endpoints.authenticationManager(authenticationManager)
                .userDetailsService(userService)
                .tokenStore(tokenStore);	//配置令牌存储策略
    }

    // ...
}
```

