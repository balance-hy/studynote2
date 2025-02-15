# java企业web开发

## 文件上传 -springboot

前端格式

```html
<form action="/upload" method="post" enctype="multipart/form-data">
    
	<input type="file" name="file">
	<input type="submit" value="提交">
    
</form>
```

后端

实际上如果不上传到 resources 目录下，我们可以直接指定文件路径

`file.transferTo(new File("D:\\uploadFile"+"\\"+originalFilename));`

```java
@RestController
public class CommonController {
    @PostMapping("/upload")
    public Result<String> upload(MultipartFile file) {
        try {
            String originalFilename = UUID.randomUUID()
                .toString()+
                file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf("."));
            file.transferTo(new File("D:\\uploadFile"+"\\"+originalFilename));//上传
            return Result.success("http://localhost:8080/admin/common/file/"+originalFilename);
        } catch (IOException e) {
            return Result.error("文件上传失败");
        }
    }
}
```

**注意有两个问题**

* 文件大小默认是 1mb，超过会报错,可以在配置文件中配置

  * ```yml
    spring:
      servlet:
        multipart:
          max-file-size: 10MB # 单个文件不超过10MB
          max-request-size: 100MB # 多个文件不超过 100MB
    ```

* 如何访问上传之后的文件呢

  * ```java
    @GetMapping("/file/{fileName}")
    @ApiOperation("图片获取")
    public byte[] getFile(@PathVariable String fileName) throws IOException {
        String filePath="D:\\uploadFile\\"+fileName;
        Path path = Paths.get(filePath);
        return Files.readAllBytes(path);
    }
    ```

### 阿里云OSS对象存储

#### 前置

bucket即阿里云OSS中的存储空间，首先需要创建一个bucket

```
oss-cn-hangzhou.aliyuncs.com  # 节点地址
web-project-balance # 名字
```

获得Accesskey，这是用于访问阿里云api的秘钥，具有账户的完全权限

```
AccessKey ID:LTAI5t6ajMtSuWtT9i6mXHS2
AccessKey Secret:yGGGPG8vrer1bnuPTQpTo62GKFbxSQ
```

> [安装OSS Java SDK_对象存储(OSS)-阿里云帮助中心 (aliyun.com)](https://help.aliyun.com/zh/oss/developer-reference/java-installation?spm=a2c4g.11186623.0.i12)

为了能够获取云存储服务，我们需要获取安装 java 的SDK，即软件开发包

对于maven来说，需要导入依赖

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.15.1</version>
</dependency>
```

如果使用的是Java 9及以上的版本，则需要添加JAXB相关依赖。添加JAXB相关依赖示例代码如下：

```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>
<!-- no more than 2.3.3-->
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
    <version>2.3.3</version>
</dependency>
```

#### 代码示例

```java
public class Demo {
    public static void main(String[] args) throws Exception {
        // Endpoint以华东1（杭州）为例，其它Region请按实际情况填写。
        String endpoint = "https://oss-cn-hangzhou.aliyuncs.com";
        String accessKeyId = "LTAI5t6ajMtSuWtT9i6mXHS2";
        String accessKeySecret = "yGGGPG8vrer1bnuPTQpTo62GKFbxSQ";

        String bucketName = "web-project-balance";
        String objectName = "1.jpg";

        String filePath = "C:\\Users\\18356\\Pictures\\wallhaven-4d5qwo.jpg";

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        try {
            InputStream inputStream = new FileInputStream(filePath);
            ossClient.putObject(bucketName, objectName, inputStream);
        } catch (OSSException oe) {
            System.out.println("Caught an OSSException, which means your request made it to OSS, "
                    + "but was rejected with an error response for some reason.");
            System.out.println("Error Message:" + oe.getErrorMessage());
            System.out.println("Error Code:" + oe.getErrorCode());
            System.out.println("Request ID:" + oe.getRequestId());
            System.out.println("Host ID:" + oe.getHostId());
        } catch (ClientException ce) {
            System.out.println("Caught an ClientException, which means the client encountered "
                    + "a serious internal problem while trying to communicate with OSS, "
                    + "such as not being able to access the network.");
            System.out.println("Error Message:" + ce.getMessage());
        } finally {
            if (ossClient != null) {
                ossClient.shutdown();
            }
        }
    }
}
```

观看代码其实不难总结使用步骤

* 配置 endpoint 即访问的url，配置 acessid和key，填写要存储到哪一个 bucketName，上传后的文件名字objectName 以及本地资源路径 filepath

* 创建OSSClient实例。

  * ```java
    OSS ossClient = new OSSClientBuilder().build(endpoint, acessKeyId,acessKeySecret);
    ```

* 创建输入流读取本地资源

  * ```java
    InputStream inputStream=new FileInputStream()
    ```

* 创建putobject请求，即将资源上传到云端

  * ```java
    ossClient.putObject(bucketName, objectName, inputStream);
    ```

* 最后关闭 ossClient

  * ```java
    ossClient.shutdown();
    ```

## Jwt 令牌校验

**Json Web Token** 

![image-20240407162323261](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407162323261.png)

- 会话：用户打开浏览器，访问web服务器的资源，会话建立，直到有一方断开连接，会话结束。在一次会话中可以包含多次请求和响应。
- 会话跟踪：一种维护浏览器状态的方法，服务器需要识别多次请求是否来自于同一浏览器，以便在同一次会话的多次请求间共享数据。

客户端：cookie

* 缺点：移动端APP无法使用cookie
* 不安全，用户可以自己禁用cookie
* Cookie不能跨域 （==协议、IP、端口，有一不同，即为跨域==），每个 Cookie 都会绑定单一的域名

服务端：session

* 优点：存储在服务端，安全
* 缺点：
  * 服务器集群环境下无法直接使用Session(多台服务器，不知道是哪一个session，若只有一台服务器，则会有单点故障，即一台服务器故障，所有服务下线，所以现在多使用集群防灾)
  * Cookie的缺点

现在：token 令牌技术

* 优点：支持PC端、移动端
  * 解决集群环境下的认证问题
  * 减轻服务器端存储压力
* 缺点：需要自己实现

### java生成Jwt

首先引入依赖

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

```java
public class GenJWT {
    public static void main(String[] args) {
        HashMap<String, Object> map = new HashMap<>();
        map.put("id",1);
        map.put("username","balance");
        String jwt = Jwts.builder()
                .signWith(SignatureAlgorithm.HS256, "balance") //第一个参数为密码算法，第二个为密钥
                .setClaims(map) //设置 JWT 的声明（claims）。声明是包含有关用户和其他实体的信息的 JSON 对象
                .setExpiration(new Date(System.currentTimeMillis() + 3600 * 1000)) //设置有效期 1H
                .compact();//将构建好的 JWT 转换为一个紧凑的、URL安全的字符串表示形式。这个字符串就是最终的 JWT。

        System.out.println(jwt);
    }
}
```

```
eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwiZXhwIjoxNzEyNDgyNjMxLCJ1c2VybmFtZSI6ImJhbGFuY2UifQ.4_OyAaCH3iKsM0KXWlQx-YC9c6_RaNUGXoNLuij8DYc
```

生成的jwt是base64编码后的数据，可以直接解码，只有最后一部分无法解码，因为需要密钥

可以这样解析

```java
public static void main(String[] args) {
    Claims claim = Jwts.parser()
        .setSigningKey("balance") //设置密钥
      .parseClaimsJws("eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwiZXhwIjoxNzEyNDgyNjMxLCJ1c2VybmFtZSI6ImJhbGFuY2UifQ.4_OyAaCH3iKsM0KXWlQx-YC9c6_RaNUGXoNLuij8DYc")
        .getBody();//获得我们自定义的部分，即 claims 部分
    System.out.println(claim);

}
```

## 过滤器 -springboot

过滤器是javaweb中的，springboot 若使用过滤器，必须开启 `ServletComponentScan` 支持，并且在实现了 Filter 的类上加上 `webFilter` 注解，配置过滤路径。

![image-20240407170741542](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407170741542.png)

![image-20240407171042988](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407171042988.png)

过滤器链，当一个过滤器执行结束，直接进入下一个过滤器

**注意：过滤器的执行顺序是按类名字符串排序执行的**

可以通过`@Order`注解显式指定过滤器顺序

## 拦截器 -springboot

定义拦截器，实现Handlerlnterceptor接口，并重写其所有方法

```java
public class MyInterceptor implements HandlerInterceptor {
    //在请求处理的方法之前执行
    //如果返回true执行下一个拦截器
    //如果返回false就不执行下一个拦截器
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        System.out.println("------------处理前------------");
        return true;
    }

    //在请求处理方法执行之后执行
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        System.out.println("------------处理后------------");
    }

    //在dispatcherServlet处理后执行,做清理工作.
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        System.out.println("------------清理------------");
    }
}
```

注册拦截器,创建配置类，实现 webMvcConfigurer 接口，重写 addInterceptors 方法，可以在此指定 url 路径

```java
@Configuration
@Slf4j
public class MyWebMvcConfiguration extends WebMvcConfigurationSupport {

    @Autowired
    private JwtTokenAdminInterceptor jwtTokenAdminInterceptor;

    /**
     * 注册自定义拦截器
     *
     * @param registry
     */
    protected void addInterceptors(InterceptorRegistry registry) {
        log.info("开始注册自定义拦截器...");
        registry.addInterceptor(jwtTokenAdminInterceptor)
                .addPathPatterns("/admin/**") //注意所有资源需要 /**
                .excludePathPatterns("/admin/employee/login");
    }
}
```

![image-20240407193019043](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407193019043.png)

## 全局异常处理器

```java
@RestControllerAdvice //结合了 @ControllerAdvice 和 @ResponseBody 注解的功能。它主要用于 REST ful API 开发中，
@Slf4j // 用于全局性地处理控制器层（Controller）抛出的异常，并以 JSON 格式返回异常信息。
public class GlobalExceptionHandler {

    /**
     * 捕获业务异常
     * @param ex
     * @return
     */
    @ExceptionHandler(Exception.class) 
    //通过使用 @ExceptionHandler 注解，可以将方法标记为用于处理特定异常类型的处理器方法。参数为要捕获哪一类型的异常
    //不指定异常类型参数，则该方法将处理所有未被其他 @ExceptionHandler 注解标记的异常
    public Result exceptionHandler(BaseException ex){
        log.error("异常信息：{}", ex.getMessage());
        return Result.error(ex.getMessage());
    }

}
```

## SpringBoot事务

SpringBoot的事务注解，因为多张表，我们希望一个service的操作应该是原子性的，即要么都成功，要么都失败

* 如何开启事务呢
* **在需要事务的方法上加  `@Transactional`  在启动类上加 `@EnableTransactionManagement //开启注解方式的事务管理`**

![image-20240407200628994](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407200628994.png)

![image-20240407200819061](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407200819061.png)

注意，如果有需求要求在多个方法后加操作日志，这个日志应该是新事务，而不是加入到其它方法的旧日志，这样就不会因为其它方法的事务回滚而影响日志的新增。

## Maven高级

中大型项目构建管理所需

### 分模块设计与开发

分模块设计，为什么？

将项目按照功能拆分成若干个子模块，方便项目的管理维护、扩展，也方便模块间的相互调用，资源共享。

**注意：**分模块开发需要先针对模块功能进行设计，再进行编码。不会先将工程开发完毕，然后进行拆分。

![image-20240408180548508](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240408180548508.png)

### 继承与聚合

#### 继承

所谓继承，就是把每一个模块的共有依赖定义在父工程当中，这样就会自动继承，从而减少不必要的配置

实现：`<parent>…</parent>`

![image-20240408190034065](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240408190034065.png)

```xml
<packaging>pom</packaging>
```

#### 版本锁定

在maven中，可以在父工程的pom文件中通过`<dependencyManagement>`来统一管理依赖版本。

在父工程设定好版本

```java
<dependencyManagement>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>xxxxxx</version>
    </dependency>
</dependencyManagement>
```

**注意 dependencyManagement 只是锁定版本，不能做到子工程继承定义其中的依赖，需要显式引入，只不过不需要指定版本**

可以在pom.xml中定义属性，即所有的版本，从而抽离出来，方便管理,使用` ${}` 来取

```xml
<properties>
    <mybatis.spring>2.2.0</mybatis.spring>
    <lombok>1.18.20</lombok>
</properties>
<dependencyManagement>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>${mybatis.spring}</version>
    </dependency>
</dependencyManagement>
```

#### 聚合

分模块开发会导致一个问题，之前程序开发完成直接点击 package 就可以打包成jar包，现在需要将父工程以及子工程全部打包，如果模块很多，无疑很繁琐。

**聚合：**将多个模块组织成一个整体，同时进行项目的构建。

为了实现聚合，必须要有一个聚合工程（一个不具有业务功能的“空”工程（有且仅有一个pom文件））。

然后在其中定义

```xml
<modules>
    <module>xxxx子模块</module>
    <module>xxxx子模块</module>
    <module>xxxx子模块</module>
</modules>
```

之后就可以只打包聚合工程

### 私服

如果有多个项目组，每个项目组有自己的工具类，如果想要用别的项目组的工具类如何进行？

会先在本地仓库查找依赖，没有再去远程中央仓库，但我们的工具类是在本地仓库的，我们无法访问到别人的本地仓库，我们也无法将自己编写的jar包上传到中央仓库（需资格）。

所以就需要私有服务器！

> 私服是一种特殊的远程仓库，它是架设在局域网内的仓库服务，用来代理位于外部的中央仓库，用于解决团队内部的资源共享与资源同步问题。

![image-20240408194624369](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240408194624369.png)

项目版本：

* RELEASE（发行版本）：功能趋于稳定、当前更新停止，可以用于发行的版本，存储在私服中的RELEASE仓库中。
* SNAPSHOT（快照版本）：功能不稳定、尚处于开发中的版本，即快照版本，存储在私服的SNAPSHOT仓库中。

具体配置步骤可以参照

> https://www.bilibili.com/video/BV1m84y1w7Tb?p=199&spm_id_from=pageDriver&vd_source=17542f416e2251679b4c28b8e3f5e220

## HttpClient

HttpClient是Apache的一个子项目，是高效的、功能丰富的支持HTTP协议的**客户端编程工具包**

**构造和发送 Http请求到第三方获得服务**

![image-20240410191055270](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410191055270.png)

导入依赖

如果导入过阿里云sdk就无需再导这个包，因为已经包含在内了

```xml
<dependency>    
    <groupId>org.apache.httpcomponents</groupId>    
    <artifactId>httpclient</artifactId>    
    <version>4.5.13</version>
</dependency>
```

**核心API：**

- HttpClient 
  - 使用其发送请求
- HttpClients 
  - 创建HttpClient 对象
- CloseableHttpClient 
  - 具体的 HttpClient  实现类
- HttpGet
  - get请求
- HttpPost
  - post请求

**发送请求步骤：**

- 创建HttpClient对象
- 创建Http请求对象 （HttpGet/HttpPost）
- 调用HttpClient的execute方法发送请求

### get

```java
@SpringBootTest
public class HttpClientTest {

    //通过HttpClient发送get方式请求
    @Test
    public void testHttpClient() throws IOException {
        //创建HttpClient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();

        //构建请求对象
        HttpGet httpGet = new HttpGet("https://api.map.baidu.com/geocoding/v3/?address=" +
                "北京市海淀区上地十街10号&output=json&ak=UEBQm9c3KZ5LrsO2C2qsOAs1eSdLvlzM");

        //发送请求
        CloseableHttpResponse response = httpClient.execute(httpGet);
        //输出服务端返回状态码
        System.out.println(response.getStatusLine());

        //获取具体响应信息
        HttpEntity entity = response.getEntity();
        //解析
        String body = EntityUtils.toString(entity);
        System.out.println(body);

        //关闭资源
        response.close();
        httpClient.close();
    }
}
```

### post

```java
//通过HttpClient发送post方式请求
@Test
public void testHttpClientPost() throws IOException {
    //创建HttpClient对象
    CloseableHttpClient httpClient = HttpClients.createDefault();

    //构建请求对象
    HttpPost httpPost = new HttpPost("https://api.map.baidu.com/geocoding/v3/?address=" +
                                     "北京市海淀区上地十街10号&output=json&ak=UEBQm9c3KZ5LrsO2C2qsOAs1eSdLvlzM");

    //注意 post 方式 若需 传递 json 格式数据
    JSONObject jsonObject = new JSONObject();
    jsonObject.put("username", "balance");
    jsonObject.put("password", "123456");

    StringEntity stringEntity = new StringEntity(jsonObject.toString());

    //设置请求的编码方式
    stringEntity.setContentEncoding("UTF-8");
    stringEntity.setContentType("application/json");

    httpPost.setEntity(stringEntity);

    //发送请求
    CloseableHttpResponse response = httpClient.execute(httpPost);
    //输出服务端返回状态码
    System.out.println(response.getStatusLine());

    //获取具体响应信息
    HttpEntity entity = response.getEntity();
    //解析
    EntityUtils.toString(entity);

    //关闭资源
    response.close();
    httpClient.close();
}
```

## Spring Cache

SpringCache是一个框架，实现了基于注解的缓存功能，只需要简单地加一个注解，就能实现缓存功能。

Spring Cache 提供了一层抽象，**底层可以切换不同的缓存实现**，例如：

* EHCache

* Caffeine

* Redis

首先引入依赖

```xml
<dependency> 
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-cache</artifactId>  
    <version>2.7.3</version>
</dependency>
```

| 常用注解       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能，通常加在启动类上                           |
| @Cacheable     | 在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中 |
| @CachePut      | 将方法的返回值放到缓存中                                     |
| @CacheEvict    | 将一条或多条数据从缓存中删除                                 |

**Spring Boot会自动配置Redis缓存管理器 (`RedisCacheManager`) 来替代默认的 `ConcurrentMapCacheManager`。通常你不需要手动创建 `RedisCacheManager`，因为Spring Boot会自动配置它。**

导入 `springcache-demo` 项目，作为入门案例

```java
public class UserController {

    @Autowired
    private UserMapper userMapper;


    @PostMapping
    //如果使用spring cache缓存数据，redis中key的命名 cacheNames::key
    @CachePut(cacheNames = "userCache", key = "#user.id")
    public User save(@RequestBody User user){
        userMapper.insert(user);
        return user;
    }

    @DeleteMapping
    @CacheEvict(cacheNames = "userCache", key = "#id")
    public void deleteById(Long id){
        userMapper.deleteById(id);
    }

	@DeleteMapping("/delAll")
    // allEntries = true 代表清除所有缓存以userCache开头的缓存
    @CacheEvict(cacheNames = "userCache", allEntries = true)
    public void deleteAll(){
        userMapper.deleteAll();
    }

    @GetMapping
    @Cacheable(cacheNames = "userCache", key = "#id")
    public User getById(Long id){
        User user = userMapper.getById(id);
        return user;
    }
}
```

至于如何获取参数的值，方法返回值从而拼接缓存名，可以查看源码

![image-20240413131859305](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240413131859305.png)

## 微信支付

需要企业认证，这里采用传智教育。。

> 参考：https://pay.weixin.qq.com/static/product/product_index.shtml

![image-20240414124522692](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240414124522692.png)

![image-20240414125231371](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240414125231371.png)

[JSAPI](https://pay.weixin.qq.com/wiki/doc/apiv3/apis/chapter3_5_1.shtml)[下单](https://pay.weixin.qq.com/wiki/doc/apiv3/apis/chapter3_5_1.shtml)：商户系统调用该接口在微信支付服务后台生成预支付交易单

![image-20240414125615355](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240414125615355.png)

[微信小程序调起支付](https://pay.weixin.qq.com/wiki/doc/apiv3/apis/chapter3_5_4.shtml)：通过JSAPI下单接口获取到发起支付的必要参数prepay_id，然后使用微信支付提供的小程序方法调起小程序支付

![image-20240414125656616](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240414125656616.png)

因为和支付相关，所以要求在调用预下单时的安全性，需要加密！

获取微信支付平台证书、商户私钥文件：

```
apiclient_key.pem
wechatpay_166D96F876F45C7D07CE98952A96EC980368ACFC.pem
```

因为用户支付成功之后，微信会推送支付结果相关信息，这需要我们传递给微信一个可访问的公网IP，目前我们是局域网IP，需要获取临时域名

此处采用内网穿透工具 cpolar 

![image-20240414130935348](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240414130935348.png)

微信支付相关配置项

属性绑定

```java
@Component
@ConfigurationProperties(prefix = "sky.wechat")
@Data
public class WeChatProperties {

    private String appid; //小程序的appid
    private String secret; //小程序的秘钥
    private String mchid; //商户号
    private String mchSerialNo; //商户API证书的证书序列号
    private String privateKeyFilePath; //商户私钥文件
    private String apiV3Key; //证书解密的密钥
    private String weChatPayCertFilePath; //平台证书
    private String notifyUrl; //支付成功的回调地址
    private String refundNotifyUrl; //退款成功的回调地址

}
```

配置文件

```yml
  wechat:
    appid: ${sky.wechat.appid}
    secret: ${sky.wechat.secret}
    mchid: ${sky.wechat.mchid}
    mch-serial-no: ${sky.wechat.mch-serial-no}
    private-key-file-path: ${sky.wechat.private-key-file-path}
    api-v3-key: ${sky.wechat.api-v3-key}
    we-chat-pay-cert-file-path: ${sky.wechat.we-chat-pay-cert-file-path}
    notify-url: ${sky.wechat.notify-url}
    refund-notify-url: ${sky.wechat.refund-notify-url}
```

## WebSocket

WebSocket 是**基于TCP的一种新的网络协议**。

它实现了浏览器与服务器全双工通信—浏览器和服务器只需要完成一次握手，两者之间就可以创建持久性的连接，**并进行双向数据传输。**

HTTP协议和WebSocket协议对比：

* HTTP是短连接

* WebSocket是长连接

* HTTP通信是单向的，基于请求响应模式

* WebSocket支持双向通信

* HTTP和WebSocket底层都是TCP连接

应用场景：视频弹幕、网页聊天、体育实况更新、股票基金报价实时更新

**WebSocket缺点：**

* 服务器长期维护长连接需要一定的成本

* 各个浏览器支持程度不一

* WebSocket 是长连接，受网络限制比较大，需要处理好重连

***结论：WebSocket并不能完全取代HTTP，它只适合在特定的场景下使用***

引入依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

### 入门案例

前端

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>WebSocket Demo</title>
</head>
<body>
    <input id="text" type="text" />
    <button onclick="send()">发送消息</button>
    <button onclick="closeWebSocket()">关闭连接</button>
    <div id="message">
    </div>
</body>
<script type="text/javascript">
    var websocket = null;
    var clientId = Math.random().toString(36).substr(2);

    //判断当前浏览器是否支持WebSocket
    if('WebSocket' in window){
        //连接WebSocket节点
        websocket = new WebSocket("ws://localhost:8080/ws/"+clientId);
    }
    else{
        alert('Not support websocket')
    }

    //连接发生错误的回调方法
    websocket.onerror = function(){
        setMessageInnerHTML("error");
    };

    //连接成功建立的回调方法
    websocket.onopen = function(){
        setMessageInnerHTML("连接成功");
    }

    //接收到消息的回调方法
    websocket.onmessage = function(event){
        setMessageInnerHTML(event.data);
    }

    //连接关闭的回调方法
    websocket.onclose = function(){
        setMessageInnerHTML("close");
    }

    //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
    window.onbeforeunload = function(){
        websocket.close();
    }

    //将消息显示在网页上
    function setMessageInnerHTML(innerHTML){
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }

    //发送消息
    function send(){
        var message = document.getElementById('text').value;
        websocket.send(message);
    }
	
	//关闭连接
    function closeWebSocket() {
        websocket.close();
    }
</script>
</html>
```

后端

准备一个配置类

```java
/**
 * WebSocket配置类，用于注册WebSocket的Bean
 */
@Configuration
public class WebSocketConfiguration {

    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }

}
```

编写websocket具体实现

```java
/**
 * WebSocket服务
 */
@Component
@ServerEndpoint("/ws/{sid}")
public class WebSocketServer {

    //存放会话对象
    private static Map<String, Session> sessionMap = new HashMap();

    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session, @PathParam("sid") String sid) {
        System.out.println("客户端：" + sid + "建立连接");
        sessionMap.put(sid, session);
    }

    /**
     * 收到客户端消息后调用的方法
     *
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, @PathParam("sid") String sid) {
        System.out.println("收到来自客户端：" + sid + "的信息:" + message);
    }

    /**
     * 连接关闭调用的方法
     *
     * @param sid
     */
    @OnClose
    public void onClose(@PathParam("sid") String sid) {
        System.out.println("连接断开:" + sid);
        sessionMap.remove(sid);
    }

    /**
     * 群发
     *
     * @param message
     */
    public void sendToAllClient(String message) {
        Collection<Session> sessions = sessionMap.values();
        for (Session session : sessions) {
            try {
                //服务器向客户端发送消息
                session.getBasicRemote().sendText(message);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

}
```

写一个定时任务类测试一下

```java
@Component
public class WebSocketTask {
    @Autowired
    private WebSocketServer webSocketServer;

    /**
     * 通过WebSocket每隔5秒向客户端发送消息
     */
    @Scheduled(cron = "0/5 * * * * ?")
    public void sendMessageToClient() {
        webSocketServer.sendToAllClient("这是来自服务端的消息：" + DateTimeFormatter.ofPattern("HH:mm:ss").format(LocalDateTime.now()));
    }
}
```

## Apache POI

Apache POI 是一个处理Miscrosoft Office各种文件格式的开源项目。简单来说就是，我们可以使用 POI 在 Java 程序中对Miscrosoft Office各种文件进行读写操作。

一般情况下，**POI 都是用于操作 Excel 文件。**

Apache POI 的应用场景：

* 银行网银系统导出交易明细

* 各种业务系统导出Excel报表

* 批量导入业务数据

### 入门案例

首先导入maven坐标 两个包

```xml
<dependency>
    <groupId>org.apache.poi</groupId>   
    <artifactId>poi</artifactId>    
    <version>3.16</version>
</dependency>
<dependency>   
    <groupId>org.apache.poi</groupId>   
    <artifactId>poi-ooxml</artifactId>   
    <version>3.16</version>
</dependency>
```

**写入excel**

```java
public class ApachePOITest {
    @Test
    public void test() throws IOException {
        //先创建一个excel
        XSSFWorkbook excel = new XSSFWorkbook();

        //新建一个sheet页
        XSSFSheet balanceTest = excel.createSheet("balanceTest");

        //在sheet页中创建行 注意 row number从0开始
        XSSFRow row = balanceTest.createRow(0);

        //在行上创建单元格 注意 cell number从0开始
        row.createCell(0).setCellValue("name");
        row.createCell(1).setCellValue("sex");

        XSSFRow row1 = balanceTest.createRow(1);
        row1.createCell(0).setCellValue("balance");
        row1.createCell(1).setCellValue("man");

        //当前操作都是在内存中 写入磁盘中
        FileOutputStream fileOutputStream = new FileOutputStream("D:\\balance.xlsx");
        excel.write(fileOutputStream);

        //关闭资源
        fileOutputStream.close();
        excel.close();
    }
}
```

**读取excel**

```java
public void readTest() throws IOException {
    // 创建文件输入流
    FileInputStream fileInputStream = new FileInputStream("D:\\balance.xlsx");
    // 从输入流中得到对应 excel 文件
    XSSFWorkbook excel = new XSSFWorkbook(fileInputStream);

    //通过sheet页名字 获取指定 sheet 页
    XSSFSheet sheet = excel.getSheet("balanceTest");

    //获得最后一行有文字的行号 从0开始
    int lastRowNum = sheet.getLastRowNum();
    //获得第一行有文字的行号 从0开始
    int firstRowNum = sheet.getFirstRowNum();
    for (int i = firstRowNum; i <=lastRowNum; i++) {
        //根据行号获得具体行
        XSSFRow row = sheet.getRow(i);
        //获得行中有文字的单元列 起始列号
        short firstCellNum = row.getFirstCellNum();
        //获得行中有文字的单元列+1 终止列号   注意！！！！！！！
        short lastCellNum = row.getLastCellNum();
        for (int j = firstCellNum; j < lastCellNum ; j++) {
            //获得字符串形式的 单元格值
            String cellValue = row.getCell(j).getStringCellValue();
            System.out.print(cellValue+" ");
        }
        System.out.println();
    }

    //关闭资源
    fileInputStream.close();
    excel.close();
}
```

## 地图工具-百度

### 环境准备

登录百度地图开放平台：https://lbsyun.baidu.com/
