---
layout: post
title:  "Spring Security"
categories:  Spring Security
---

## 添加依赖
```
implementation 'org.springframework.boot:spring-boot-starter-security'
```

## 默认配置
只需要在项目构建中添加 security starter，就可以获得以下安全特性：
- 所有的 HTTP 请求路径都需要认证。
- 不需要特定的角色或权限。
- 没有登录页面。
- 身份验证由 HTTP 基本身份验证提供。
- 只有一个用户；用户名是 user。
当应用程序启动时，自动配置将检测类路径中的 Spring Security，并设置一些基本的安全性配置。
启动应用程序并访问主页（或任何页面）。将提示使用 HTTP 基本身份验证对话框进行身份验证。
用户名： user
密码：
```
Using generated security password: bb2ded7c-7f59-4141-b0cb-48d602f25084
```
在配置文件（application.properties）中更改：
```
spring.security.user.name=admin
spring.security.user.password=12
```

## 自定义配置
Spring Security 为配置用户存储提供了几个选项：
- 一个内存用户存储
- 基于 JDBC 的用户存储
- 由 LDAP 支持的用户存储
- 定制用户详细信息服务
ps:
在手动测试安全性时，将浏览器设置为 private 或 incognito 模式是很有用的。这将确保每次打开私人/隐身窗口时都有一个新的会话。必须每次都登录到应用程序，在安全性方面所做的任何更改都将被应用，并且旧 session 的任何残余都不会阻止你查看你的更改。

## 内存用户存储
假设只有少数几个用户，这些用户都不可能改变。
```
 @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .passwordEncoder(new BCryptPasswordEncoder())
            .withUser("user1")
            .password(new BCryptPasswordEncoder().encode("123456"))
            .roles("USER");
    }
```
内存中的用户存储应用于测试或非常简单的应用程序时非常方便，但是它不允许对用户进行简单的编辑。  
如果需要添加、删除或更改用户，则必须进行必要的更改，然后重新构建、部署应用程序。

## 基于 JDBC 的用户存储
#### 默认配置
```
@Autowired
DataSource dataSource;
​
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
        .jdbcAuthentication()
        .dataSource(dataSource);
}
```
虽然这个最小配置可以工作，但它对数据库模式做了一些假设。具体地说，以下来自 Spring Security 内部的代码片段显示了在查找用户详细信息时将执行的 SQL 查询
```
public static final String DEF_USERS_BY_USERNAME_QUERY = 
    "select username,password,enabled " +
    "from users " +
    "where username = ?";
​
public static final String DEF_AUTHORITIES_BY_USERNAME_QUERY =
    "select username,authority " +
    "from authorities " +
    "where username = ?";
​
public static final String DEF_GROUP_AUTHORITIES_BY_USERNAME_QUERY =
    "select g.id, g.group_name, ga.authority " +
    "from groups g, group_members gm, group_authorities ga " +
    "where gm.username = ? " +
    "and g.id = ga.group_id " +
    "and g.id = gm.group_id";
```
#### 自定义查询
```
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
        .jdbcAuthentication()
            .dataSource(dataSource)
            .usersByUsernameQuery(
                "select username, password, enabled from Users " +
                "where username=?")
            .authoritiesByUsernameQuery(
                "select username, authority from UserAuthorities " +
                "where username=?");
}
```
在将默认 SQL 查询替换为自己设计的查询时，一定要遵守查询的基本约定。
- 它们都以用户名作为唯一参数。
- 身份验证查询选择用户名、密码和启用状态；
- 授权查询选择包含用户名和授予的权限的零个或多个行的数据；
- 组权限查询选择零个或多个行数据，每个行有一个 group id、一个组名和一个权限。
#### 编码加密
通过调用 passwordEncoder() 方法指定一个密码编码器：
```
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
        .jdbcAuthentication()
            .dataSource(dataSource)
            .usersByUsernameQuery(
                "select username, password, enabled from Users " +
                "where username=?")
            .authoritiesByUsernameQuery(
                "select username, authority from UserAuthorities " +
                "where username=?")
            .passwordEncoder(new StandardPasswordEncoder("53cr3t");
}
```
passwordEncoder() 方法接受 Spring Security 的 passwordEncoder 接口的任何实现。Spring Security 的加密模块包括几个这样的实现：
- BCryptPasswordEncoder —— 采用 bcrypt 强哈希加密
- NoOpPasswordEncoder —— 不应用任何编码
- Pbkdf2PasswordEncoder —— 应用 PBKDF2 加密
- SCryptPasswordEncoder —— 应用了 scrypt 散列加密
- StandardPasswordEncoder —— 应用 SHA-256 散列加密
自定义编码实现
```
public interface PasswordEncoder {
    String encode(CharSequence rawPassword);
    boolean matches(CharSequence rawPassword, String encodedPassword);
}
```
无论使用哪种密码编码器，数据库中的密码永远不会被解码。用户在登录时输入的密码使用相同的算法进行编码，然后将其与数据库中编码的密码进行比较。比较是在 PasswordEncoder 的 matches() 方法中执行的。

## 自定义用户身份验证
#### 定义User
```
@Entity
@Data
@NoArgsConstructor(access=AccessLevel.PRIVATE, force=true)
@RequiredArgsConstructor
public class User implements UserDetails {
    private static final long serialVersionUID = 1L;
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    private final String username;
    private final String password;
    private final String fullname;
    private final String street;
    private final String city;
    private final String state;
    private final String zip;
    private final String phoneNumber;
    
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return Arrays.asList(new SimpleGrantedAuthority("ROLE_USER"));
    }
    
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }
    
    @Override
    public boolean isAccountNonLocked() {
        return true;
    }
    
    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }
    
    @Override
    public boolean isEnabled() {
        return true;
    }
}
```
UserDetails 的实现将向框架提供一些基本的用户信息，比如授予用户什么权限以及用户的帐户是否启用。
- getAuthorities() 方法应该返回授予用户的权限集合。
- 各种 isXXXexpired() 方法返回一个布尔值，指示用户的帐户是否已启用或过期。
#### 定义存储库接口
```
public interface UserRepository extends CrudRepository<User, Long> {
    User findByUsername(String username);
}
```
#### 定义Service
```
@Service
public class UserRepositoryUserDetailsService implements UserDetailsService {
    
    private UserRepository userRepo;
    
    @Autowired
    public UserRepositoryUserDetailsService(UserRepository userRepo) {
        this.userRepo = userRepo;
    }
    
    @Override
    public UserDetails loadUserByUsername(String username)
        throws UsernameNotFoundException {
        User user = userRepo.findByUsername(username);
        if (user != null) {
            return user;
        }
    
        throw new UsernameNotFoundException("User '" + username + "' not found");
    }
}
```
#### 定义SecurityConfig
```
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Bean
    public PasswordEncoder encoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService)
                .passwordEncoder(encoder());

    }
}

```

## 保护 web 请求
#### 保护请求
```
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
            .antMatchers("/design", "/orders")
                .access("hasRole('ROLE_USER')")
            .antMatchers(“/”, "/**").access("permitAll")
}
```
指定两个安全规则：
- 对于 /design 和 /orders 的请求应该是授予 ROLE_USER 权限的用户的请求。
- 所有的请求都应该被允许给所有的用户。
#### 用户登录界面
```
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
            .antMatchers("/design", "/orders")
                .access("hasRole('ROLE_USER')")
            .antMatchers(“/”, "/**").access("permitAll")
        
        .and()
            .formLogin()
            .loginPage("/login");
}
```
and() 方法表示已经完成了授权配置，并准备应用一些额外的 HTTP 配置。
连接之后，调用 formLogin() 开始配置自定义登录表单。
```
@Override
public void addViewControllers(ViewControllerRegistry registry) {
    registry.addViewController("/").setViewName("home");
    registry.addViewController("/login");
}
```
在WebConfig中的 addViewControllers() 方法在将 “/” 映射到主控制器的视图控制器旁边设置登录页面视图控制器


