# SpringSecurity

> https://www.cnblogs.com/lihaijia/p/14406272.html

## 简介

在 Web 开发中，安全一直是非常重要的一个方面。

**安全虽然属于应用的非功能性需求，但是应该在应用开发的初期就考虑进来**。

如果在应用开发的后期才考虑安全的问题，就可能陷入一个两难的境地：

- 一方面，应用存在严重的安全漏洞，无法满足用户的要求，并可能造成用户的隐私数据被攻击者窃取；
- 另一方面，应用的基本架构已经确定，要修复安全漏洞，可能需要对系统的架构做出比较重大的调整，因而需要更多的开发时间，影响应用的发布进程。因此，从应用开发的第一天就应该把安全相关的因素考虑进来，并在整个应用的开发过程中。

市面上存在比较有名的：**Shiro，Spring Security ！**

Spring Security是一个框架，**侧重于为Java应用程序提供身份验证和授权**。与所有Spring项目一样，Spring安全性的真正强大之处在于它可以轻松地扩展以满足定制需求

从官网的介绍中可以知道这是一个权限框架。想我们之前做项目是没有使用框架是怎么控制权限的？对于权限 一般会细分为功能权限，访问权限，和菜单权限。**代码会写的非常的繁琐，冗余**。

一般来说，Web 应用的安全性包括用户认证（Authentication）和用户授权（Authorization）两个部分。

- **用户认证**指的是验证某个用户是否为系统中的合法主体，也就是说用户能否访问该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程。
- **用户授权**指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户所具有的权限是不同的。比如对一个文件来说，有的用户只能进行读取，而有的用户可以进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的权限。

对于上面提到的两种应用情景，Spring Security 框架都有很好的支持。

- 在用户认证方面，Spring Security 框架支持主流的认证方式，包括 HTTP 基本认证、HTTP 表单验证、HTTP 摘要认证、OpenID 和 LDAP 等。
- 在用户授权方面，Spring Security 提供了基于角色的访问控制和访问控制列表（Access Control List，ACL），可以对应用中的领域对象进行细粒度的控制。

## 搭建测试环境

新建一个初始的springboot项目web模块，thymeleaf模块

导入静态资源，编写路由跳转

```java
@Controller
public class RouterController {
    @GetMapping(value = {"/", "/index"})
    public String index() {
        return "index";
    }
    @GetMapping("/tologin")
    public String login() {
        return "views/login";
    }
    @GetMapping("/level1/{id}")
    public String level1(@PathVariable("id") int id) {
        return "views/level1/" + id;
    }
    @GetMapping("/level2/{id}")
    public String level2(@PathVariable("id") int id) {
        return "views/level2/" + id;
    }
    @GetMapping("/level3/{id}")
    public String level3(@PathVariable("id") int id) {
        return "views/level3/" + id;
    }
}
```

发现无论如何都会跳转到

![image-20240321163417203](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240321163417203.png)

这是SpringSecurity默认的登录页

这是默认的规则

## SpringSecurity实战

Spring Security 是针对Spring项目的安全框架，也是Spring Boot底层安全模块默认的技术选型，他可以实现强大的Web安全控制，对于安全控制，我们仅需要引入spring-boot-starter-security 模块，进行少量的配置，即可实现强大的安全管理！

记住几个类：

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式  之后也会有很多@Enablexxx

Spring Security的两个主要目标是 “认证” 和 “授权”（访问控制）。

**“认证”（Authentication）**

身份验证是关于验证您的凭据，如用户名/用户ID和密码，以验证您的身份。

身份验证通常通过用户名和密码完成，有时与身份验证因素结合使用。

**“授权” （Authorization）**

授权发生在系统成功验证您的身份后，最终会授予您访问资源（如信息，文件，数据库，资金，位置，几乎任何内容）的完全权限。

这个概念是通用的，而不是只在Spring Security 中存在。

### **编写 Spring Security 配置类**

新建config文件夹，新建MySecurity类

```java
@EnableWebSecurity //开启web安全模式
public class MySecurity extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //默认是拦截所有的访问，自动导向登录页
        //super.configure(http);
        //首页可以访问，但是功能页只有有权限的人才可以访问
        //请求授权规则
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");
        //没有权限默认到登录页
        http.formLogin();
    }

}
```

此时再访问localhost8080就可以正常进入了，但访问其它页面依旧会跳到登录页

### **定义登录认证规则**

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    //可以在内存中定义，也可以去数据库取 auth.jdbcAuthentication()
    //Spring security 5.0中新增了多种加密方式，也改变了密码的格式。
    //要想我们的项目还能够正常登陆，需要修改一下configure中的代码。我们要将前端传过来的密码进行某种方式加密
    //spring security 官方推荐的是使用bcrypt加密方式。
    auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
        .withUser("zhangsan").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2")
        .and()
        .withUser("lisi").password(new BCryptPasswordEncoder().encode("123456")).roles("vip2", "vip3").
        and()
        .withUser("wangwu").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2","vip3");
}
```

此时再跳到登录页时就可以使用账号密码登录，从而方法其权限内的页面

### **注销实现**

1、开启自动配置的注销的功能

```java
//定制请求的授权规则
@Override
protected void configure(HttpSecurity http) throws Exception {
   //....
   //开启自动配置的注销的功能
      // /logout 注销请求
   http.logout();
}
```

2、我们在前端，增加一个注销的按钮，index.html 导航栏中

```html
<a class="item" th:href="@{/logout}">
   <i class="address card icon"></i> 注销
</a>
```

3、我们可以去测试一下，登录成功后点击注销，发现注销完毕会跳转到登录页面！

4、但是，我们想让他注销成功后，依旧可以跳转到首页，该怎么处理呢？

```java
// .logoutSuccessUrl("/"); 注销成功来到首页
http.logout().logoutSuccessUrl("/");
```

5、测试，注销完毕后，发现跳转到首页OK

6、我们现在又来一个需求：用户没有登录的时候，导航栏上只显示登录按钮，用户登录之后，导航栏可以显示登录的用户信息及注销按钮！还有就是，比如kuangshen这个用户，它只有 vip2，vip3功能，那么登录则只显示这两个功能，而vip1的功能菜单不显示！这个就是真实的网站情况了！该如何做呢？

**我们需要结合thymeleaf中的一些功能**

此处略

### 记住我

现在的情况，我们只要登录之后，关闭浏览器，再登录，就会让我们重新登录，但是很多网站的情况，就是有一个记住密码的功能，这个该如何实现呢？很简单

1、开启记住我功能

```java
//定制请求的授权规则
@Override
protected void configure(HttpSecurity http) throws Exception {
//。。。。。。。。。。。
   //记住我
   http.rememberMe();
}
```

2、我们再次启动项目测试一下，发现登录页多了一个记住我功能，我们登录之后关闭 浏览器，然后重新打开浏览器访问，发现用户依旧存在！

思考：如何实现的呢？其实非常简单

我们可以查看浏览器的cookie

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412230410090-275194565.png)

3、我们点击注销的时候，可以发现，spring security 帮我们自动删除了这个 cookie

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412230424964-1382261300.png)
4、结论：登录成功后，将cookie发送给浏览器保存，以后登录带上这个cookie，只要通过检查就可以免登录了。如果点击注销，则会删除这个cookie，具体的原理我们在JavaWeb阶段都讲过了，这里就不在多说了！

### 定制登录页

现在这个登录页面都是spring security 默认的，怎么样可以使用我们自己写的Login界面呢？

1、在刚才的登录页配置后面指定 loginpage

```java
http.formLogin().loginPage("/toLogin");
```

2、然后前端也需要指向我们自己定义的 login请求

```html
<a class="item" th:href="@{/toLogin}">
   <i class="address card icon"></i> 登录
</a>
```

3、我们登录，需要将这些信息发送到哪里，我们也需要配置，login.html 配置提交请求及方式，方式必须为post:

在 loginPage()源码中的注释上有写明：

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412230440929-230804638.png)

```html
<form th:action="@{/login}" method="post">
   <div class="field">
       <label>Username</label>
       <div class="ui left icon input">
           <input type="text" placeholder="Username" name="username">
           <i class="user icon"></i>
       </div>
   </div>
   <div class="field">
       <label>Password</label>
       <div class="ui left icon input">
           <input type="password" name="password">
           <i class="lock icon"></i>
       </div>
   </div>
   <input type="submit" class="ui blue submit button"/>
</form>
```

4、这个请求提交上来，我们还需要验证处理，怎么做呢？我们可以查看formLogin()方法的源码！我们配置接收登录的用户名和密码的参数！

```java
http.formLogin()
  .usernameParameter("username")
  .passwordParameter("password")
  .loginPage("/toLogin")
  .loginProcessingUrl("/login"); // 登陆表单提交请求
```

5、在登录页增加记住我的多选框

```html
<input type="checkbox" name="remember"> 记住我
```

6、后端验证处理！

```java
//定制记住我的参数！
http.rememberMe().rememberMeParameter("remember");
```

7、测试，OK

==总结：Spring Security 的使用多看源码==

# Shiro

## 什么是shiro

- Apache Shiro是一个Java 的安全(权限)框架。
- Shiro可以非常容易的开发出足够好的应用，其不仅可以用在JavaSE环境，也可以用在JavaEE环境。
- Shiro可以完成，认证，授权，加密，会话管理，Web集成，缓存等.
- 下载地址: http://shiro.apache.org/

## shiro的功能

![在这里插入图片描述](https://raw.githubusercontent.com/KyDestroy/image/main/20201103223432234.png)

- Authentication: 身份认证、登录，验证用户是不是拥有相应的身份;
- Authorization:授权,即权限验证，验证某个已认证的用户是否拥有某个权限，即判断用户能否进行什么操作，如:验证某个用户是否拥有某个角色，或者细粒度的验证某个用户对某个资源是否具有某个权限!
- Session Manager: 会话管理，即用户登录后就是第1次会话，在没有退出之前，它的所有信息都在会话中;会话可以是普通的JavaSE环境，也可以是Web环境;
- Cryptography: 加密,保护数据的安全性，如密码加密存储到数据库中，而不是明文存储;
- Web Support: Web支持，可以非常容易的集成到Web环境;
- Caching: 缓存，比如用户登录后，其用户信息，拥有的角色、权限不必每次去查,这样可以提高效率
- Concurrency: Shiro支持多线程应用的并发验证，即，如在一个线程中开启另-一个线程,能把权限自动的传 播过去
- Testing:提供测试支持;
- RunAs:允许一个用户假装为另-一个用户(如果他们允许)的身份进行访问;
- Remember Me:记住我，这个是非常常见的功能，即一次登录后， 下次再来的话不用登录了

## shiro架构

1. 外部结构

   从外部来看`Shiro`,即从应用程序角度来观察如何使用`shiro`完成工作：

   [![在这里插入图片描述](https://raw.githubusercontent.com/KyDestroy/image/main/20201103223750298.png)](https://raw.githubusercontent.com/KyDestroy/image/main/20201103223750298.png)

   - subject: 应用代码直接交互的对象是Subject, 也就是说Shiro的对外API核心就是Subject, Subject代表了当前的用户，这个用户不-定是一个具体的人，与当前应用交互的任何东西都是Subject,如网络爬虫，机器人等，与Subject的所有交互都会委托给SecurityManager; Subject其实是一一个门面， SecurityManageer 才是 实际的执行者
   - SecurityManager: 安全管理器，即所有与安全有关的操作都会与SercurityManager交互, 并且它管理着所有的Subject,可以看出它是Shiro的核心，它负责与Shiro的其他组件进行交互，它相当于SpringMVC的DispatcherServlet的角色
   - Realm: Shiro从Realm获取安全数据 (如用户,角色，权限)，就是说SecurityManager要验证用户身份，那么它需要从Realm获取相应的用户进行比较，来确定用户的身份是否合法;也需要从Realm得到用户相应的角色、权限，进行验证用户的操作是否能够进行，可以把Realm看DataSource

2. 内部结构

   [![在这里插入图片描述](https://raw.githubusercontent.com/KyDestroy/image/main/20201103224134417-16544970837775.png)](https://raw.githubusercontent.com/KyDestroy/image/main/20201103224134417-16544970837775.png)

   - Subject: 任何可以与应用交互的用户;
   - Security Manager:相当于SpringMVC中的DispatcherSerlet; 是Shiro的心脏， 所有具体的交互都通过Security Manager进行控制，它管理者所有的Subject, 且负责进行认证,授权，会话，及缓存的管理。
   - Authenticator:负责Subject认证， 是-一个扩展点，可以自定义实现;可以使用认证策略(Authentication Strategy)，即什么情况下算用户认证通过了;
   - Authorizer:授权器，即访问控制器，用来决定主体是否有权限进行相应的操作;即控制着用户能访问应用中 的那些功能;
   - Realm: 可以有-一个或者多个的realm, 可以认为是安全实体数据源，即用于获取安全实体的，可以用JDBC实现，也可以是内存实现等等，由用户提供;所以- -般在应用中都需要实现自己的realm
   - SessionManager:管理Session生命周期的组件,而Shiro并不仅仅可以用在Web环境，也可以用在普通的JavaSE环境中
   - CacheManager: 缓存控制器，来管理如用户，角色，权限等缓存的;因为这些数据基本上很少改变,放到缓存中后可以提高访问的性能;
   - Cryptography:密码模块，Shiro 提高了一些常见的加密组件用于密码加密， 解密等

## 快速开始

官方项目路径：[shiro/pom.xml at main · apache/shiro · GitHub](https://github.com/apache/shiro)

创建一个普通的maven项目，然后删除`src`目录，创建一个新model——>hello-shiro

pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.9.0</version>
    </dependency>

    <!-- configure logging -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.36</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-slf4j-impl</artifactId>
        <version>2.17.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.17.2</version>
    </dependency>
</dependencies>
```

resources目录下

log4j2.xml 和 shiro.ini

```xml
<Configuration name="ConfigTest" status="ERROR" monitorInterval="5">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="org.springframework" level="warn" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Logger name="org.apache" level="warn" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Logger name="net.sf.ehcache" level="warn" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Logger name="org.apache.shiro.util.ThreadContext" level="warn" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Root level="info">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

```ini
[users]
# user 'root' with password 'secret' and the 'admin' role
root = secret, admin
# user 'guest' with the password 'guest' and the 'guest' role
guest = guest, guest
# user 'presidentskroob' with password '12345' ("That's the same combination on
# my luggage!!!" ;)), and role 'president'
presidentskroob = 12345, president
# user 'darkhelmet' with password 'ludicrousspeed' and roles 'darklord' and 'schwartz'
darkhelmet = ludicrousspeed, darklord, schwartz
# user 'lonestarr' with password 'vespa' and roles 'goodguy' and 'schwartz'
lonestarr = vespa, goodguy, schwartz

# -----------------------------------------------------------------------------
# Roles with assigned permissions
# 
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setRoleDefinitions JavaDoc
# -----------------------------------------------------------------------------
[roles]
# 'admin' role has all permissions, indicated by the wildcard '*'
admin = *
# The 'schwartz' role can do anything (*) with any lightsaber:
schwartz = lightsaber:*
# The 'goodguy' role is allowed to 'drive' (action) the winnebago (type) with
# license plate 'eagle5' (instance specific id)
goodguy = winnebago:drive:eagle5
```

java包下的Quickstart运行类

```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.Subject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;


public class Quickstart {

    private static final transient Logger log = LoggerFactory.getLogger(Quickstart.class);

    public static void main(String[] args) {

        DefaultSecurityManager defaultSecurityManager=new DefaultSecurityManager();
        IniRealm iniRealm=new IniRealm("classpath:shiro.ini");
        defaultSecurityManager.setRealm(iniRealm);
        SecurityUtils.setSecurityManager(defaultSecurityManager);

        // 通过 SecurityUtils 获得当前用户的对象 subject
        Subject currentUser = SecurityUtils.getSubject();

        // 通过当前用户拿到Session
        Session session = currentUser.getSession();
        session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Subject=>session [" + value + "]");
        }

        // 判断当前的用户是否被认证
        if (!currentUser.isAuthenticated()) {
            // Token：令牌
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            token.setRememberMe(true); //设置记住我
            try {
                currentUser.login(token); //执行登录操作
            } catch (UnknownAccountException uae) {
                log.info("There is no user with username of " + token.getPrincipal());
            } catch (IncorrectCredentialsException ice) {
                log.info("Password for account " + token.getPrincipal() + " was incorrect!");
            } catch (LockedAccountException lae) {
                log.info("The account for username " + token.getPrincipal() + " is locked.  " +
                        "Please contact your administrator to unlock it.");
            }
            // ... catch more exceptions here (maybe custom ones specific to your application?
            catch (AuthenticationException ae) {
                //unexpected condition?  error?
            }
        }

        // 打印他们的识别主体（在本例中为用户名）：
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");

        //test a role:
        if (currentUser.hasRole("schwartz")) {
            log.info("May the Schwartz be with you!");
        } else {
            log.info("Hello, mere mortal.");
        }

        // 粗粒度
        //test a typed permission (not instance-level)
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        // 细粒度
        //a (very powerful) Instance Level permission:
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  " +
                    "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }

        // 注销
        //all done - log out!
        currentUser.logout();

        // 结束
        System.exit(0);
    }
}
```

```java
//1、获取当前用户的对象 Subject
Subject currentUser = SecurityUtils.getSubject();
//2、通过当前用户拿到Session
Session session = currentUser.getSession();
//3、判断当前的用户是否被认证
currentUser.isAuthenticated()
//4、获得当前用户的认证
currentUser.getPrincipal()
//5、当前用户是否有某个角色
currentUser.hasRole("schwartz")
//6、根据不同的角色获取不同的权限
currentUser.isPermitted("lightsaber:wield")
//7、注销
currentUser.logout();
```

## 整合Springboot

新建SpringBoot项目,勾选`web`和`thymeleaf`

templates下新建index.html

```html
<!DOCTYPE html>
<html lang="en"
      xmlns:th="https://www.thymeleaf.org"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h1>首页</h1>
<p th:text="${msg}"></p>
<a th:href="@{/user/add}">添加</a>
<a th:href="@{/user/update}">修改</a>
</body>
</html>
```

controller包下新建MyController

```java
@Controller
public class MyController {

    @RequestMapping({"/", "/index"})
    public String toIndex(Model model) {
        model.addAttribute("msg", "Hello,Shiro!");
        return "index";
    }
}
```

加入shiro依赖

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.13.0</version>
</dependency>
```

编写配置类

新建config文件夹，新建ShiroConfig

```java
@Configuration
public class ShiroConfig {

    // ShiroFilterFactoryBean 第三步 注意此处需要给定name，否则报错
    @Bean(name="shiroFilterFactoryBean")
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("getDefaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(defaultWebSecurityManager);
        return shiroFilterFactoryBean;
    }

    // DefaultWebSecurityManager 第二步
    @Bean
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm) {
        DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        //关联userRealm对象
        defaultWebSecurityManager.setRealm(userRealm);//所以这里不能直接 .setRealm(userRealm())
        return defaultWebSecurityManager;
    }

    // 创建 realm 对象 ，需要自定义类 第一步
    @Bean
    public UserRealm userRealm(){ //userRealm() 方法的目的是告诉 Spring 容器如何创建 UserRealm Bean，
        return new UserRealm(); // 而不是为了在其他地方获取 UserRealm 实例。
    }
}
```

新建UserRealm类

```java
public class UserRealm extends AuthorizingRealm {
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("doGetAuthorizationInfo 授权完成");
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("doGetAuthenticationInfo 认证完成");
        return null;
    }
}
```

在templates文件夹下新建user文件夹，下面新建两个简单页面add和update

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
add
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
update
</body>
</html>
```

### 实现登录拦截

**shiro的是通过过滤请求完成拦截，具体而言，就是指定一个map，其中键为对应请求，值为权限分类，通过设置登录请求，将不符合map中规则的转向登录页**

1. ShiroConfig类

   在 ShiroFilterFactoryBean 方法中加入下面代码

   ```java
           // 添加Shiro的内置过滤器
           /*
               anno：无需认证就可以访问
               authc：必须认证才能访问
               user：必须拥有 记住我 功能才能用
               perms：拥有对某个资源的权限才能访问
               role：拥有某个角色权限才能访问
           */
           Map<String, String> filterMap = new LinkedHashMap<>();
   
   //        filterMap.put("/user/add", "authc");
   //        filterMap.put("/user/update", "authc");
           filterMap.put("/user/*", "authc");
   
           bean.setFilterChainDefinitionMap(filterMap);
   
           // 设置登录的请求
           bean.setLoginUrl("/toLogin");
   ```

2. MyController类中添加跳转事务

   ```java
   @Controller
   public class MyController {
   
       @GetMapping({"/", "/index"})
       public String toIndex(Model model) {
           model.addAttribute("msg", "Hello,Shiro!");
           return "index";
       }
   
       @GetMapping("/user/add")
       public String add() {
           return "user/add";
       }
       @GetMapping("/user/update")
       public String update() {
           return "user/update";
       }
       @GetMapping("/toLogin")
       public String toLogin() {
           return "login";
       }
   }
   ```

3. 在templates包下添加login页面

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
   
   <h1>登录</h1>
   <hr>
   <form action="">
       <p>用户名：<input type="text" name="username"></p>
       <p>密码：<input type="password" name="password"></p>
       <p><input type="submit"></p>
   </form>
   
   </body>
   </html>
   ```

### 实现用户认证

**第一步修改login.html使之传递用户名密码**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h1>登录</h1>
<hr>
<p th:text="${msg}" style="color: red"></p>
<form th:action="@{/login}" th:method="post">
    <p>用户名：<input type="text" name="username"></p>
    <p>密码：<input type="password" name="password"></p>
    <p><input type="submit"></p>
</form>

</body>
</html>
```

**第二步在controller中处理登录请求 将用户名和密码封装成token**

```java
@PostMapping("/login")
public String login(String username, String password,Model model) {
    //获取当前用户 subject
    Subject currentUser = SecurityUtils.getSubject();

    //封装用户的登录数据，从前端发过来的
    UsernamePasswordToken token = new UsernamePasswordToken(username, password);

    //检测是否正确
    try {
        currentUser.login(token);
        return "index";
    }catch (UnknownAccountException e){
        model.addAttribute("msg", "用户名不存在");
        return "login";
    }catch (IncorrectCredentialsException e){
        model.addAttribute("msg", "密码不正确");
        return "login";
    }
}
```

**第三步在 UserRealm类中判断用户名密码是否正确**

```java
//认证
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
    System.out.println("doGetAuthenticationInfo 执行认证");

    //用户名 密码可以从数据库取，这里先伪造一个
    String username = "admin";
    String password = "123456";

    UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;

    if (!token.getUsername().equals(username)) {
        return null;//这里return null 会自动帮我们抛出异常 UnknownAccountException
    }
    // 密码认证，shiro做 不用我们做
    return new SimpleAuthenticationInfo("",password,"");
}
```

### 整合Mybatis

整合druid和mybatis

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.9</version>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.3.1</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

`application.properties`配置别名和mapper位置

```properties
mybatis.type-aliases-package=com.balance.pojo
mybatis.mapper-locations=classpath:mapper/*.xml
```

在`application.yml`配置 druid

```yml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/test?useSSL=true&useUnicode=true&characterEncoding=utf8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

新建DruidConfig，将配置文件中属性与其绑定

```java
@Configuration
public class DruidConfig {

    /*
       将自定义的 Druid数据源添加到容器中，不再让 Spring Boot 自动创建
       绑定全局配置文件中的 druid 数据源属性到 com.alibaba.druid.pool.DruidDataSource从而让它们生效
       @ConfigurationProperties(prefix = "spring.datasource")：作用就是将 全局配置文件中
       前缀为 spring.datasource的属性值注入到 com.alibaba.druid.pool.DruidDataSource 的同名参数中
     */
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource() {
        return new DruidDataSource();
    }
}
```

POJO层

- 实体类User

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
}
```

Mapper层

- UserMapper接口类

```java
@Mapper
@Repository
public interface UserMapper {
    User getUserByName(String name);
}
```

- UserMapper接口实现类`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.balance.mapper.UserMapper">

    <select id="getUserByName" parameterType="string" resultType="user">
        select * from users where name = #{name}
    </select>

</mapper>
```

Service层

- UserService业务接口类

```java
public interface UserService {
    User getUserByName(String name);
}
```

- UserService业务接口实现类`UserServiceImpl`

```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    UserMapper userMapper;

    @Override
    public User getUserByName(String name) {
        return userMapper.getUserByName(name);
    }
}
```

更改UserRealm类

```java
public class UserRealm extends AuthorizingRealm {
    @Autowired
    UserServiceImpl userService;
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("doGetAuthorizationInfo 执行授权");
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("doGetAuthenticationInfo 执行认证");

        //用户从数据库取
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        User user = userService.getUserByName(token.getUsername());

        if(user == null){
            return null;// UnknownAccountException
        }
        // 密码认证，shiro做 不用我们做
        return new SimpleAuthenticationInfo("",user.getPassword(),"");
    }
}
```

测试，成功认证

![image-20240323163722538](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240323163722538.png)

### 用户授权实现

数据库层面

- 添加多一个字段，用来存储用户的授权

![image-20240323165859943](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240323165859943.png)

实体类User添加新字段

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
    private String permission;
}
```

ShiroConfig配置类

- ShiroFilterFactoryBean 方法 此方法需要增加对特定权限的过滤
- **注意此处需要将需要权限的放在前面，因为LinkedHashMap是有序的，如果前面的通过就直接放行了**

```java
// ShiroFilterFactoryBean 第三步
@Bean(name="shiroFilterFactoryBean")
public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("getDefaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager){
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    //设置安全管理器
    shiroFilterFactoryBean.setSecurityManager(defaultWebSecurityManager);
    // 添加Shiro的内置过滤器
    /*
            anno：无需认证就可以访问
            authc：必须认证才能访问
            user：必须拥有 记住我 功能才能用
            perms：拥有对某个资源的权限才能访问
            role：拥有某个角色权限才能访问
        */
    Map<String, String> filterMap = new LinkedHashMap<>();

    filterMap.put("/user/update", "perms[user:update]");
    filterMap.put("/user/add", "perms[user:add]");
    filterMap.put("/user/*", "authc");

    shiroFilterFactoryBean.setFilterChainDefinitionMap(filterMap);

    // 设置登录的请求 未认证会跳转到登录页
    shiroFilterFactoryBean.setLoginUrl("/toLogin");
    // 设置未授权的请求
    shiroFilterFactoryBean.setUnauthorizedUrl("/noauth");

    return shiroFilterFactoryBean;
}
```

MyController类设置无授权处理

```java
@GetMapping("/noAuth")
@ResponseBody
public String noAuth(){
    return "无权访问此页面";
}
```

UserRealm配置类 对用户权限进行判断

* 认证方法中返回值修改

```java
return new SimpleAuthenticationInfo(user,user.getPassword(),"");
```

* 授权方法修改

```java
//授权
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
    System.out.println("doGetAuthorizationInfo 执行授权");

    SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();

    //拿到这个登录对象
    Subject subject = SecurityUtils.getSubject();
    User currentUser = (User) subject.getPrincipal();//因为认证中SimpleAuthenticationInfo中principal放入了user

    //设置当前用户权限
    info.addStringPermission(currentUser.getPermission());
    return info;
}
```

测试 以张三为例，他有进入add的权限但无update权限

![image-20240323170833870](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240323170833870.png)

访问修改就无权限了

![image-20240323171711580](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240323171711580.png)
