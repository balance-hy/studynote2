# SpringBoot

## 什么是Spring

Spring是一个开源框架，2003 年兴起的一个轻量级的Java 开发框架，作者：Rod Johnson 。

**Spring是为了解决企业级应用开发的复杂性而创建的，简化开发。**

## Spring如何简化java开发

为了降低Java开发的复杂性，Spring采用了以下4种关键策略：

1、基于POJO的轻量级和最小侵入性编程，所有东西都是bean；

2、通过IOC，依赖注入（DI）和面向接口实现松耦合；

3、基于切面（AOP）和惯例进行声明式编程；

4、通过切面和模版减少样式代码，RedisTemplate，xxxTemplate；

## 什么是SpringBoot

学过javaweb的同学就知道，开发一个web应用，从最初开始接触Servlet结合Tomcat, 跑出一个Hello Wolrld程序，是要经历特别多的步骤；后来就用了框架Struts，再后来是SpringMVC，到了现在的SpringBoot，过一两年又会有其他web框架出现；你们有经历过框架不断的演进，然后自己开发项目所有的技术也在不断的变化、改造吗？建议都可以去经历一遍；

言归正传，什么是SpringBoot呢，就是一个javaweb的开发框架，和SpringMVC类似，对比其他javaweb框架的好处，官方说是简化开发，约定大于配置， you can "just run"，能迅速的开发web应用，几行代码开发一个http接口。

所有的技术框架的发展似乎都遵循了一条主线规律：从一个复杂应用场景 衍生 一种规范框架，人们只需要进行各种配置而不需要自己去实现它，这时候强大的配置功能成了优点；发展到一定程度之后，人们根据实际生产应用情况，选取其中实用功能和设计精华，重构出一些轻量级的框架；之后为了提高开发效率，嫌弃原先的各类配置过于麻烦，于是开始提倡“约定大于配置”，进而衍生出一些一站式的解决方案。

是的这就是Java企业级应用->J2EE->spring->springboot的过程。

随着 Spring 不断的发展，涉及的领域越来越多，项目整合开发需要配合各种各样的文件，慢慢变得不那么易用简单，违背了最初的理念，甚至人称配置地狱。Spring Boot 正是在这样的一个背景下被抽象出来的开发框架，目的为了让大家更容易的使用 Spring 、更容易的集成各种常用的中间件、开源软件；

Spring Boot 基于 Spring 开发，Spirng Boot 本身并不提供 Spring 框架的核心特性以及扩展功能，只是用于快速、敏捷地开发新一代基于 Spring 框架的应用程序。也就是说，它并不是用来替代 Spring 的解决方案，而是和 Spring 框架紧密结合用于提升 Spring 开发者体验的工具。Spring Boot 以**约定大于配置的核心思想**，默认帮我们进行了很多设置，**多数 Spring Boot 应用只需要很少的 Spring 配置。同时它集成了大量常用的第三方库配置（例如 Redis、MongoDB、Jpa、RabbitMQ、Quartz 等等**），Spring Boot 应用中这些第三方库几乎可以零配置的开箱即用。

简单来说就是SpringBoot其实不是什么新的框架，它默认配置了很多框架的使用方式，就像maven整合了所有的jar包，spring boot整合了所有的框架 。

Spring Boot 出生名门，从一开始就站在一个比较高的起点，又经过这几年的发展，生态足够完善，Spring Boot 已经当之无愧成为 Java 领域最热门的技术。

**Spring Boot的主要优点：**

- 为所有Spring开发者更快的入门
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

## 第一个SpringBoot程序

注意高版本的SpringBoot（3.0以上）不再支持选择java 8。

两种方式解决：

* 通过修改服务器URL解决 (推荐)，在当前页面将 https://start.spring.io 替换为阿里的 https://start.aliyun.com，如截图所示：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fb6d229d15d64954bccdf612fd18f5d8.png)

* 通过代码解决

  直接随便选一个版本，创建完成后到pom.xml中修改springboot版本和java版本

  ```xml
  <parent>
  	<groupId> org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-parent</artifactId>
  	<version> 3.0以下 </version>
  </parent>
  
  <properties>
  	<!-- 修改java版本 -->
  	<java.version>1.8</java.version>
  </properties> 
  ```

Spring官方提供了非常方便的工具让我们快速构建应用

### **项目创建方式一：**spring

使用Spring Initializr 的 Web页面创建项目

1、打开 https://start.spring.io/

2、填写项目信息

3、点击”Generate Project“按钮生成项目；下载此项目

4、解压项目包，并用IDEA以Maven项目导入，一路下一步即可，直到项目导入完毕。

5、如果是第一次使用，可能速度会比较慢，包比较多、需要耐心等待一切就绪。

**项目结构分析：**

通过上面步骤完成了基础项目的创建。就会自动生成以下文件。

1、程序的主启动类

2、一个 application.properties 配置文件

3、一个 测试类

4、一个 pom.xml

**pom.xml文件分析**

打开pom.xml，看看Spring Boot项目的依赖：

**可以看到有一个parent项目，然后依赖中含有web依赖和测试相关（可以换成junit）,然后有一个打包所用插件**

```xml
<!-- 父依赖 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath/>
</parent>

<dependencies>
    <!-- web场景启动器 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- springboot单元测试 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <!-- 剔除依赖 -->
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>

<build>
    <plugins>
        <!-- 打包插件 -->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### 项目创建方式二：idea

![image-20240314140358509](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314140358509.png)

**删除package后面多余项和不删除区别如下**

![image-20240314140517691](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314140517691.png)



**注意demos文件夹是演示代码可以删除**

新建一个controller包新建hellocontroller

![image-20240314141339942](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314141339942.png)

测试

![image-20240314141411661](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314141411661.png)

可以修改启动端口，直接在application.properties中修改即可

```properties
# 应用服务 WEB 访问端口
server.port=8080
```

### 如何打包

![image-20240314135859808](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314135859808.png)

**可以配置打包时 跳过项目运行测试用例**

```xml
<!--
    在工作中,很多情况下我们打包是不想执行测试用例的
    可能是测试用例不完事,或是测试用例会影响数据库数据
    跳过测试用例执
    -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <!--跳过项目运行测试用例-->
        <skipTests>true</skipTests>
    </configuration>
</plugin>
```

**如果打包成功，则会在target目录下生成一个 jar 包,打成了jar包后，就可以在任何地方运行了**

运行命令如下

```
java -jar 你的jar包
```

**如果显示 xxxx.jar中没有主清单属性**

查找之后发现阿里云生成的pom.xml文件默认跳过了主类，删除后成功运行

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>${spring-boot.version}</version>
-            <configuration>
-                <mainClass>com.balance.Springboot01HelloApplication</mainClass>
-                <skip>true</skip>
            </configuration>
            <executions>
                <execution>
                    <id>repackage</id>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### 设置banner

将banner.txt放在resources目录下即可

## SpringBoot原理初探

我们之前写的HelloSpringBoot，到底是怎么运行的呢，Maven项目，我们一般从pom.xml文件探究起；

### 父依赖

其中它主要是依赖一个父项目，主要是管理项目的资源过滤及插件！

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

点进去，发现还有一个父依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

**以后我们导入依赖默认是不需要写版本；但是如果导入的包没有在依赖中管理着就需要手动配置版本了**

### 启动器 spring-boot-starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**springboot-boot-starter-xxx**：就是spring-boot的场景启动器

**spring-boot-starter-web**：帮我们导入了web模块正常运行所依赖的组件；

SpringBoot将所有的功能场景都抽取出来，做成一个个的starter （启动器），只需要在项目中引入这些starter即可，所有相关的依赖都会导入进来 ， 我们要用什么功能就导入什么样的场景启动器即可 ；我们未来也可以自己自定义 starter；

分析完了 pom.xml 来看看这个启动类

```java
//@SpringBootApplication 来标注一个主程序类
//说明这是一个Spring Boot应用
@SpringBootApplication
public class SpringbootApplication {

   public static void main(String[] args) {
     //以为是启动了一个方法，没想到启动了一个服务
      SpringApplication.run(SpringbootApplication.class, args);
   }

}
```

#### @SpringBootApplication

作用：标注在某个类上说明这个类是SpringBoot的主配置类 ， SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

进入这个注解：可以看到上面还有很多其他注解！

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    // ......
}
```

##### @ComponentScan

这个注解在Spring中很重要 ,它对应XML配置中的元素。默认扫描当前包及其子包

作用：自动扫描并加载符合条件的组件或者bean ， 将这个bean定义加载到IOC容器中

##### @SpringBootConfiguration

作用：SpringBoot的配置类 ，标注在某个类上 ， 表示这是一个SpringBoot的配置类；

我们继续进去这个注解查看

```java
// 点进去得到下面的 @Component
@Configuration
public @interface SpringBootConfiguration {}

@Component
public @interface Configuration {}
```

这里的 @Configuration，说明这是一个配置类 ，配置类就是对应Spring的xml 配置文件；

里面的 @Component 这就说明，启动类本身也是Spring中的一个组件而已，负责启动应用！

我们回到 SpringBootApplication 注解中继续看。

##### @EnableAutoConfiguration

```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

**开启自动配置功能**

以前我们需要自己配置的东西，而现在SpringBoot可以自动帮我们配置 ；@EnableAutoConfiguration告诉SpringBoot开启自动配置功能，这样自动配置才能生效；

点进注解继续查看：

###### **@AutoConfigurationPackage**

**自动配置包**

```java
@Import({AutoConfigurationPackages.Registrar.class})
public @interface AutoConfigurationPackage {
}
```

**@import** ：Spring底层注解@import ， 给容器中导入一个组件

Registrar.class 作用：将主启动类的所在包及包下面所有子包里面的所有组件扫描到Spring容器 ；

###### @Import({AutoConfigurationImportSelector.class})

AutoConfigurationImportSelector ：自动配置导入选择器，那么它会导入哪些组件的选择器呢？我们点击去这个类看源码：

1、这个类中有一个这样的方法

```java
// 获得候选的配置
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    //这里的getSpringFactoriesLoaderFactoryClass（）方法
    //返回的就是我们最开始看的启动自动导入配置文件的注解类；EnableAutoConfiguration
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

2、这个方法又调用了 SpringFactoriesLoader 类的静态方法！我们进入SpringFactoriesLoader类loadFactoryNames() 方法

```java
public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
    String factoryClassName = factoryClass.getName();
    //这里它又调用了 loadSpringFactories 方法
    return (List)loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
}
```

3、我们继续点击查看 loadSpringFactories 方法

```java
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    //获得classLoader ， 我们返回可以看到这里得到的就是EnableAutoConfiguration标注的类本身
    MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
    if (result != null) {
        return result;
    } else {
        try {
            //去获取一个资源 "META-INF/spring.factories"
            Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
            LinkedMultiValueMap result = new LinkedMultiValueMap();

            //将读取到的资源遍历，封装成为一个Properties
            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                Iterator var6 = properties.entrySet().iterator();

                while(var6.hasNext()) {
                    Entry<?, ?> entry = (Entry)var6.next();
                    String factoryClassName = ((String)entry.getKey()).trim();
                    String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                    int var10 = var9.length;

                    for(int var11 = 0; var11 < var10; ++var11) {
                        String factoryName = var9[var11];
                        result.add(factoryClassName, factoryName.trim());
                    }
                }
            }

            cache.put(classLoader, result);
            return result;
        } catch (IOException var13) {
            throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
        }
    }
}
```

**4、发现一个多次出现的文件：spring.factories，全局搜索它**

### spring.factories

我们根据源头打开spring.factories ， 看到了很多自动配置的文件；这就是自动配置根源所在！

![image-20240314153427813](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314153427813.png)

**WebMvcAutoConfiguration**

我们在上面的自动配置类随便找一个打开看看，比如 ：WebMvcAutoConfiguration

![image-20240314153550658](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314153550658.png)

可以看到这些一个个的都是JavaConfig配置类，而且都注入了一些Bean，**注意看到上面有@ConditionOnClass的注解，只有类似这样的注解条件满足才会自动导入该配置类**

所以，自动配置真正实现是**从classpath中搜寻所有的META-INF/spring.factories配置文件** ，并将其中对应的 org.springframework.boot.autoconfigure 包下的配置项，**通过反射实例化为对应标注了 @Configuration的JavaConfig形式的IOC容器配置类** ， 然后将这些都**汇总成为一个实例并加载到IOC容器**中。

**结论：**

1. SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
2. 将这些值作为自动配置类导入容器 ， 自动配置类就生效 ， 帮我们进行自动配置工作；
3. 整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中；
4. 它会给容器中导入非常多的自动配置类 （xxxAutoConfiguration）, 就是给容器中导入这个场景需要的所有组件 ， 并配置好这些组件 ；
5. 有了自动配置类 ， 免去了我们手动编写配置注入功能组件等的工作；

### 不简单的run方法

我最初以为就是运行了一个main方法，没想到却开启了一个服务；

```java
@SpringBootApplication
public class Springboot01HelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot01HelloApplication.class, args);
    }

}
```

**SpringApplication.run分析**

分析该方法主要分两部分，一部分是SpringApplication的实例化，二是run方法的执行；

#### SpringApplication

**这个类主要做了以下四件事情：**

1、推断应用的类型是普通的项目还是Web项目

2、查找并加载所有可用初始化器 ， 设置到initializers属性中

3、找出所有的应用程序监听器，设置到listeners属性中

4、推断并设置main方法的定义类，找到运行的主类

查看构造器：

```java
public SpringApplication(ResourceLoader resourceLoader, Class... primarySources) {
    // ......
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    this.setInitializers(this.getSpringFactoriesInstances();
    this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
    this.mainApplicationClass = this.deduceMainApplicationClass();
}
```

#### run方法流程分析

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412221135119-1843322351.png)

## yaml

yaml语法学习

### 3.1、配置文件

SpringBoot使用一个全局的配置文件 ， 配置文件名称是固定的

- application.properties

  语法结构 ：key=value

- application.yml（或者写成application.yaml，没有区别）

  语法结构 ：key：空格value

**配置文件的作用 ：**修改SpringBoot自动配置的默认值，因为SpringBoot在底层都给我们自动配置好了；

比如我们可以在配置文件中修改Tomcat 默认启动的端口号！测试一下！

```ini
server.port=8081
```

### yaml 概述

YAML是 "YAML Ain't a Markup Language" （YAML不是一种标记语言）的递归缩写。

在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）

**其实就是为了强调这种语言以数据作为中心，而不是以标记语言为重点！**

以前的配置文件，大多数都是使用xml来配置；比如一个简单的端口配置，我们来对比下yaml和xml

传统xml配置：

```xml
<server>
    <port>8081<port>
</server>
```

yaml配置：

```yml
server：
  prot: 8080
```

### 基础语法

说明：语法要求严格！

1、空格不能省略

2、以缩进来控制层级关系，只要是左边对齐的一列数据都是同一个层级的。

3、属性和值的大小写都是十分敏感的。

**字面量：普通的值 [ 数字，布尔值，字符串 ]**

字面量直接写在后面就可以 ， **字符串默认不用加上双引号或者单引号；**

```yml
k: v
```

注意：

- “ ” 双引号，不会转义字符串里面的特殊字符 ， 特殊字符会作为本身想表示的意思；

  比如 ：name: "kuang \n shen" 输出 ：kuang 换行 shen

- '' 单引号，会转义特殊字符 ， 特殊字符最终会变成和普通字符一样输出

  比如 ：name: ‘kuang \n shen’ 输出 ：kuang \n shen

#### **对象、Map（键值对）**

```yml
#对象、Map格式
k: 
    v1:
    v2:
```

在下一行来写对象的属性和值的关系，注意缩进；比如：

```yml
student:
    name: qinjiang
    age: 3
```

行内写法

```yml
student: {name: qinjiang,age: 3}
```

#### **数组（ List、set ）**

用 - 值表示数组中的一个元素,比如：

```yml
pets:
 - cat
 - dog
 - pig
```

行内写法

```yml
pets: [cat,dog,pig]
```

#### **修改SpringBoot的默认端口号**

配置文件中添加，端口号的参数，就可以切换端口；

```yml
server:  
  port: 8082
```

注入配置文件

**yaml文件更强大的地方在于，它可以给我们的实体类直接注入匹配值！**

### yaml注入配置文件

1、在springboot项目中的resources目录下新建一个文件 application.yml

2、编写一个实体类 Dog；

```java
@Component  //注册bean到容器中
public class Dog {
    private String name;
    private Integer age;
    
    //有参无参构造、get、set方法、toString()方法  
}
```

3、思考，我们原来是如何给bean注入属性值的！@Value，给狗狗类测试一下：

```java
@Component //注册bean
public class Dog {
    @Value("旺财")
    private String name;
    @Value("18")
    private Integer age;
}
```

4、在SpringBoot的测试类下注入狗狗输出一下；

```java
@SpringBootTest
class DemoApplicationTests {

    @Autowired //将狗狗自动注入进来
    Dog dog;

    @Test
    public void contextLoads() {
        System.out.println(dog); //打印看下狗狗对象
    }

}
```

结果成功输出，@Value注入成功，这是我们原来的办法。

![image-20240314162213372](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314162213372.png)

5、我们在编写一个复杂一点的实体类：Person 类

```java
@Component //注册bean到容器中
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
    
    //有参无参构造、get、set方法、toString()方法  
}
```

6、我们来使用yaml配置的方式进行注入，大家写的时候注意区别和优势，我们编写一个yaml配置！

```yml
person:
  name: qinjiang
  age: 3
  happy: false
  birth: 2000/01/01
  maps: {k1: v1,k2: v2}
  lists:
   - code
   - girl
   - music
  dog:
    name: 旺财
    age: 1
```

7、我们刚才已经把person这个对象的所有值都写好了，我们现在来注入到我们的类中！

```java
/*
@ConfigurationProperties作用：
将配置文件中配置的每一个属性的值，映射到这个组件中；
告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
参数 prefix = “person” : 将配置文件中的person下面的所有属性一一对应
*/
@Component //注册bean
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
}
```

8、确认以上配置都OK之后，我们去测试类中测试一下：

```java
@SpringBootTest
class DemoApplicationTests {

    @Autowired
    Person person; //将person自动注入进来

    @Test
    public void contextLoads() {
        System.out.println(person); //打印person信息
    }

}
```

结果：所有值全部注入成功！

![image-20240314162626669](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240314162626669.png)

### 加载指定的配置文件

**@PropertySource ：**加载指定的配置文件；

**@configurationProperties**：默认从全局配置文件中获取值；

1、我们去在resources目录下新建一个**person.properties**文件

```properties
name=kuangshen
```

2、然后在我们的代码中指定加载person.properties文件

**注意此处取是以 `${}` 形式取**

```java
@PropertySource(value = "classpath:person.properties")
@Component //注册bean
public class Person {

    @Value("${name}")
    private String name;

    ......  
}
```

3、再次输出测试一下：指定配置文件绑定成功！

### 配置文件占位符

配置文件还可以编写占位符生成随机数

```yml
person:
    name: qinjiang${random.uuid} # 随机uuid
    age: ${random.int}  # 随机int
    happy: false
    birth: 2000/01/01
    maps: {k1: v1,k2: v2}
    lists:
      - code
      - girl
      - music
    dog:
      name: ${person.hello:other}_旺财
      age: 1
```

`name: ${person.hello:other}`这一行的意思是如果 person.hello有值则值为person.hello 否则为 other

### 回顾properties配置

我们上面采用的yaml方法都是最简单的方式，开发中最常用的；也是springboot所推荐的！那我们来唠唠其他的实现方式，道理都是相同的；写还是那样写；配置文件除了yml还有我们之前常用的properties ， 我们没有讲，我们来唠唠！

【注意】properties配置文件在写中文的时候，会有乱码 ， 我们需要去IDEA中设置编码格式为UTF-8；settings-->FileEncodings 中配置；

**测试步骤：**

1、新建一个实体类User

```java
@Component //注册bean
public class User {
    private String name;
    private int age;
    private String sex;
}
```

2、编辑配置文件 user.properties

```properties
user1.name=kuangshen
user1.age=18user1.sex=男
```

3、我们在User类上使用@Value来进行注入！

```java
@Component //注册bean
@PropertySource(value = "classpath:user.properties")
public class User {
    //直接使用@value
    @Value("${user.name}") //从配置文件中取值
    private String name;
    @Value("#{9*2}")  // #{SPEL} Spring EL表达式
    private int age;
    @Value("男")  // 字面量
    private String sex;
}
```

4、Springboot测试

```ini
user1.name=kuangshen
user1.age=18
user1.sex=男
```

测试！

### 对比 

@Value 使用起来并不友好！我们需要为每个属性单独注解赋值，比较麻烦；我们来看个功能对比图

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412221528114-1258238964.png)

1、**@ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加**

2、松散绑定：这个什么意思呢? 比如我的**yml中写的last-name，这个和lastName是一样的， 后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下**

3、JSR303数据校验 ， 这个就是我们**可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性**

4、复杂类型封装，**yml中可以封装对象 ， 使用value就不支持**

**结论：**

配置yml和配置properties都可以获取到值 ， 强烈推荐 yml；

如果我们在某个业务中，只需要获取配置文件中的某个值，可以使用一下 @value；

如果说，我们专门编写了一个JavaBean来和配置文件进行一一映射，就直接@configurationProperties，不要犹豫！

### JSR303校验

#### 使用示例

Springboot中可以用@validated来校验数据，如果数据异常则会统一抛出异常，方便异常中心统一处理。我们这里来写个注解让我们的name只能支持Email格式；

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {
    @Email
    private String name;
    
    
@SpringBootTest
class Springboot01HelloApplicationTests {
    @Autowired
    private Person person;

    @Test
    void contextLoads() {
        System.out.println(person);
    }

}
```

运行结果 ：default message [不是一个合法的电子邮件地址];

若注解报错，需要导包

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

#### 常见参数

```java
@NotNull(message="名字不能为空")
private String userName;
@Max(value=120,message="年龄最大不能查过120")
private int age;
@Email(message="邮箱格式错误")
private String email;

空检查
@Null       验证对象是否为null
@NotNull    验证对象是否不为null, 无法查检长度为0的字符串
@NotBlank   检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
@NotEmpty   检查约束元素是否为NULL或者是EMPTY.
    
Booelan检查
@AssertTrue     验证 Boolean 对象是否为 true  
@AssertFalse    验证 Boolean 对象是否为 false  
    
长度检查
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内  
@Length(min=, max=) string is between min and max included.

日期检查
@Past       验证 Date 和 Calendar 对象是否在当前时间之前  
@Future     验证 Date 和 Calendar 对象是否在当前时间之后  
@Pattern    验证 String 对象是否符合正则表达式的规则

.......等等
除此以外，我们还可以自定义一些数据校验规则
```

其实用 Pattern 写正则表达式就可以完成校验

### 多环境配置

> 工作中常常需要写多个环境

我们在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml , 用来指定多个环境版本；

**例如：**

application-test.properties 代表测试环境配置

application-dev.properties 代表开发环境配置

但是Springboot并不会直接启动这些配置文件，它**默认使用application.properties主配置文件**；

我们需要通过一个配置来选择需要激活的环境：

```properties
#比如在配置文件中指定使用dev环境，我们可以通过设置不同的端口号进行测试；
#我们启动SpringBoot，就可以看到已经切换到dev下的配置了；
spring.profiles.active=dev
```

#### yaml的多环境切换

和properties配置文件中一样，但是使用yml去实现不需要创建多个配置文件，更加方便了 !

```yml
server:
  port: 8081
#选择要激活那个环境块
spring:
  profiles:
    active: prod

---
server:
  port: 8083
spring:
  profiles: dev #配置环境的名称


---

server:
  port: 8084
spring:
  profiles: prod  #配置环境的名称
```

**注意：如果yml和properties同时都配置了端口，并且没有激活其他环境 ， 默认会使用properties配置文件的！**

#### 配置文件加载位置及优先级

**外部加载配置文件的方式十分多，我们选择最常用的即可，在开发的资源文件中进行配置！**

官方外部配置文件说明参考文档

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412221627864-281289739.png)

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件：

```lua
优先级1：项目路径下的config文件夹配置文件
优先级2：项目路径下配置文件
优先级3：资源路径下的config文件夹配置文件
优先级4：资源路径下配置文件
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

**SpringBoot会从这四个位置全部加载主配置文件；互补配置；**

![image-20240320130532426](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320130532426.png)

#### 拓展，运维小技巧

指定位置加载配置文件

我们还可以通过spring.config.location来改变默认的配置文件位置

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；

这种情况，一般是后期运维做的多，**相同配置，外部指定的配置文件优先级最高**

## SpringBoot自动配置原理

自动配置原理

配置文件到底能写什么？怎么写？

SpringBoot官方文档中有大量的配置，我们无法全部记住

### 分析自动配置原理

我们以**HttpEncodingAutoConfiguration（Http编码自动配置）**为例解释自动配置原理；

```java
//表示这是一个配置类，和以前编写的配置文件一样，也可以给容器中添加组件；
@Configuration 

//启动指定类的ConfigurationProperties功能；
  //进入这个HttpProperties查看，将配置文件中对应的值和HttpProperties绑定起来；
  //并把HttpProperties加入到ioc容器中
@EnableConfigurationProperties({HttpProperties.class}) 

//Spring底层@Conditional注解
  //根据不同的条件判断，如果满足指定的条件，整个配置类里面的配置就会生效；
  //这里的意思就是判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnWebApplication(
    type = Type.SERVLET
)

//判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnClass({CharacterEncodingFilter.class})

//判断配置文件中是否存在某个配置：spring.http.encoding.enabled；
  //如果不存在，判断也是成立的
  //即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
@ConditionalOnProperty(
    prefix = "spring.http.encoding",
    value = {"enabled"},
    matchIfMissing = true
)

public class HttpEncodingAutoConfiguration {
    //他已经和SpringBoot的配置文件映射了
    private final Encoding properties;
    //只有一个有参构造器的情况下，参数的值就会从容器中拿
    public HttpEncodingAutoConfiguration(HttpProperties properties) {
        this.properties = properties.getEncoding();
    }
    
    //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    @Bean
    @ConditionalOnMissingBean //判断容器没有这个组件？
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.RESPONSE));
        return filter;
    }
    //。。。。。。。
}
```

**一句话总结 ：根据当前不同的条件判断，决定这个配置类是否生效！**

- 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
- 这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；
- 配置文件能配置什么就可以参照某个功能对应的这个属性类

```java
//从配置文件中获取指定的值和bean的属性进行绑定
@ConfigurationProperties(prefix = "spring.http") 
public class HttpProperties {
    // .....
}
```

### 精髓

1、SpringBoot启动会加载大量的自动配置类

2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**

### @Conditional

了解完自动装配的原理后，我们来关注一个细节问题，**自动配置类必须在一定的条件下才能生效；**

**@Conditional派生注解（Spring注解版原生的@Conditional作用）**

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

![img](https://img2020.cnblogs.com/blog/1905053/202004/1905053-20200412221747954-591282661.png)

**那么多的自动配置类，必须在一定的条件下才能生效；**也就是说，我们加载了这么多的配置类，但不是所有的都生效了。

我们怎么知道哪些自动配置类生效？

**我们可以通过启用 debug=true属性；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；**

```properties
#开启springboot的调试类
debug=true
```

**Positive matches:**（自动配置类启用的：正匹配）

**Negative matches:**（没有启动，没有匹配成功的自动配置类：负匹配）

**Unconditional classes: **（没有条件的类）

【演示：查看输出的日志】

掌握吸收理解原理，即可以不变应万变！

## SpringBootWeb开发

### 一、首先要解决的问题

1. 导入静态资源
2. 首页
3. jsp–模板引擎 Thymeleaf
4. 装配扩展SpringMVC
5. 增删改查
6. 拦截器
7. 国际化–中英翻译

### 二、静态资源访问

#### 2.1、什么是webjars

 WebJars是**将web前端资源（js，css等）打成jar包文件**，然后借助Maven工具，以jar包形式对web前端资源进行统一依赖管理，保证这些Web资源版本唯一性。WebJars的jar包部署在Maven中央仓库上。

#### 2.2、方式一（webjars一般不使用这种方式）

找到`WebMvcAutoConfiguration`类，这其中有一个`WebMvcAutoConfigurationAdapter`内部类，里面是关于mvc相关配置，这其中有一个方法

![image-20240320140940486](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320140940486.png)

了解可以从webjars中寻找后，去webjars官网，在pom中导入相关依赖

![image-20240320141134440](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320141134440.png)

使用`http://localhost:8080/webjars/**` 方式对静态资源进行访问！

比如`http://localhost:8080/webjars/jquery/3.6.2/package.json`

#### 2.3、方式二

![image-20240320141552484](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240320141552484.png)

ctrl点击后发现

![image-20240320141648977](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320141648977.png)

ctrl点击进入webproperties便可以发现，有以下四条路径可以访问

![image-20240320141818515](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320141818515.png)

它们的优先级如下

![image-20240320141928361](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320141928361.png)

#### 2.4、方式三（自定义资源路径）

 可以通过配置SpringBoot的默认配置文件，来自定义静态资源路径

### 三、首页

到`WebMvcAutoConfiguration`类，可以发现有关首页/欢迎页相关的方法

![image-20240320142349524](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320142349524.png)

研读可以发现访问`http://localhost:8080/`就是在静态资源路径下找名为index的页面

可以在static文件夹下加入favicon.ico更换图标

### 四、Thymeleaf 模板引擎 

>https://docs.spring.io/spring-boot/docs/2.7.18/reference/html/index.html springboot文档
>
>https://www.thymeleaf.org/ 官网

#### 4.1、引入

前端交给我们的页面，是html页面。如果是我们以前开发，我们需要把他们转成jsp页面，jsp好处就是当我们查出一些数据转发到JSP页面以后，我们可以用jsp轻松实现数据的显示，及交互等。

jsp支持非常强大的功能，包括能写Java代码，但是SpringBoot这个项目首先是以jar的方式，不是war。

第二，我们用的还是嵌入式的Tomcat，所以呢，他现在默认是不支持jsp的。
不支持jsp，如果我们直接用纯静态页面的方式，那给我们开发会带来非常大的麻烦，那怎么办呢？

#### 4.2、模板引擎

 SpringBoot推荐使用模板引擎来进行开发，基本思想参考下图：

![img](https://img-blog.csdnimg.cn/a6c6823337cd48d59d6a362dee00884e.png#pic_center)

模板引擎的作用就是我们来写一个页面模板，比如有些值是动态的，我们需要写一些表达式。

而这些值从哪来？其实就是我们在后台封装一些数据。然后把这个模板和这个数据交给我们模板引擎，模板引擎按照我们这个数据帮你把这表达式解析、填充到我们指定的位置，然后把这个数据最终生成一个我们想要的内容给我们写出去。

#### 4.3、引入Thymeleaf

```xml
<!--thymeleaf-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

![image-20240320150106172](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320150106172.png)

#### 4.4 简单测试

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {
    @RequestMapping("/hello")
    public String hello() {
        return "index";
    }
}
```

**当开启了thymeleaf支持，此时会自动匹配resources目录下templates文件夹下的页面进行跳转**

![image-20240320150317202](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240320150317202.png)

**注意templates文件夹下页面无法直接访问**

#### 4.5 Thymeleaf自动配置类：ThymeleafProperties

```java
//部分代码
public class ThymeleafProperties {
  private static final Charset DEFAULT_ENCODING;
  //默认前缀
  public static final String DEFAULT_PREFIX = "classpath:/templates/";
  //默认后缀
  public static final String DEFAULT_SUFFIX = ".html";
  private boolean checkTemplate = true;
  private boolean checkTemplateLocation = true;
  //前缀
  private String prefix = "classpath:/templates/";
  //后缀
  private String suffix = ".html";
}
```

-  从源码可以发现，这些代码和我们之前学习的SpringMVC的视图解析器配置很相似

- 可以看到视图解析器默认的前缀和后缀
- 只需要把我们的html页面放在类路径下的templates下，thymeleaf就可以帮我们自动渲染了。使用thymeleaf什么都不需要配置，只需要将他放在指定的文件夹下即可！

具体的thymeleaf基本语法格式，还需要我们参考thymeleaf官方文档练习。

## MVC自动配置及扩展

![image-20240320151308351](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240320151308351.png)

**双击shift全局搜索**

现在是SpringBoot自动帮我们配置了MVC，以前我们自己需要配置 映射器 适配器 和视图解析器，我们如何扩展SpringBoot帮我们自动所做的呢？可以看上面图片最后一段

### 实现视图解析器

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    //如果想要自己定义一些功能，只要写个组件，然后把它交给SpringBoot，SpringBoot就会自动装配
    //扩展SpringMVC

    //interface ViewResolver：实现了视图解析器接口的类，就可以看做是视图解析器
    @Bean
    public ViewResolver myViewResolver(){
        return new MyViewResolver();
    }


    /*
    * 自定义了一个自己的视图解析器MyViewResolver
    *   1、实现视图解析器接口：ViewResolver
    *   2、重写接口中的方法
    * */
    public static class MyViewResolver implements ViewResolver {

        @Override
        public View resolveViewName(String viewName, Locale locale) throws Exception {
            return null;
        }
    }
}
```

### 自定义starter

> Spring Boot Starters启动器依赖:
> 官方文档:
> https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/#using-boot-starter

我们分析完毕了源码以及自动装配的过程，我们可以尝试自定义一个启动器来玩玩！

#### 6.1、说明

启动器模块是一个 空 jar 文件，仅提供辅助性依赖管理，这些依赖可能用于自动装配或者其他类库；

**命名归约：**

官方命名：

- 前缀：spring-boot-starter-xxx
- 比如：spring-boot-starter-web....

自定义命名：

- xxx-spring-boot-starter
- 比如：mybatis-spring-boot-starter

####  6.2、编写阿里云OSS-Starter

![image-20240408171121566](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240408171121566.png)

##### starter 依赖管理

创建 aliyun-oss-spring-boot-starter 项目

如下所示

![image-20240408171853509](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240408171853509.png)

引入 autoconfigure 依赖

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-oss-spring-boot-autoconfigure</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

##### autoconfigure 自动配置

创建 aliyun-oss-spring-boot-autoconfigure 项目

注意无需启动类

![image-20240408172242136](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240408172242136.png)

引入阿里云oss依赖

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.10.2</version>
</dependency>
```

创建工具类和属性类

**AliOssUtil**

```java
package com.aliyun.oss;

import java.io.ByteArrayInputStream;

public class AliOssUtil {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

    /**
     * 文件上传
     *
     * @param bytes
     * @param objectName
     * @return
     */
    public String upload(byte[] bytes, String objectName) {

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        try {
            // 创建PutObject请求。
            ossClient.putObject(bucketName, objectName, new ByteArrayInputStream(bytes));
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

        //文件访问路径规则 https://BucketName.Endpoint/ObjectName
        StringBuilder stringBuilder = new StringBuilder("https://");
        stringBuilder
                .append(bucketName)
                .append(".")
                .append(endpoint)
                .append("/")
                .append(objectName);

        return stringBuilder.toString();
    }
    
    //get set constructor
}
```

**AliOssProperties**

```java
package com.aliyun.oss;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@ConfigurationProperties(prefix = "sky.alioss")
public class AliOssProperties {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;
	
    // get set constructor
}
```

**创建配置类**

AliOssAutoConfiguration

```java
package com.aliyun.oss;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
//用于告诉 Spring Boot 自动配置系统需要扫描项目中的配置属性类，并将其自动注册为 Spring 的 Bean。
@EnableConfigurationProperties(AliOssProperties.class)
public class AliOssAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public AliOssUtil aliOssUtil(AliOssProperties aliOssProperties){
        return  new AliOssUtil(aliOssProperties.getEndpoint(),
                aliOssProperties.getAccessKeyId(),
                aliOssProperties.getAccessKeySecret(),
                aliOssProperties.getBucketName());
    }
}
```

**因为spring会扫描 META-INF/spring/xxxx.imports ，所以我们自定义的也需要这个目录**

在resources目录下创建 `org.springframework.boot.autoconfigure.AutoConfiguration.imports`文件

里面需要配置类的全类名

```
com.aliyun.oss.AliOssAutoConfiguration
```

**至此，已经完成starter的编写**

编写完成后，可以安装到maven仓库中！

![](https://camo.githubusercontent.com/a2f03de793b5b34bb5874f19af1811cc50b7c6bcdbc98ee3d611616793c2f8ea/68747470733a2f2f696d67323032302e636e626c6f67732e636f6d2f626c6f672f313930353035332f3230323030342f313930353035332d32303230303431323232333730353436362d3534363931393132372e706e67)

#### 测试

略，直接引入即可

## SpringBoot-Data

对于数据访问层，无论是 SQL(关系型数据库) 还是 NOSQL(非关系型数据库)，Spring Boot 底层都是采用 Spring Data 的方式进行统一处理。

Spring Boot 底层都是采用 Spring Data 的方式进行统一处理各种数据库，Spring Data 也是 Spring 中与 Spring Boot、Spring Cloud 等齐名的知名项目。

Sping Data 官网：https://spring.io/projects/spring-data

数据库相关的启动器 ：可以参考官方文档：

https://docs.spring.io/spring-boot/docs/2.2.5.RELEASE/reference/htmlsingle/#using-boot-starter

创建一个SpringBoot文件，记得勾选web支持

![image-20240321132643033](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240321132643033.png)

编写yaml配置文件连接数据库

```yml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/test?useSSL=true&useUnicode=true&characterEncoding=utf8
    driver-class-name: com.mysql.cj.jdbc.Driver
```

配置完这一些东西后，我们就可以直接去使用了，因为SpringBoot已经默认帮我们进行了自动配置；去测试类测试一下

```java
import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@SpringBootTest
class Springboot03DataApplicationTests {
    @Autowired
    DataSource dataSource;

    @Test
    void contextLoads() throws SQLException {
        //查看一下数据源 默认为HikariDataSource (null)
        System.out.println(dataSource);

        //获得数据库连接
        Connection connection = dataSource.getConnection();
        System.out.println(connection);

        //xxxx Template ：springboot已经配置好模板，拿来即用 CRUD

        connection.close();
    }

}
```

结果：我们可以看到他默认给我们配置的数据源为 : class com.zaxxer.hikari.HikariDataSource ， 我们并没有手动配置

我们来全局搜索一下，找到数据源的所有自动配置都在 ：DataSourceAutoConfiguration文件：

```java
@Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
         DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.OracleUcp.class,
         DataSourceConfiguration.Generic.class, DataSourceJmxConfiguration.class })
protected static class PooledDataSourceConfiguration {
```

这里导入的类都在 DataSourceConfiguration 配置类下，可以看出 Spring Boot 2.2.5 默认使用HikariDataSource 数据源，而以前版本，如 Spring Boot 1.5 默认使用 org.apache.tomcat.jdbc.pool.DataSource 作为数据源；

**HikariDataSource 号称 Java WEB 当前速度最快的数据源，相比于传统的 C3P0 、DBCP、Tomcat jdbc 等连接池更加优秀；**

**可以使用 spring.datasource.type 指定自定义的数据源类型，值为 要使用的连接池实现的完全限定名。**

### JDBCTemplate

1、有了数据源(com.zaxxer.hikari.HikariDataSource)，然后可以拿到数据库连接(java.sql.Connection)，有了连接，就可以使用原生的 JDBC 语句来操作数据库；

2、**即使不使用第三方第数据库操作框架，如 MyBatis等，Spring 本身也对原生的JDBC 做了轻量级的封装，即JdbcTemplate。**

3、数据库操作的所有 CRUD 方法都在 JdbcTemplate 中。

4、Spring Boot 不仅提供了默认的数据源，同时默认已经配置好了 JdbcTemplate 放在了容器中，程序员只需自己注入即可使用

5、JdbcTemplate 的自动配置是依赖 org.springframework.boot.autoconfigure.jdbc 包下的 JdbcTemplateConfiguration 类

**JdbcTemplate主要提供以下几类方法：**

- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

#### 测试

```java
@RestController
public class JDBCController {

    @Autowired
    JdbcTemplate jdbcTemplate;


    //查询数据库的所有信息
    //没有实体类，数据库中的东西，怎么获取？ Map
    @GetMapping("/queryAll")
    public List<Map<String, Object>> queryAll() {
        String sql = "select * from users";
        List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
        return list;
    }

    //插入数据
    @GetMapping("/insert")
    public void insert() {
        String sql = "insert into users(name,password) values('小米','19464666')";
        jdbcTemplate.update(sql);
    }
    //更新数据
    @GetMapping("/update")
    public void update() {
        String sql = "update users set name = '小花花' where id = 1";
        jdbcTemplate.update(sql);
    }
    //删除数据
    @GetMapping("/delete/{id}")
    public void delete(@PathVariable int id) {
        String sql = "delete from users where id = ?";
        jdbcTemplate.update(sql,id);
    }
}
```

`jdbcTemplate.update(sql, id)` 中的 `id` 实际上是作为参数传递给 SQL 语句中的问号占位符的。这是 Spring JDBC 中执行参数化 SQL 查询的一种典型方式。当 `update` 方法执行时，它会将 SQL 语句中的问号占位符替换为提供的参数值，然后执行相应的 SQL 操作。

因此，虽然你在源代码中看不到 `update` 方法直接接收第二个参数 `id`，但在实际执行时，它会将 `id` 的值作为 SQL 语句中的参数传递给数据库进行操作。**传递是按序的**

### Druid

Druid 是阿里巴巴开源平台上一个数据库连接池实现，结合了 C3P0、DBCP 等 DB 池的优点，同时加入了日志监控。

Druid 可以很好的监控 DB 池连接和 SQL 的执行情况，天生就是针对监控而生的 DB 连接池。

Spring Boot 2.0 以上默认使用 Hikari 数据源，可以说 Hikari 与 Driud 都是当前 Java Web 上最优秀的数据源。

Github地址：https://github.com/alibaba/druid/

**com.alibaba.druid.pool.DruidDataSource 基本配置参数如下：**

| 配置                          | 缺省值             | 说明                                                         |
| ----------------------------- | ------------------ | ------------------------------------------------------------ |
| name                          |                    | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。 如果没有配置，将会生成一个名字，格式是："DataSource-" + System.identityHashCode(this). 另外配置此属性至少在1.0.5版本中是不起作用的，强行设置name会出错 [详情-点此处](http://blog.csdn.net/lanmo555/article/details/41248763)。 |
| url                           |                    | 连接数据库的url，不同数据库不一样。例如： mysql : jdbc:mysql://10.20.153.104:3306/druid2 oracle : jdbc:oracle:thin:@10.20.149.85:1521:ocnauto |
| username                      |                    | 连接数据库的用户名                                           |
| password                      |                    | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。详细看这里：https://github.com/alibaba/druid/wiki/使用ConfigFilter |
| driverClassName               | 根据url自动识别    | 这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName |
| initialSize                   | 0                  | 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 |
| maxActive                     | 8                  | 最大连接池数量                                               |
| maxIdle                       | 8                  | 已经不再使用，配置了也没效果                                 |
| minIdle                       |                    | 最小连接池数量                                               |
| maxWait                       |                    | 获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。 |
| poolPreparedStatements        | false              | 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。 |
| maxOpenPreparedStatements     | -1                 | 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 |
| validationQuery               |                    | 用来检测连接是否有效的sql，要求是一个查询语句。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会其作用。 |
| validationQueryTimeout        |                    | 单位：秒，检测连接是否有效的超时时间。底层调用jdbc Statement对象的void setQueryTimeout(int seconds)方法 |
| testOnBorrow                  | true               | 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。 |
| testOnReturn                  | false              | 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 |
| testWhileIdle                 | false              | 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。 |
| timeBetweenEvictionRunsMillis | 1分钟（1.0.14）    | 有两个含义： 1) Destroy线程会检测连接的间隔时间，如果连接空闲时间大于等于minEvictableIdleTimeMillis则关闭物理连接 2) testWhileIdle的判断依据，详细看testWhileIdle属性的说明 |
| numTestsPerEvictionRun        |                    | 不再使用，一个DruidDataSource只支持一个EvictionRun           |
| minEvictableIdleTimeMillis    | 30分钟（1.0.14）   | 连接保持空闲而不被驱逐的最长时间                             |
| connectionInitSqls            |                    | 物理连接初始化的时候执行的sql                                |
| exceptionSorter               | 根据dbType自动识别 | 当数据库抛出一些不可恢复的异常时，抛弃连接                   |
| filters                       |                    | 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有： 监控统计用的filter:stat 日志用的filter:log4j 防御sql注入的filter:wall |
| proxyFilters                  |                    | 类型是List<com.alibaba.druid.filter.Filter>，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系 |

#### 配置数据源

1、添加上 Druid 数据源依赖。

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.15</version>
</dependency>
```

2、切换数据源；之前已经说过 Spring Boot 2.0 以上默认使用 com.zaxxer.hikari.HikariDataSource 数据源，但可以 通过 spring.datasource.type 指定数据源。

```yml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/test?useSSL=true&useUnicode=true&characterEncoding=utf8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
```

3、数据源切换之后，在测试类中注入 DataSource，然后获取到它，输出一看便知是否成功切换；

![image-20240321144035959](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240321144035959.png)

4、切换成功！既然切换成功，就可以设置数据源连接初始化大小、最大连接数、等待时间、最小连接数 等设置项；可以查看源码

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

5、注意上面有log4j，导入Log4j 的依赖

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

6、现在需要程序员自己为 DruidDataSource 绑定全局配置文件中的参数，再添加到容器中，；

**我们需要自己添加 DruidDataSource 组件到容器中，并绑定属性；**

创建config包,下面新建 DruidConfig 配置类

> 注意因为导入的依赖不是启动器，所以需要使用java的方式进行注入

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

7、去测试类中测试一下；看是否绑定成功！

![image-20240321145014270](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240321145014270.png)

#### 配置Druid数据源监控

Druid 数据源具有监控的功能，并提供了一个 web 界面方便用户查看，类似安装 路由器 时，人家也提供了一个默认的 web 页面。

所以第一步需要设置 Druid 的后台管理页面，比如 登录账号、密码 等；配置后台管理；

```java
//配置 Druid 监控管理后台的Servlet；
    //内置 Servlet 容器时没有web.xml文件，所以使用 Spring Boot 的注册 Servlet 方式
    @Bean
    public ServletRegistrationBean statViewServlet() {
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");

        // 这些参数可以在 com.alibaba.druid.support.http.StatViewServlet
        // 的父类 com.alibaba.druid.support.http.ResourceServlet 中找到
        Map<String, String> initParams = new HashMap<>();
        initParams.put("loginUsername", "admin"); //后台管理界面的登录账号
        initParams.put("loginPassword", "123456"); //后台管理界面的登录密码

        //后台允许谁可以访问
        //initParams.put("allow", "localhost")：表示只有本机可以访问
        //initParams.put("allow", "")：为空或者为null时，表示允许所有访问
        initParams.put("allow", "");

        //deny：Druid 后台拒绝谁访问
        //initParams.put("kuangshen", "192.168.1.20");表示禁止此ip访问

        //设置初始化参数
        bean.setInitParameters(initParams);
        return bean;
    }
```

配置完毕后，我们可以选择访问 ：http://localhost:8080/druid

![image-20240321145406225](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240321145406225.png)

**配置 Druid web 监控 filter 过滤器**

```java
//配置 Druid 监控 之  web 监控的 filter
    //WebStatFilter：用于配置Web和Druid数据源之间的管理关联监控统计
    @Bean
    public FilterRegistrationBean webStatFilter() {
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        //exclusions：设置哪些请求进行过滤排除掉，从而不进行统计
        Map<String, String> initParams = new HashMap<>();
        initParams.put("exclusions", "*.js,*.css,/druid/*,/jdbc/*");
        bean.setInitParameters(initParams);

        //"/*" 表示过滤所有请求
        bean.setUrlPatterns(Arrays.asList("/*"));
        return bean;
    }
```

### Mybatis

在maven仓库中搜索mybatis，找到mybatis springboot starter，若之后需要整合mybatisplus，也同样操作

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.3.1</version>
</dependency>
```

 ![image-20240321150150683](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240321150150683.png)

**配置数据库连接信息**

```yml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/test?useSSL=true&useUnicode=true&characterEncoding=utf8
    driver-class-name: com.mysql.cj.jdbc.Driver
```

**创建实体类**

pojo/user

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
}
```

**配置Mapper接口类**

mapper/UserMapper

```java
@Mapper //这个注解表示这是一个mybatis的mapper类 或者在启动类上加 @MapperScan("com.balance.mapper")
@Repository
public interface UserMapper {

    List<User> getUserList();

    User getUserById(int id);

    int updateUser(User user);

    int deleteUser(int id);

    int addUser(User user);
}
```

**对应Mapper映射文件**

在resources目录下新建mybatis文件夹，其下新建mapper文件夹，新建UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.balance.mapper.UserMapper">

    <select id="getUserList" resultType="user">
        select * from users
    </select>

    <select id="getUserById" resultType="user">
        select * from users where id = #{id}
    </select>

    <insert id="addUser" parameterType="user">
        insert into users (name,password) values (#{name},#{pwd})
    </insert>

    <update id="updateUser" parameterType="user">
        update users set name=#{name},pwd=#{pwd} where id = #{id}
    </update>

    <delete id="deleteUser" parameterType="int">
        delete from users where id = #{id}
    </delete>


</mapper>
```

**在yml配置文件中配置别名和mapper映射**

```yml
# mybatis配置
mybatis:
  type-aliases-package: com.balance.pojo
  mapper-locations: classpath:mybatis/mapper/*.xml
```

已经说过 spring boot 官方并没有提供 myBaits 的启动器，是 myBatis 官方提供的开发包来适配的 spring boot，从 pom.xml 文件中的依赖包名也能看出来，并非是以 spring-boot 开头的；

同理上面全局配置文件中的这两行配置也是以 mybatis 开头 而非 spring 开头也充分说明这些都是 myBatis 官方提供的

可以从 org.mybatis.spring.boot.autoconfigure.MybatisProperties 中查看所有配置项

```java
@ConfigurationProperties(
    prefix = "mybatis"
)
public class MybatisProperties {
    public static final String MYBATIS_PREFIX = "mybatis";
    private static final ResourcePatternResolver resourceResolver = new PathMatchingResourcePatternResolver();
    private String configLocation;
    private String[] mapperLocations;
    private String typeAliasesPackage;
    private Class<?> typeAliasesSuperType;
    private String typeHandlersPackage;
    private boolean checkConfigLocation = false;
    private ExecutorType executorType;
    private Class<? extends LanguageDriver> defaultScriptingLanguageDriver;
    private Properties configurationProperties;
    @NestedConfigurationProperty
    private Configuration configuration;
```

**编写Controller**

```java
@RestController
public class MyController {

    @Autowired
    UserMapper userMapper;

    @GetMapping("/hello")
    public String hello() {
        return userMapper.getUserList().toString();
    }
}
```

**这里省略了Service层**

## 任务

### 异步任务

> 为什么要有异步任务

异步处理任务，比如我们在网站上发送邮件，后台会去发送邮件，此时前台会造成响应不动，直到邮件发送完毕，响应才会成功，所以我们一般会采用多线程的方式去处理这些任务。

使用两个注解解决：

- @EnableAsync //SpringBoot启动类
- @Async //执行方法

在业务层 service 中定义AsyncService类

```java
@Service
public class AsyncService {

    @Async
    public void hello() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("hello");
    }
}
```

在控制层 controller 中定义AsyncController控制类

```java
@RestController
public class MyController {
    @Autowired
    AsyncService asyncService;

    @GetMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "hello";
    }
}
```

在主启动类 Springboor09TestApplication 添加开启异步测试

```java
@EnableAsync
@SpringBootApplication
public class Springboot06TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot06TestApplication.class, args);
    }

}
```

加上这两个注解之后，前台就不会卡在那里等响应了

### 邮件任务

邮件发送，在我们的日常开发中，也非常的多，Springboot也帮我们做了支持

- 邮件发送需要引入spring-boot-start-mail
- SpringBoot 自动配置MailSenderAutoConfiguration
- 定义MailProperties内容，配置在application.yml中
- 自动装配JavaMailSender
- 测试邮件发送

导入spring-boot-starter-mail依赖

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

配置文件application.yml

```yml
spring:
  mail:
    username: 1835675603@qq.com
    password: oevqqvpkacjwebhh # 授权码
    host: smtp.qq.com
    properties:
      mail.smtp.ssl.enable: true
```

- 获取授权码：在QQ邮箱中的设置->账户->开启pop3和smtp服务

测试类中进行测试

```java
@SpringBootTest
class Springboot06TestApplicationTests {
    @Autowired
    JavaMailSenderImpl javaMailSender;

    @Test
    void contextLoads() {
        //一个简单的邮件发送
        SimpleMailMessage message = new SimpleMailMessage();

        //设置主题
        message.setSubject("这是测试邮件，测试简单的邮件发送");
        //设置正文
        message.setText("这是一封测试邮件");
        //设置收发人
        message.setTo("1835675603@qq.com");
        message.setFrom("1835675603@qq.com");

        javaMailSender.send(message);
    }

}
```

![image-20240324125335152](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324125335152.png)

复杂邮件怎么发呢？比如有字体颜色，附件之类。这时可以与之前javaweb所学知识结合

```java
@SpringBootTest
class Springboot06TestApplicationTests {
    @Autowired
    JavaMailSenderImpl javaMailSender;

    @Test
    void contextLoads() {
        //一个简单的邮件发送
        SimpleMailMessage message = new SimpleMailMessage();

        //设置主题
        message.setSubject("这是测试邮件，测试简单的邮件发送");
        //设置正文
        message.setText("这是一封测试邮件");
        //设置收发人
        message.setTo("1835675603@qq.com");
        message.setFrom("1835675603@qq.com");

        javaMailSender.send(message);
    }

    @Test
    void contextLoads2() throws MessagingException {
        //复杂的邮件发送
        MimeMessage mimeMessage = javaMailSender.createMimeMessage();
        //组装
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);////这里的true表示是否为Multipart，支持多文件

        //设置主题
        helper.setSubject("复杂的邮件发送");
        //设置正文
        helper.setText("<p style='color:red'>这是一封测试邮件</p>", true);//这里的true表示是否为html
        //发送附件
        helper.addAttachment("附件", new File("D:\\ideaProject\\SpringBoot\\springboot-06-test\\src\\main\\resources\\application.yml"));

        //设置收发人
        helper.setTo("1835675603@qq.com");
        helper.setFrom("1835675603@qq.com");

        javaMailSender.send(mimeMessage);
    }
    
    /* 这样也可以
    	//复杂的邮件发送
        MimeMessage mimeMessage = new MimeMessage(javaMailSender.getSession());
        //组装
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true,"utf-8");
    */
}

```

![image-20240324130318291](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324130318291.png)

### 定时任务

项目开发中经常需要执行一些定时任务，比如需要在每天凌晨的时候，分析一次前一天的日志信息，Spring为我们提供了异步执行任务调度的方式，提供了自带两个接口。

- TaskExecutor接口 **任务执行**
- TaskScheduler接口 **任务调度**

实际上就是写两个注解：

- @EnableScheduling  //SpringBoot启动类 开启定时任务
- @Scheduled  //方法上，什么时候执行

ScheduledService业务类

```java
@Service
public class ScheduledService {

    @Scheduled(cron = "0/2 * * * * ?")
    public void hello(){
        System.out.println("hello");
    }
}
```

在SpringBoot主启动类中添加注解

```java
@EnableScheduling
@SpringBootApplication
public class Springboot06TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot06TestApplication.class, args);
    }

}
```

cron表达式其实就是一个字符串，通过cron表达式可以**定义任务触发的时间**

构成规则：分为6或7个域，由空格分隔开，每个域代表一个含义

每个域的含义分别为：秒、分钟、小时、日、月、周、年(可选)

常用的表达式 交给gpt生成就行了 

> cron表达式在线生成器：https://cron.qqe2.com/

```
0/2 * * * * ?   表示每2秒 执行任务
0 0/2 * * * ?   表示每2分钟 执行任务
0 0 2 1 * ?   表示在每月的1日的凌晨2点调整任务
0 15 10 ? * MON-FRI   表示周一到周五每天上午10:15执行作业
0 15 10 ? 6L 2002-2006   表示2002-2006年的每个月的最后一个星期五上午10:15执行作
0 0 10,14,16 * * ?   每天上午10点，下午2点，4点
0 0/30 9-17 * * ?   朝九晚五工作时间内每半小时
0 0 12 ? * WED   表示每个星期三中午12点
0 0 12 * * ?   每天中午12点触发
0 15 10 ? * *   每天上午10:15触发
0 15 10 * * ?     每天上午10:15触发
0 15 10 * * ?   每天上午10:15触发
0 15 10 * * ? 2005   2005年的每天上午10:15触发
0 * 14 * * ?     在每天下午2点到下午2:59期间的每1分钟触发
0 0/5 14 * * ?   在每天下午2点到下午2:55期间的每5分钟触发
0 0/5 14,18 * * ?     在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
0 0-5 14 * * ?   在每天下午2点到下午2:05期间的每1分钟触发
0 10,44 14 ? 3 WED   每年三月的星期三的下午2:10和2:44触发
0 15 10 ? * MON-FRI   周一至周五的上午10:15触发
0 15 10 15 * ?   每月15日上午10:15触发
0 15 10 L * ?   每月最后一日的上午10:15触发
0 15 10 ? * 6L   每月的最后一个星期五上午10:15触发
0 15 10 ? * 6L 2002-2005   2002年至2005年的每月的最后一个星期五上午10:15触发
0 15 10 ? * 6#3   每月的第三个星期五上午10:15触发
```

## 分布式系统

在《分布式系统原理与范型》一书中有如下定义：“分布式系统是若干独立计算机的集合，这些计算机对于用户来说就像单个相关系统”；

分布式系统是由一组通过网络进行通信、为了完成共同的任务而协调工作的计算机节点组成的系统。分布式系统的出现是为了用廉价的、普通的机器完成单个计算机无法完成的计算、存储任务。其目的是**利用更多的机器，处理更多的数据**。

分布式系统（distributed system）是建立在网络之上的软件系统。

首先需要明确的是，**只有当单个节点的处理能力无法满足日益增长的计算、存储任务的时候，且硬件的提升（加内存、加磁盘、使用更好的CPU）高昂到得不偿失的时候，应用程序也不能进一步优化的时候，我们才需要考虑分布式系统**。因为，分布式系统要解决的问题本身就是和单机系统一样的，而由于分布式系统多节点、通过网络通信的拓扑结构，会引入很多单机系统没有的问题，为了解决这些问题又会引入更多的机制、协议，带来更多的问题。

随着互联网的发展，网站应用的规模不断扩大，常规的垂直应用架构已无法应对，分布式服务架构以及流动计算架构势在必行，急需**一个治理系统**确保架构有条不紊的演进。

在Dubbo的官网文档有这样一张图

![image-20220311214745570](https://cocochimp-markdown-img.oss-cn-beijing.aliyuncs.com/16_Mybatis-plus/image-20220311214745570.png)

> 单一应用架构

当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架(ORM)是关键。

![image-20220311214819155](https://cocochimp-markdown-img.oss-cn-beijing.aliyuncs.com/16_Mybatis-plus/image-20220311214819155.png)

适用于小型网站，小型管理系统，将所有功能都部署到一个功能里，简单易用。

1、性能扩展比较难

2、协同开发问题

3、不利于升级维护

> 垂直应用架构

当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架(MVC)是关键。

![image-20220311214936464](https://cocochimp-markdown-img.oss-cn-beijing.aliyuncs.com/16_Mybatis-plus/image-20220311214936464.png)

通过切分业务来实现各个模块独立部署，降低了维护和部署的难度，团队各司其职更易管理，性能扩展也更方便，更有针对性。

**缺点：公用模块无法重复利用，开发性的浪费**

> 分布式服务架构

当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的**分布式服务框架(RPC: Remote Procedure Call 远程过程调用)**是关键。

![image-20220311215008041](https://cocochimp-markdown-img.oss-cn-beijing.aliyuncs.com/16_Mybatis-plus/image-20220311215008041.png)

> 流动计算架构

当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于**提高机器利用率的资源调度和治理中心**(SOA)[ Service Oriented Architecture]是关键。

### 什么是RPC

RPC【Remote Procedure Call】是指远程过程调用，是一种进程间通信方式，他是一种技术的思想，而不是规范。它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。

**为什么要用RPC呢？就是无法在一个进程内，甚至一个计算机内通过本地调用的方式完成的需求，比如不同的系统间的通讯，甚至不同的组织间的通讯，由于计算能力需要横向扩展，需要在多台机器组成的集群上部署应用。RPC就是要像调用本地的函数一样去调远程函数；**

推荐阅读文章：https://www.jianshu.com/p/2accc2840a1b

**基本原理**

![image-20220311215125107](https://cocochimp-markdown-img.oss-cn-beijing.aliyuncs.com/16_Mybatis-plus/image-20220311215125107.png)

![image-20220311215143733](https://cocochimp-markdown-img.oss-cn-beijing.aliyuncs.com/16_Mybatis-plus/image-20220311215143733.png)

RPC两个核心模块：==通讯，序列化==

**HTTP和RPC都是通信协议，但它们的使用场景和目标有所不同。**

HTTP（超文本传输协议）是一种无状态的、基于请求-响应的协议，主要用于Web浏览器和服务器之间的通信。它的主要优点是简单、灵活和广泛支持。**但是，HTTP的无状态性意味着每个请求都需要包含所有必要的信息，这可能导致一些效率问题。**

RPC（远程过程调用）是一种允许程序调用另一台计算机上的程序或服务的协议。**RPC更像是一种通信模式，它可以基于各种底层协议（如HTTP、TCP等）**。RPC的主要优点是**它可以提供更高的性能和更好的封装性**。通过使用RPC，**开发人员可以像调用本地函数一样调用远程服务，而不需要关心底层的网络通信细节**。

总的来说，HTTP和RPC各有优势，选择使用哪种协议取决于具体的应用场景和需求。

### Dubbo

![image-20220311215250368](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20220311215250368.png)

**服务提供者**（Provider）：暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。

**服务消费者**（Consumer）：调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

**注册中心**（Registry）：注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者

**监控中心**（Monitor）：服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心

**调用关系说明**

- 服务容器负责启动，加载，运行服务提供者。
- 服务提供者在启动时，向注册中心注册自己提供的服务。
- 服务消费者在启动时，向注册中心订阅自己所需的服务。
- 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
- 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
- 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

### Zookeeper 注册中心

点进dubbo官方文档，推荐我们使用Zookeeper 注册中心

下载链接：[Apache ZooKeeper](https://gitee.com/link?target=https%3A%2F%2Fzookeeper.apache.org%2Freleases.html%23download)

下载版本：3.8.4

[![image-20220617103517716](https://raw.githubusercontent.com/KyDestroy/image/main/image-20220617103517716.png)](https://raw.githubusercontent.com/KyDestroy/image/main/image-20220617103517716.png)

- 注意：3.5版本后要下载bin文件

解压后打开 conf，将`zoo_sample.cfg`复制一份改名为`zoo.cfg`

bin目录下双击或者在cmd中执行zkServer.cmd

![image-20240324150829686](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324150829686.png)

再启动客户端即 zkCli.cmd

使用`zkCli.cmd`测试

`ls /`：列出Zookeeper根下保存的所有节点

![image-20240324151004270](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324151004270.png)

`create -e /balance 123`：创建一个 /dt 节点，值为123

![image-20240324151126222](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324151126222.png)

`get /balance`：获取 /dt 节点的值

![image-20240324151143348](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324151143348.png)

我们再来看下节点

![image-20240324151156427](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324151156427.png)

### Dubbo-admin

dubbo本身并不是服务软件。它其实就是一个jar包，能够帮助你的java程序连接到zookeeper，并利用zookeeper消费、提供服务。

但是为了让用户更好的管理监控更多的dubbo服务，官方提供了一个可视化的监控程序dubbo-admin，不过这个监控即使不装也不影响使用。

**我们这里来安装一下：**

1. **下载dubbo-admin**

   地址：https://github.com/apache/dubbo-admin/releases

   0.6.0

2. **解压进入目录**

   修改 dubbo-admin\scr\main\resources\application.properties 指定zookeeper地址，如果前面未动，

   ```properties
   server.port=38080
   spring.velocity.cache=false
   spring.velocity.charset=UTF-8
   spring.velocity.layout-url=/templates/default.vm
   spring.messages.fallback-to-system-locale=false
   spring.messages.basename=i18n/message
   spring.root.password=root
   spring.guest.password=guest
   # 注册中心的地址
   dubbo.registry.address=zookeeper://127.0.0.1:2181
   ```

3. 在项目dubbo-admin-server目录下打包 dubbo-admin windows使用powershell需要加引号 cmd无需

   ```
   mvn clean package '-Dmaven.test.skip=true'
   ```

   - 如果打包失败，切换环境变量的jdk版本为1.8

4. 执行 dubbo/target 下的 `dubbo-admin-server-0.6.0.jar`

   ```
   java -jar dubbo-admin-server-0.6.0.jar
   ```

   - 注意：**Zookeeper的服务一定要打开**

   注意新版本Dubbo是前后端分离项目，前端 dubbo-admin-ui 是一个 vue2 项目，运行需要 node.js 环境，安装好 node.js 环境后，用命令行进入 dubbo-admin-ui 目录下，使用 npm install 命令下载依赖包，再使用 npm run dev 运行项目。

   执行完毕，我们去访问一下 http://localhost:8080/前端页面，这时候我们需要输入登录账号的密码，默认root-root

   **我这里因为电脑node版本过高无法正常启动**

### 实战

1. zookeeper：注册中心

   dubbo-admin：一个监控管理后台，查看我们注册了哪些服务，哪些服务被消费了

   Dubbo：jar包

2. 步骤

   前提：zookeeper服务已经开启！

   1. 提供者提供服务
      1. 导入依赖
      2. 配置注册中心的地址，以及服务发现名，和要扫描的包~
      3. **在想要被注册的服务上面~ 增加一个注解 @DubboService**
   2. 消费者如何消费
      1. 导入依赖
      2. 配置注册中心的地址，配置自己的服务名
      3. **从远程注入服务~ @DubboReference**

1. 创建一个空项目

2. 创建两个module，皆为springboot项目，分别为 provider 和 consumer

3. 导入依赖

   ```xml
   <!--导入依赖：Dubbo + Zookeeper-->
   <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
   <dependency>
       <groupId>org.apache.dubbo</groupId>
       <artifactId>dubbo-spring-boot-starter</artifactId>
       <version>2.7.15</version>
   </dependency>
   <!--zkclient-->
   <!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
   <dependency>
       <groupId>com.github.sgroschupf</groupId>
       <artifactId>zkclient</artifactId>
       <version>0.1</version>
   </dependency>
   
   <!--日志会冲突，需要导入如下依赖-->
   <dependency>
       <groupId>org.apache.curator</groupId>
       <artifactId>curator-framework</artifactId>
       <version>2.12.0</version>
   </dependency>
   <dependency>
       <groupId>org.apache.curator</groupId>
       <artifactId>curator-recipes</artifactId>
       <version>2.12.0</version>
   </dependency>
   <dependency>
       <groupId>org.apache.zookeeper</groupId>
       <artifactId>zookeeper</artifactId>
       <version>3.4.14</version>
       <!--排除这个slf4j-log4j12-->
       <exclusions>
           <exclusion>
               <groupId>org.slf4j</groupId>
               <artifactId>slf4j-log4j12</artifactId>
           </exclusion>
       </exclusions>
   </dependency>
   ```

   **provider项目结构**

   ![image-20240324164006370](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324164006370.png)

- Service层

  - TicketService

    ```java
    package com.balance.service;
    
    public interface TicketService {
        public String getTicket();
    }
    
    ```

  - TicketServiceImpl

    ```java
    package com.balance.service;
    
    import org.apache.dubbo.config.annotation.DubboService;
    import org.springframework.stereotype.Service;
    
    @DubboService //注册为Dubbo的服务
    @Service //注册为Spring的服务 注意不要导错包
    public class TicketServiceImpl implements TicketService{
        @Override
        public String getTicket() {
            return "hello,world";
        }
    }
    ```

- `application.properties`

  ```properties
  # 应用服务 WEB 访问端口
  server.port=8081
  
  # 服务提供者名称
  spring.application.name=provider
  # 服务的应用
  dubbo.application.name=provider-service
  # 注册中心地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  # 哪些服务注册
  dubbo.scan.base-packages=com.balance.service
  ```

**consumer项目结构**

![image-20240324164546262](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324164546262.png)

- Service层

  - UserService

    ```java
    @Service
    public class UserService {
        // 想拿到provider-server提供的票，要去注册中心拿到服务
        // 引用， Pom坐标，可以定义路径相同的接口名
        @DubboReference
        TicketService ticketService;
    
        public void buyTicket(){
            String ticket = ticketService.getTicket();
            System.out.println("在注册中心拿到=>" + ticket);
        }
    }
    ```

  - TicketService **这里通过定义相同的接口来使用**

    ```java
    package com.balance.service;
    
    public interface TicketService {
        public String getTicket();
    }
    ```

- `application.properties`修改端口号

  ```properties
  # 应用服务 WEB 访问端口
  server.port=8082
  
  #应用名称
  spring.application.name=consumer-spring
  #从哪里获得服务，此时需要给出自己的名字
  dubbo.application.name=consumer
  #注册中心的地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  ```

测试：

编写测试类

```java
@SpringBootTest
class ConsumerApplicationTests {
    @Autowired
    UserService userService;

    @Test
    void contextLoads() {
        userService.buyTicket();
    }

}
```

* 开启ZooKeeper
* 运行 ProviderApplication 启动类 服务提供者
* 运行 ConsumerApplicationTests 测试类  消费者

结果：

![image-20240324165153093](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240324165153093.png)

## 总结

```
三层架构 + MVC
	架构 -->解耦

开发框架
    Spring:IOC AOP
			IOC : 控制反转
原来我们都是自己一步步操作,现在交给容器了!我们需要什么就去拿就可以了
                
            AOP:切面(本质,动态代理）
为了解什么?不影响业本来的情况下,实现动态增加功能,大量应用在日志,事务等等
                    
	Spring是一个轻量级的Java开源框架，容器
        目的：解决企业开发的复杂性问题
        Spring是春天，但配置文件繁琐

    SpringBoot
      	SpringBoot,新代javaEE的开发标准,开箱即用!>拿过来就可以用,
        它自动帮我们配置了非常多的东西,我们拿来即用,
        特性:约定大于配置!
                    
随着公司体系越来越大,用户越来越多
                    
微服务架构—>新架构
	模块化,功能化!
	用户,支付,签到,娱乐…;
	如果一台服务器解决不了就再增加一台服务器! --横向扩展
	假设A服务器占用98%资源B服务器只占用了10%.–负载均衡;

	用户非常多而到十分少给用户多一点服务器,给签到少一点服务器
	
	将原来的整体项,分成模块化,用户就是一个单独的项目,签到也是一个单独的项目,项目和项目之前需要通信,如何通信
微服务架构问题?即分布式架构问题
                    
分布式架构会遇到的四个核心问题?
   	1.这么多服务,客户端该如何去访问? 网关
    2.这么多服务,服务之间如何进行通信? http/rpc
    3.这么多服务,如何治理呢? //zookeeper
    4.服务挂了,怎么办? // Hystrix
        
解决方案:                    
	Springcloud是一套生态，就是来解决以上分布式架构的4个问题
    想使用Spring Clould ,必须要掌握 springBoot , 因为Springcloud是基于springBoot ;

	1. spring Cloud NetFlix ,出来了一套解决方案！一站式解决方案。可以直接使用
	Api网关 , zuul组件
	Feign --> Httpclient ---> http通信方式,同步并阻塞
	服务注册与发现, Eureka
	熔断机制, Hystrix
                    
2018年年底,NetFlix 宣布无限期停止维护。生态不再维护,就会脱节。

	2. Apache Dubbo zookeeper ,第二套解决方案
	API:没有!要么找第三方组件,要么自己实现
	Dubbo 是一个高性能的基于Java实现的RPC通信框架!2.6.x
	服务注册与发现 , zookeeper :动物管理者 ( Hadoop , Hive )
	没有:借助了Hystrix
                    
不完善，Dubbo

	3. SpringCloud Alibaba 一站式解决方案

目前又提出了新的思路:
	服务网格：也许是下一代维服务标准，Service mesh
	代表解决方案：istio（未来可能需要掌握）

万变不离其宗,一通百通！
	1.API网关 ， 服务路由
	2.HTTP，RPC框架，异步调用
	3.服务注册与发现，高可用
	4.熔断机制，服务降级
         
为什么要解决这个问题？因为网络是不可靠的!!!
```
