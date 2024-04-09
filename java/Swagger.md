# Swagger

- 号称世界上最流行的API框架
- Restful Api 文档在线自动生成器 => **API 文档 与API 定义同步更新**
- 直接运行，在线测试API
- 支持多种语言 （如：Java，PHP等）

## SpringBoot集成Swagger

新建一个SpringBoot项目，web依赖

添加Swagger的依赖

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

要使用Swagger还需要编写它对应的配置类

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

}
```

访问测试：http://localhost:8080/swagger-ui/index.html

进入Swagger界面

![image-20240323210429577](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403232104925.png)

注意有报错

在配置文件中加上

```
Failed to start bean 'documentationPluginsBootstrapper'; nested exception is java.lang.NullPointerException
```

```properties
spring.mvc.pathmatch.matching-strategy=ant_path_matcher
# 这段配置代码是针对Spring MVC框架的配置。具体来说，它设置了路径匹配的策略为ant_path_matcher，这意味着在Spring MVC中使用的路径匹配策略是Ant风格的路径匹配器。

#在Spring MVC中，路径匹配策略决定了如何匹配请求的URL路径与控制器中的请求映射方法。Ant路径匹配器使用类似于Ant风格的通配符来进行路径匹配，例如/**表示匹配任意路径，?表示匹配一个字符，*表示匹配零个或多个字符等。
```

或者在启动类上面加

```java
@EnableWebMvc
@SpringBootApplication
public class SwaggerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SwaggerApplication.class, args);
    }

}
/*
@EnableWebMvc 是一个注解，用于启用Spring MVC框架的基于注解的配置。当你在Spring Boot应用程序中使用Spring MVC时，通常不需要显式地配置@EnableWebMvc，因为Spring Boot会自动配置Spring MVC。

但是，在某些情况下，你可能想要自定义Spring MVC配置，例如添加自定义的拦截器、消息转换器、视图解析器等。这时，你可以创建一个配置类，并在该类上使用@EnableWebMvc注解，以启用Spring MVC的完整功能，并提供自定义配置。
*/
```

## 基本信息配置

注入Docket的bean实例（Swagger的实例是Docket）

```
@Bean
public Docket getDocket(){
    //通过该类的apiInfo方法来修改默认的配置信息
    return new Docket(DocumentationType.SWAGGER_2);
}
```

![image-20240323213423020](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403232134220.png)

可以看到内部是通过一个构造方法来设置，需要传入一个DocumentationType，点进去看看

![image-20240323213523991](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403232135990.png)

可以看到一共有三种方式，这里我们选择swagger2

因为

```java
this.apiInfo = ApiInfo.DEFAULT;
```

我们可以修改此处，点击查看  ApiInfo 类

![image-20240323213729262](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403232137320.png)

至此就可以着手修改了

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket getDocket(){
        Contact contact = new Contact("balance", "balacne.wiki", "18356765603@qq.com");
        //通过该类的apiInfo方法来修改默认的配置信息
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(new ApiInfo(
                "Balance的Swagger",
                "这是所有的接口信息",
                "v1.0",
                "baidu.com",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList<>()));
    }
}
```

![image-20240323214457710](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403232144855.png)

## 接口扫描以及环境切换

> 指定要扫描的接口

- 构造Docket类实例时通过调用select方法配置
- **之后通过调用`apis()` 方法**

```java
@Bean
public Docket getDocket(){
    Contact contact = new Contact("balance", "balacne.wiki", "18356765603@qq.com");
    //通过该类的apiInfo方法来修改默认的配置信息
    return new Docket(DocumentationType.SWAGGER_2).apiInfo(new ApiInfo(
        "Balance的Swagger",
        "这是所有的接口信息",
        "v1.0",
        "baidu.com",
        contact,
        "Apache 2.0",
        "http://www.apache.org/licenses/LICENSE-2.0",
        new ArrayList<>()))
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.balance.controller"))
        .build();
}
```

![image-20240324100820521](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403241008435.png)

* **可以接着给出paths方法指定过滤哪些接口**

![image-20240324101238655](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403241012777.png)

> 环境切换

我们如何设置条件来让 swagger 开启呢？可想而知，在正式使用的时候不应该出现有关接口的详细信息

首先，关闭 swagger 可以通过 enable 方法

![image-20240324101538202](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403241015143.png)

这时候一个最简单的想法就是来一个flag，我们去判断当前所处环境，如果是开发和测试环境，就开启，否则就关闭

首先给出开发和生产环境的配置文件

![image-20240324101914481](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403241019432.png)

设置当前为生产环境  application.properties

```properties
spring.profiles.active=dev
# Spring Boot 使用不同的配置文件来支持不同的环境，其中的一种常见方式是基于 application-{profile}.properties 或 application-{profile}.yml 文件的命名规范，其中 {profile} 是活动的配置文件名。

# 对于您提到的 spring.profiles.active=dev，这意味着您的应用程序将使用名为 application-dev.properties 或 application-dev.yml 的配置文件来配置 dev 环境。
```

在配置类中增加读取配置文件代码

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket getDocket(Environment environment) {//注入环境变量
        Profiles profiles = Profiles.of("dev","test");//设置仅在 dev 和 test 环境中起效
        boolean flag = environment.acceptsProfiles(profiles);//环境变量去监听当前环境，返回布尔值，控制是否开启
        
        Contact contact = new Contact("balance", "balacne.wiki", "18356765603@qq.com");
        //通过该类的apiInfo方法来修改默认的配置信息
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(new ApiInfo(
                "Balance的Swagger",
                "这是所有的接口信息",
                "v1.0",
                "baidu.com",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList<>()))
                .enable(flag)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.balance.controller"))
                .build();
    }
}
```

## 配置分组

- 构造Docket类实例时通过调用groupName方法配置

```java
@Bean
public Docket docket1(){
  return new Docket(DocumentationType.SWAGGER_2).groupName("A");
}

@Bean
public Docket docket2(){
  return new Docket(DocumentationType.SWAGGER_2).groupName("B");
}

@Bean
public Docket docket3(){
  return new Docket(DocumentationType.SWAGGER_2).groupName("C");
}
```

通过实例化多个Docket，然后给定 groupName 就可以完成分组实现

![image-20240324105223834](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202403241052884.png)

## 注释

| Swagger注解                                            | 说明                                                 |
| ------------------------------------------------------ | ---------------------------------------------------- |
| @Api(tags = “xxx模块说明”)                             | 作用在模块类上                                       |
| @ApiOperation(“xxx接口说明”)                           | 作用在接口方法上                                     |
| @ApiModel(“xxxPOJO说明”)                               | 一般作用在实体类上                                   |
| @ApiModelProperty(value = “xxx属性说明”,hidden = true) | 作用在类方法和属性上，hidden设置为true可以隐藏该属性 |
| @ApiParam(“xxx参数说明”)                               | 作用在参数、方法和字段上，类似@ApiModelProperty      |

