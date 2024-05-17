# Spring

> https://www.kuangstudy.com/course?cid=1
>
> https://www.cnblogs.com/renxuw/p/12994080.html
>
> https://docs.spring.io/spring-framework/docs/5.3.31/reference/html/overview.html#overview

## 简介

> SpringFramework 5.3
>
> https://docs.spring.io/spring-framework/docs/5.3.31/reference/html/
>
> 看官网，spring 5.3 依旧支持jdk8 ，再向上不支持了，导包也注意一下导springmvc 5.3.31

Spring框架是由于软件开发的复杂性而创建的。Spring使用的是基本的[JavaBean](https://baike.baidu.com/item/JavaBean/529577?fromModule=lemma_inlink)来完成以前只可能由[EJB](https://baike.baidu.com/item/EJB/144195?fromModule=lemma_inlink)完成的事情。然而，Spring的用途不仅仅限于[服务器端](https://baike.baidu.com/item/服务器端/3369401?fromModule=lemma_inlink)的开发。从简单性、[可测试性](https://baike.baidu.com/item/可测试性/1459715?fromModule=lemma_inlink)和松[耦合性](https://baike.baidu.com/item/耦合性/4297612?fromModule=lemma_inlink)角度而言，绝大部分[Java](https://baike.baidu.com/item/Java/85979?fromModule=lemma_inlink)应用都可以从Spring中受益。

**Spring的初衷：**

* JAVA EE开发应该更加简单。

* 使用接口而不是使用类，是更好的编程习惯。Spring将使用接口的复杂度几乎降低到了零。

* 为JavaBean提供了一个更好的应用配置框架。

* 更多地强调面向对象的设计，而不是现行的技术如JAVA EE。

* 尽量减少不必要的异常捕捉。

* 使应用程序更加容易测试。

**Spring的目标：**

* 可以令人方便愉快的使用Spring。

* 应用程序代码并不依赖于Spring APIs。

* Spring不和现有的解决方案竞争，而是致力于将它们融合在一起。

**Spring的基本组成：**

* 最完善的轻量级核心框架。

* 通用的事务管理抽象层。

* JDBC抽象层。

* 集成了Toplink, Hibernate, JDO, and iBATIS SQL Maps。

* AOP功能。

* 灵活的MVC Web应用框架。

### 优点

1、Spring是一个开源免费的框架 , 容器 .

2、Spring是一个轻量级的框架 , 非侵入式的 .

**3、控制反转 IoC , 面向切面 Aop**

4、对事物的支持 , 对框架的支持

.......

一句话概括：

**Spring是一个轻量级的控制反转(IoC)和面向切面(AOP)的容器（框架）。**

==IoC：Inversion of Control==  ==AOP：Aspect Oriented Programming==

### 组成

Spring 框架是一个分层架构，由 7 个定义良好的模块组成。Spring 模块构建在核心容器之上，核心容器定义了创建、配置和管理 bean 的方式 .

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401071521619.png)

组成 Spring 框架的每个模块（或组件）都可以单独存在，或者与其他一个或多个模块联合实现。每个模块的功能如下：

- **核心容器**：核心容器提供 Spring 框架的基本功能。核心容器的主要组件是 BeanFactory，它是**工厂模式**的实现。**BeanFactory 使用*控制反转*（IOC） 模式将应用程序的配置和依赖性规范与实际的应用程序代码分开**。
- **Spring 上下文**：Spring 上下文是一个配置文件，向 Spring 框架提供上下文信息。Spring 上下文包括企业服务，例如 JNDI、EJB、电子邮件、国际化、校验和调度功能。
- **Spring AOP**：通过配置管理特性，Spring AOP 模块直接将面向切面的编程功能 , 集成到了 Spring 框架中。所以，可以很容易地使 Spring 框架管理任何支持 AOP的对象。Spring AOP 模块为基于 Spring 的应用程序中的对象提供了事务管理服务。通过使用 Spring AOP，不用依赖组件，就可以将声明性事务管理集成到应用程序中。
- **Spring DAO**：JDBC DAO 抽象层提供了有意义的异常层次结构，可用该结构来管理异常处理和不同数据库供应商抛出的错误消息。异常层次结构简化了错误处理，并且极大地降低了需要编写的异常代码数量（例如打开和关闭连接）。Spring DAO 的面向 JDBC 的异常遵从通用的 DAO 异常层次结构。
- **Spring ORM**：Spring 框架插入了若干个 ORM 框架，从而提供了 ORM 的对象关系工具，其中包括 JDO、Hibernate 和 iBatis SQL Map。所有这些都遵从 Spring 的通用事务和 DAO 异常层次结构。
- **Spring Web 模块**：Web 上下文模块建立在应用程序上下文模块之上，为基于 Web 的应用程序提供了上下文。所以，Spring 框架支持与 Jakarta Struts 的集成。Web 模块还简化了处理多部分请求以及将请求参数绑定到域对象的工作。
- **Spring MVC 框架**：MVC 框架是一个全功能的构建 Web 应用程序的 MVC 实现。通过策略接口，MVC 框架变成为高度可配置的，MVC 容纳了大量视图技术，其中包括 JSP、Velocity、Tiles、iText 和 POI。

### 拓展

![image-20240107152345871](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401071523019.png)

**Spring Boot与Spring Cloud**

- Spring Boot 
  - 是 Spring 的一套快速配置脚手架，
  - 可以基于Spring Boot 快速开发单个微服务;
  - 约定大于配置
- Spring Cloud
  - 基于Spring Boot实现的；

Spring Boot专注于快速、方便集成的单个微服务个体，Spring Cloud关注全局的服务治理框架；

SpringBoot在SpringClound中起到了承上启下的作用，**如果你要学习SpringCloud必须要学习SpringBoot。**

Spring Boot使用了**约定优于配置的理念**，很多集成方案已经帮你选择好了，能不配置就不配置 , Spring Cloud很大的一部分是**基于Spring Boot来实现**，**Spring Boot可以离开Spring Cloud独立使用开发项目**，**但是Spring Cloud离不开Spring Boot，属于依赖的关系**。

## IOC

### 原型

新建一个空白的maven项目

我们先用我们原来的方式写一段代码 .

1、先写一个UserDao接口

```java
public interface UserDao {
   public void getUser();
}
```

2、再去写Dao的实现类

```java
public class UserDaoImpl implements UserDao {
   @Override
   public void getUser() {
       System.out.println("获取用户数据");
  }
}
```

3、然后去写UserService的接口

```java
public interface UserService {
   public void getUser();
}
```

4、最后写Service的实现类

```java
public class UserServiceImpl implements UserService {
   private UserDao userDao = new UserDaoImpl();

   @Override
   public void getUser() {
       userDao.getUser();
  }
}
```

5、测试一下`(这里每次更换不同接口都需要在service层更改new的接口)`

```java
@Test
public void test(){
   UserService service = new UserServiceImpl();
   service.getUser();
}
```

这是我们原来的方式 , 我们现在修改一下 .

把Userdao的实现类增加一个 .

```java
public class UserDaoMySqlImpl implements UserDao {
   @Override
   public void getUser() {
       System.out.println("MySql获取用户数据");
  }
}
```

紧接着我们要去使用MySql的话 , 我们就需要去service实现类里面修改对应的实现

```java
public class UserServiceImpl implements UserService {
   private UserDao userDao = new UserDaoMySqlImpl();

   @Override
   public void getUser() {
       userDao.getUser();
  }
}
```

在假设, 我们再增加一个Userdao的实现类 .

```java
public class UserDaoOracleImpl implements UserDao {
   @Override
   public void getUser() {
       System.out.println("Oracle获取用户数据");
  }
}
```

那么我们要使用Oracle , 又需要去service实现类里面修改对应的实现 . 假设我们的这种需求非常大 , 这种方式就根本不适用了, 甚至反人类对吧 , 每次变动 , 都需要修改大量代码 . 这种设计的耦合性太高了, 牵一发而动全身 .

**那我们如何去解决呢 ?**

我们可以在需要用到他的地方 , 不去实现它 , 而是留出一个接口 , 利用set , 我们去代码里修改下 .

```java
public class UserServiceImpl implements UserService {
   private UserDao userDao;
// 利用set实现
   public void setUserDao(UserDao userDao) {
       this.userDao = userDao;
  }

   @Override
   public void getUser() {
       userDao.getUser();
  }
}
```

现在去我们的测试类里 , 进行测试 ;`（有了set方法就可以在调用的时候由用户选择调用的接口）`

```java
@Test
public void test(){
   UserServiceImpl service = new UserServiceImpl();
   service.setUserDao( new UserDaoMySqlImpl() );
   service.getUser();
   //那我们现在又想用Oracle去实现呢
   service.setUserDao( new UserDaoOracleImpl() );
   service.getUser();
}
```

大家发现了区别没有 ? 可能很多人说没啥区别 . 但是同学们 , 他们已经发生了根本性的变化 , 很多地方都不一样了 . 仔细去思考一下 , **以前所有东西都是由程序去进行控制创建** , **而现在是由我们自行控制创建对象 , 把主动权交给了调用者** . 程序不用去管怎么创建,怎么实现了 . 它只负责提供一个接口 .

这种思想 , 从本质上解决了问题 , 我们程序员不再去管理对象的创建了 , 更多的去关注业务的实现 . 耦合性大大降低 . 这也就是IOC的原型 !

### 本质

**控制反转IoC(Inversion of Control)，是一种设计思想，DI(依赖注入)是实现IoC的一种方法**，也有人认为DI只是IoC的另一种说法。没有IoC的程序中 , 我们使用面向对象编程 , 对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401071551479.webp)

**IoC是Spring框架的核心内容**，使用多种方式完美的实现了IoC，可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现IoC。

Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从Ioc容器中取出需要的对象。

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401071552637.webp)

采用**XML方式配置**Bean的时候，Bean的**定义信息是和实现分离**的，而**采用注解的方式可以把两者合为一体**，==Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。==

**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）。**

## HelloSpring

导入jar包,注意需要是spring-webmvc，因为这个会自动帮我们导入其他依赖的jar包

```xml
<dependency>
   <groupId>org.springframework</groupId>
   <artifactId>spring-webmvc</artifactId>
   <version>5.2.0.RELEASE</version>
</dependency>
```

编写一个Hello实体类

```java
public class Hello {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "name='" + name + '\'' +
                '}';
    }
}

```

编写我们的spring文件 , 这里我们命名为beans.xml

*resources/beans.xml*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        使用spring来创建对象，在spring中这些都称为bean
        id = 变量名
        class = new 的对象
        property 相当于给属性设置值
    -->
    <bean id="hello" class="com.balance.pojo.Hello">
        <property name="name" value="Spring"/>
    </bean>

</beans>
```

测试

```java
public class Test {
    public static void main(String[] args) {
        //解析beans.xml，获取spring上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在spring中管理，如果要使用，直接去里面取
        //getBean，参数为配置文件中bean标签的id
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello);
    }
}
```

问题：

- Hello 对象是谁创建的 ? hello 对象是由Spring创建的
- Hello 对象的属性是怎么设置的 ? hello 对象的属性是由Spring容器设置的

这个过程就叫控制反转 :

- 控制 : 谁来控制对象的创建 , 传统应用程序的对象是由程序本身控制创建的 , 使用Spring后 , 对象是由Spring来创建的
- 反转 : 程序本身不创建对象 , 而变成被动的接收对象 .

**依赖注入 : 就是利用set方法来进行注入的**。IOC是一种编程思想，由主动的编程变成被动的接收

**对IOC原型进行修改**

新建*resources/beans.xml* **注意八大基本类型用value，其他用ref**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

   <bean id="MysqlImpl" class="com.kuang.dao.impl.UserDaoMySqlImpl"/>
   <bean id="OracleImpl" class="com.kuang.dao.impl.UserDaoOracleImpl"/>

   <bean id="ServiceImpl" class="com.kuang.service.impl.UserServiceImpl">
       <!--注意: 这里的name并不是属性 , 而是set方法后面的那部分 , 首字母小写-->
       <!--引用另外一个bean , 不是用value 而是用 ref-->
       <property name="userDao" ref="OracleImpl"/><!--具体使用哪个接口这里可以直接配置-->
   </bean>

</beans>
```

测试

```java
@Test
public void test2(){
   ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   //不需要再在代码中写出调用哪个接口，只需要在配置文件中指明调用的接口即可。
   UserServiceImpl serviceImpl = (UserServiceImpl) context.getBean("ServiceImpl");
   serviceImpl.getUser();
    //原来的步骤
    //UserService userService = new UserServiceImpl();
    //userService.setUserDao(new UserDaoMysqlImpl());//原先需要在代码中调用特定的方法
	//userService.getUser();
}
```

到了现在 , 我们彻底不用再程序中去改动了 , 要实现不同的操作 , 只需要在xml配置文件中进行修改 ,

**所谓的IoC,一句话搞定 : 对象由Spring 来创建 , 管理 , 装配 !**

## Ioc创建对象方式

### 无参构造创建

就是在HelloSpring中创建的方式

类中为无参构造，xml中用`<property>`标签

```xml
public Hello() {}

<bean id="hello" class="com.balance.pojo.Hello">
    <property name="name" value="Spring"/>
</bean>
```

### 有参构造

当类中构造为有参构造时 

beans.xml 有三种方式编写

下标方式

```xml
<!-- 第一种根据index参数下标设置 -->
<bean id="hello" class="com.kuang.pojo.Hello">
   <!-- index指构造方法 , 下标从0开始 -->
   <constructor-arg index="0" value="balance"/>
</bean>
```

参数名方式

```xml
<!-- 第二种根据参数名字设置 -->
<bean id="hello" class="com.kuang.pojo.Hello">
   <!-- name指参数名 -->
   <constructor-arg name="name" value="balance"/>
</bean>
```

参数类型方式-**不推荐**

```xml
<!-- 第三种根据参数类型设置(不推荐使用) -->
<bean id="hello" class="com.balance.pojo.Hello">
    <constructor-arg type="java.lang.String" value="balance"/>
</bean>
```

**结论：在配置文件加载的时候。其中管理的所有对象都已经初始化了！即所有的bean标签**

## Spring配置

### 别名

alias 设置别名 , 为bean设置别名 , 可以设置多个别名

```xml
<bean id="hello" class="com.balance.pojo.Hello">
    <constructor-arg name="name" value="balance"/>
    <constructor-arg name="age" value="1"/>
</bean>

<alias name="hello" alias="helloNew"/>
```

此后getbean时就可以使用别名了

```java
public static void main(String[] args) {
    //获取spring上下文对象
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    //我们的对象现在都在spring中管理，如果要使用，直接去里面取
    Hello hello = (Hello) context.getBean("helloNew");
    System.out.println(hello);
}
```

### Bean的配置

```xml
<!--bean就是java对象,由Spring创建和管理-->

<!--
   id 是bean的标识符,要唯一,如果没有配置id,name就是默认标识符
   如果配置id,又配置了name,那么name是别名
   name可以设置多个别名,可以用逗号,分号,空格隔开

   class是bean的全限定名=包名+类名
-->
<bean id="hello" name="hello2 h2,h3;h4" class="com.balance.pojo.Hello">
   <property name="name" value="Spring"/>
</bean>
```

在bean的属性name，也是别名

 **如果不配置id和name,且类对应的bean只有一个，可以根据applicationContext.getBean(类名.class)获取对象;**

### import

团队的合作通过import来实现，假设有多个bean文件，可以将多个配置文件，导入合并为一个

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="beans1.xml"/>
    <import resource="beans2.xml"/>
    <import resource="beans3.xml"/>
</beans>
```

## 依赖注入

Dependency Injection

### 构造器注入

在Ioc创建对象方式章节说过，略

### set方式注入

依赖注入：本质就是set注入，这里是指get/set的set

* **依赖：bean对象的创建依赖于容器**
* **注入：bean对象中的所有属性，由容器来注入**

#### 环境

Student.java

```java
public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbies;
    private Map<String, String> card;
    private Set<String> games;
    private String wife;
    private Properties info;
}
```

Address.java

```java
public class Address {
    private String address;
}
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="com.balance.pojo.Student">
        <!--普通属性注入 直接用value-->
        <property name="name" value="balance"/>
    </bean>

</beans>
```

测试

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student);
    }
}
```

**后面所有的注入其实都是跟java类型相关，比如map键值可以是对象可以是基本类型等等，都可以使用标签注入，使用时自己灵活应用**

#### bean注入

```xml
<bean id="address" class="com.balance.pojo.Address">
    <property name="address" value="balanceHome"/>
    <!--
		这样也可以
        <property name="address">
            <value>balanceHome</value>
        </property>
	-->
</bean>
<bean id="student" class="com.balance.pojo.Student">
    .........
    <!--第二种，Bean注入 使用ref-->
    <property name="address" ref="address"/>
</bean>
```

#### 数组注入

```xml
<bean id="student" class="com.balance.pojo.Student">
    .......
    <!--第三种 数组注入-->
    <property name="books">
        <array>
            <value>红楼梦</value>
            <value>红楼梦2</value>
            <value>红楼梦3</value>
        </array>
    </property>
</bean>
```

#### List注入

```xml
<!--第四种 List注入-->
<property name="hobbies">
    <list>
        <value>看电视</value>
        <value>打游戏</value>
        <value>敲代码</value>
    </list>
</property>
```

#### map注入

![image-20240108163912009](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401081639533.png)

```xml
<!--第五种 Map注入-->
<property name="card">
    <map>
        <entry key="身份证" value="342522"/>
        <entry key="身份证2" value="442522"/>
        <entry key="身份证3" value="542522"/>
    </map>
</property>
```

#### Set注入

```xml
<!--第六种 Set注入-->
<property name="games">
    <set>
        <value>hhhh</value>
    </set>
</property>
```

#### Null和空值注入

```xml
<!--第六种 NUll注入-->
<property name="wife">
    <null/>
</property>

<!--空值，直接字符串为空即可-->
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```

#### Properties注入

注意和map不同，key在里面，值在外面

```xml
<!--第七种 Properties注入-->
<property name="info">
    <props>
        <prop key="username">zhangsan</prop>
        <prop key="passwd">123456</prop>
    </props>
</property>
```

### 拓展方式注入

#### p命名空间注入

p命名空间注入就是无参构造属性注入

注意文件开头加上约束

```
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
<bean id="address" class="com.balance.pojo.Address" p:address="balanceP"/>

<bean id="address" class="com.balance.pojo.Address">
    <property name="address" value="balanceHome"/>
</bean>
```

#### c命名空间注入

c命名空间注入就是有参构造摒弃`constructor-arg`标签

注意文件开头加上约束,且类需要有参构造

```
xmlns:c="http://www.springframework.org/schema/c"
```

```xml
<bean id="address2" class="com.balance.pojo.Address" c:address="balanceC"/>

<bean id="address" class="com.balance.pojo.Address">
    <constructor-arg name="address" value="balance"/>
</bean>
```

### Bean的作用域-Scope

在Spring中，那些组成应用程序的主体及由Spring IoC容器所管理的对象，被称之为bean。简单地讲，**bean就是由IoC容器初始化、装配及管理的对象 .**

![image-20240108181043523](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401081810091.png)

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401081805656.webp)

几种作用域中，request、session作用域仅在基于web的应用中使用（不必关心你所采用的是什么web应用框架），只能用在基于web的Spring ApplicationContext环境。

#### Singleton

当一个bean的作用域为Singleton，那么Spring IoC容器中只会存在一个共享的bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例。Singleton是单例类型，就是在创建起容器时就同时自动创建了一个bean的对象，不管你是否使用，他都存在了，每次获取到的对象都是同一个对象。注意，Singleton作用域是Spring中的缺省作用域。要在XML中将bean定义成singleton，可以这样配置：

```xml
<bean id="ServiceImpl" class="cn.csdn.service.ServiceImpl" scope="singleton">
```

**单例模式也就是只new一次对象，之后getBean的都直接获取第一次new的对象**

测试：

```java
 @Test
 public void test03(){
     ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
     User user = (User) context.getBean("user");
     User user2 = (User) context.getBean("user2");//第二次getBean
     System.out.println(user==user2);
 }
```

#### Prototype

当一个bean的作用域为Prototype，表示一个bean定义对应多个对象实例。Prototype作用域的bean会导致在每次对该bean请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean()方法）时都会创建一个新的bean实例。Prototype是原型类型，它在我们创建容器的时候并没有实例化，**而是当我们获取bean的时候才会去创建一个对象，而且我们每次获取到的对象都不是同一个对象**。根据经验，对有状态的bean应该使用prototype作用域，而对无状态的bean则应该使用singleton作用域。在XML中将bean定义成prototype，可以这样配置：

```xml
 <bean id="account" class="com.foo.DefaultAccount" scope="prototype"/>  
  或者
 <bean id="account" class="com.foo.DefaultAccount" singleton="false"/>
```

原型模式也就是在之后的getBean时重新new一个对象

#### Request

当一个bean的作用域为Request，表示在一次HTTP请求中，一个bean定义对应一个实例；即每个HTTP请求都会有各自的bean实例，它们依据某个bean定义创建而成。该作用域仅在基于web的Spring ApplicationContext情形下有效。考虑下面bean定义：

```xml
 <bean id="loginAction" class=cn.csdn.LoginAction" scope="request"/>
```

针对每次HTTP请求，Spring容器会根据loginAction bean的定义创建一个全新的LoginAction bean实例，且该loginAction bean实例仅在当前HTTP request内有效，因此可以根据需要放心的更改所建实例的内部状态，而其他请求中根据loginAction bean定义创建的实例，将不会看到这些特定于某个请求的状态变化。当处理请求结束，request作用域的bean实例将被销毁。

#### Session

当一个bean的作用域为Session，表示在一个HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。考虑下面bean定义：

```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```

针对某个HTTP Session，Spring容器会根据userPreferences bean定义创建一个全新的userPreferences bean实例，且该userPreferences bean仅在当前HTTP Session内有效。与request作用域一样，可以根据需要放心的更改所创建实例的内部状态，而别的HTTP Session中根据userPreferences创建的实例，将不会看到这些特定于某个HTTP Session的状态变化。当HTTP Session最终被废弃的时候，在该HTTP Session作用域内的bean也会被废弃掉。

#### 注解配置作用域

`@Scope("xxxx")` 例如 @Scope("prototype")

## Bean的自动装配

什么是自动装配？

自动装配是使用spring满足bean依赖的一种方法，spring会在应用上下文中为某个bean寻找其依赖的bean。

Spring中bean有三种装配机制，分别是：

1. 在xml中显式配置【上面用的】；
2. 在java中显式配置【还未讲，略麻烦】；
3. 隐式的自动装配bean【**重要**】。

这里主要讲第三种：自动化的装配bean。

Spring的自动装配需要从两个角度来实现，或者说是两个操作：

1. 组件扫描(component scanning)：spring会自动发现应用上下文中所创建的bean；
2. 自动装配(autowiring)：spring自动满足bean之间的依赖，也就是我们说的IoC/DI；

组件扫描和自动装配组合发挥巨大威力，使得显示的配置降低到最少。

**推荐不使用自动装配xml配置 , 而使用注解 .**

### 环境搭建

1、新建一个项目

2、新建两个实体类，Cat Dog 都有一个叫的方法

```java
public class Cat {
    public void bark() {
        System.out.println("Cat meow");
    }
}

public class Dog {
    public void bark() {
        System.out.println("Dog bark");
    }
}
```

3、新建一个用户类 People

```
public class People {
    private Cat cat;
    private Dog dog;
    private String name;
    .......
}
```

4、编写Spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="cat" class="com.balance.pojo.Cat"/>
    <bean id="dog" class="com.balance.pojo.Dog"/>
    <bean id="people" class="com.balance.pojo.People">
        <property name="name" value="balance"/>
        <property name="cat" ref="cat"/>
        <property name="dog" ref="dog"/>
    </bean>
</beans>
```

5、测试

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        People people = (People) context.getBean("people");
        people.getCat().bark();
        people.getDog().bark();
    }
}
```

### byName

**autowire byName (按名称自动装配)**

测试：

1、修改bean配置，**增加一个属性 autowire="byName",去除之前在people bean中书写的属性**

```xml
<bean id="cat" class="com.balance.pojo.Cat"/>
<bean id="dog" class="com.balance.pojo.Dog"/>

<bean id="people" class="com.balance.pojo.People" autowire="byName">
    <property name="name" value="balance"/>
</bean>
```

2、再次测试，结果依旧成功输出！

3、我们将 cat 的bean id修改为 catXXX

4、再次测试， 执行时报空指针java.lang.NullPointerException。因为按byName规则找不对应set方法，真正的setCat就没执行，对象就没有初始化，所以调用时就会报空指针错误。

**小结：**

当一个bean节点带有 autowire byName的属性时。

1. **将查找其类中所有的set方法名，例如setCat，获得将set去掉并且首字母小写的字符串，即cat。**
2. **去spring容器中寻找是否有此字符串名称id的对象。**
3. **如果有，就取出注入；如果没有，就报空指针异常。**

### byType

**autowire byType (按类型自动装配)**

使用autowire byType首先需要保证：**同一类型的对象，在spring容器中唯一。如果不唯一，会报不唯一的异常。**

```
NoUniqueBeanDefinitionException
```

测试：

1、将user的bean配置修改一下 ： autowire="byType", **根据类型时不需要id**

```xml
<bean class="com.balance.pojo.Cat"/>
<bean class="com.balance.pojo.Dog"/>

<bean id="people" class="com.balance.pojo.People" autowire="byType">
	<property name="name" value="balance"/>
</bean>
```

2、测试，正常输出

3、再注册一个cat 的bean对象！

```xml
<bean class="com.balance.pojo.Cat"/>
<bean class="com.balance.pojo.Dog"/>
<bean id="cat2" class="com.balance.pojo.Cat"/>

<bean id="people" class="com.balance.pojo.People" autowire="byType">
	<property name="name" value="balance"/>
</bean>
```

4、测试，报错：NoUniqueBeanDefinitionException

或者修改id为cat2，因为是按类型装配，所以并不会报异常，也不影响最后的结果。甚至将id属性去掉，也不影响结果。

**总结：按类型装配和id无关，但需要类型唯一**

### 注解实现自动装配

jdk1.5开始支持注解，spring2.5开始全面支持注解。

准备工作：利用注解的方式注入属性。

1、在spring配置文件中引入context文件头

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
+    xmlns:context="http://www.springframework.org/schema/context"
+    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
+        http://www.springframework.org/schema/context
+        https://www.springframework.org/schema/context/spring-context.xsd">

+    <context:annotation-config/>

</beans>
```

2、开启属性注解支持！

```xml
<context:annotation-config/>
```

#### @Autowired

@Autowired默认是 **byType的，当有多个bean，就byName**

并且，当在属性上加上了`@Autowired`注解之后，set方法可以略去（不略去，idea会自动标灰，佐证了这一点）

People.java

```java
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }


    public Dog getDog() {
        return dog;
    }


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

beans.xml

```xml
<bean id="cat"  class="com.balance.pojo.Cat"/>
<bean id="cat2" class="com.balance.pojo.Cat"/>
<bean class="com.balance.pojo.Dog"/>

<bean id="people" class="com.balance.pojo.People">
    <property name="name" value="balance"/>
</bean>
```

**拓展**

**@Autowired(required=false) 说明：false，对象可以为null；true，对象必须存对象，不能为null。**

```java
//如果允许对象为null，设置required = false,默认为true
@Autowired(required = false)
private Cat cat;
```

#### @Qualifier

思考这样一个问题，如果

```java
<bean id="cat1"  class="com.balance.pojo.Cat"/>
<bean id="cat2" class="com.balance.pojo.Cat"/>
```

此时@Autowired无法自动装配，**因为byType不唯一，byName找不到名为cat的bean**

这时可以使用@Qualifier，**它根据byName的方式自动装配**

加上以下代码即可正确运行

```java
@Autowired
@Qualifier(value = "cat1")
private Cat cat;
```

#### @Resource

其实java中自带了注解@Resource可以实现以上注解功能，但此注解在jdk 1.8后被略去，它的运作方式如下：

- @Resource如有指定的name属性，先按该属性进行byName方式查找装配；
- 其次再进行默认的byName方式进行装配；
- 如果以上都不成功，则按byType的方式自动装配。
- 都不成功，则报异常。

#### 总结

**@Autowired与@Resource异同**：

1、@Autowired与@Resource都可以用来装配bean。**都可以写在字段上，或写在setter方法上**。

2、@Autowired默认**按类型装配**（属于spring规范），默认情况下必须要求依赖对象必须存在，如果要允许null 值，可以设置它的required属性为false，如：@Autowired(required=false) ，如果我们想使用名称装配可以结合@Qualifier注解进行使用

3、@Resource（属于J2EE规范），默认**按照名称进行装配**，名称可以通过name属性进行指定。如果没有指定name属性，当注解写在字段上时，默认取字段名进行按照名称查找，如果注解写在setter方法上默认取属性名进行装配。当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配。

它们的作用相同都是用注解方式注入对象，但执行顺序不同。@Autowired先byType，@Resource先byName。

#### 拓展

我们一般在属性上使用 @Autowired 注解进行自动注入，但是 spring 不推荐此种方式，更推荐使用构造器注入,使用构造器注入会造成一个问题，如果有很多个属性，我们都需要手动编写构造器，这无疑很繁琐且效率低，可以考虑使用 lombok 来实现优雅的注入，只需要加上注解即可

```java
@Service
@RequiredArgsConstructor //只会对被final修饰的属性进行注入
public class CartServiceImpl extends ServiceImpl<CartMapper, Cart> implements ICartService {

    private final ItemClient itemClient;
}
```

## 使用注解开发

在spring4之后，想要使用注解形式，必须得要引入aop的包

![image-20240114152536505](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401141525123.png)

在配置文件当中，还得要引入一个context约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
+      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
+       http://www.springframework.org/schema/context
+       http://www.springframework.org/schema/context/spring-context.xsd">
    
+    <context:annotation-config/>
    
</beans>
```

### bean的注解实现

我们之前都是在xml中使用 bean 的标签进行bean注入，但是实际开发中，我们一般都会使用注解！

1、配置扫描哪些包下的注解

```xml
<!--指定要扫描的包，这个包下注解（spring特有）就会生效，配完这个就无需配<context:annotation-config/>-->
<context:component-scan base-package="com.balance.pojo"/>
```

2、在指定包下编写类，增加注解

**Spring 将会自动扫描并注册带有 @Component 注解的类，使它们成为 Spring IoC 容器中的 Spring Bean。**

```java
//@Component 组件 等价于在xml <bean id="user" class="com.balance.pojo.User"/>
@Component
public class User {
    public String name = "balance";
}
```

3、测试

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        User user = (User) context.getBean("user");
        System.out.println(user.name);
    }
}
```

发现依旧成功输出了

### 属性注入注解实现

使用注解注入属性

1、可以不用提供set方法，**直接在属性名上添加**@value("值")

```java
@Component
public class User {
    //@Value("balance") 等价于 <property name="name" value="balance"/>
    @Value("balance")
    public String name;
}
```

2、如果提供了set方法，**也可以在set方法上添加**@value("值");

```java
@Component
public class User {
    public String name;
    
    public String getName() {
        return name;
    }
	@Value("balance")
    public void setName(String name) {
        this.name = name;
    }
}
```

### 衍生注解

我们这些注解，就是替代了在配置文件当中配置步骤而已！更加的方便快捷！

**@Component三个衍生注解**

为了更好的进行分层，Spring可以使用其它三个注解，功能一样

- **@Controller**：controller层
- **@Service**：service层
- **@Repository**：dao层

写上这些注解，就相当于将这个类交给Spring管理装配了！

### 自动装配注解

在Bean的自动装配已经讲过，此处略

### 作用域注解

**@scope**

- singleton：默认的，Spring会采用单例模式创建这个对象。关闭工厂 ，所有的对象都会销毁。
- prototype：原型模式。关闭工厂 ，所有的对象不会销毁。内部的垃圾回收机制会回收

```
@Controller("user")
@Scope("prototype")
public class User {
   @Value("balance")
   public String name;
}
```

### 总结

**XML与注解比较**

- XML可以适用任何场景 ，结构清晰，维护方便
- 注解不是自己的类使用不了，维护复杂，但开发简单方便

**xml与注解整合开发** ：推荐最佳实践

- xml管理Bean
- 注解完成属性注入
- 使用过程中， 可以不用扫描，**扫描是为了类上spring特有的注解**

```xml
<context:annotation-config/>  
```

作用：

- 进行注解驱动注册，从而使注解生效
- 用于激活那些已经在spring容器里注册过的bean上面的注解，也就是显示的向Spring注册
- 如果不扫描包，就需要手动配置bean
- 如果不加注解驱动，则注入的值为null！

## 使用java的方式配置Spring

完全不使用xml配置

**javaConfig 原来是 Spring 的一个子项目，它通过 Java 类的方式提供 Bean 的定义信息，在 Spring4 的版本， JavaConfig 已正式成为 Spring4 的核心功能 。**

### @Configuration

@Configuration 注解用于标识一个类是 Spring 配置类，它通常包含了用于配置 Spring IoC 容器的 Bean 定义。配置类中可以包含一些用 @Bean 注解标注的方法，这些方法用于创建和配置 Spring Bean

### @Bean

一个带有该注解的方法将产生一个由 Spring 管理的 Bean 对象。通常，@Bean 注解用在配置类中，表示该方法返回的对象将被注册为 Spring Bean,在默认情况下，`@Bean` 注解的方法名称将作为 Bean 的名称注册到 Spring 容器中。你可以通过 `name` 属性显式地指定 Bean 的名称。

```java
@Bean(name = "myBean")
public MyBean createMyBean() {
    return new MyBean();
}
```

### @ComponentScan

`@ComponentScan` 是 Spring 框架提供的一个注解，用于配置 Spring 容器在哪些包下扫描组件（包括 `@Component`、`@Service`、`@Repository`、`@Controller` 等注解标识的类），并将它们注册为 Spring Bean。

### @import

`@Import` 注解是 Spring 框架中用于**导入其他配置类或者注册额外的 Bean 定义**的注解。它可以用在配置类上，通过引入其他配置类的方式，实现模块化的配置，提高配置的可维护性和复用性。

相当于之前xml中的import标签

### 使用java类配置

创建一个User类

```java
public class User {
    @Value("balance")
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

再创建一个config配置类

```java
@Configuration
@ComponentScan("com.balance")
public class BalanceConfig {
	
    //注册一个Bean，相当于我们之前写的一个bean标签
    //方法的名字为id属性，返回值为class属性
    @Bean
    public User getUser(){
        return new User();
    }
}
```

测试

```java
public class MyTest {
    public static void main(String[] args) {
        //如果完全使用java配置类的方式去做，我们就只能通过AnnotationConfig上下文来获取容器，通过配置类的class对象加载
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(BalanceConfig.class);
        User getUser = (User) context.getBean("getUser");
        System.out.println(getUser.getName());
    }
}
```

## 代理模式

为什么要学习代理模式，因为AOP的底层机制就是动态代理！【SpringAOP和SpringMVC】

代理模式：

- 静态代理
- 动态代理

学习aop之前 , 我们要先了解一下代理模式！

![image-20240114170136992](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401141701304.png)

![image-20240114170352639](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401141703861.png)

### 静态代理

**静态代理角色分析**

- 抽象角色 : 一般使用接口或者抽象类来实现
- 真实角色 : 被代理的角色
- 代理 : 代理真实角色 ，代理真实角色后 , 一般会做一些附属的操作 .
- 客户 : 访问代理的人

Rent . java 即抽象角色

```java
//抽象角色：租房
public interface Rent {
   public void rent();
}
```

Host . java 即真实角色

```java
//真实角色: 房东，房东要出租房子
public class Host implements Rent{
   public void rent() {
       System.out.println("房屋出租");
  }
}
```

Proxy . java 即代理

```java
//代理角色：中介
public class Proxy implements Rent {

   private Host host;
   public Proxy() { }
   public Proxy(Host host) {
       this.host = host;
  }

   //租房
   public void rent(){
       seeHouse();
       host.rent();
       fare();
  }
   //看房
   public void seeHouse(){
       System.out.println("带房客看房");
  }
   //收中介费
   public void fare(){
       System.out.println("收中介费");
  }
}
```

Client . java 即客户

```java
//客户类，一般客户都会去找代理！
public class Client {
   public static void main(String[] args) {
       //房东要租房
       Host host = new Host();
       //中介帮助房东
       Proxy proxy = new Proxy(host);

       //你去找中介！
       proxy.rent();
  }
}
```

分析：在这个过程中，你直接接触的就是中介，就如同现实生活中的样子，你看不到房东，但是你依旧租到了房东的房子通过代理，这就是所谓的代理模式。

**静态代理的好处:**

- 可以使得我们的真实角色更加纯粹 . 不再去关注一些公共的事情 .
- 公共的业务由代理来完成 . 实现了业务的分工 ,
- 公共业务发生扩展时变得更加集中和方便 .

缺点 :

- 类多了 , 多了代理类 , 工作量变大了，开发效率降低 .

我们想要静态代理的好处，又不想要静态代理的缺点，所以 , 就有了动态代理 !

#### 加深理解

练习步骤：

1、创建一个抽象角色，比如平时做的用户业务，抽象起来就是增删改查！

```java
//抽象角色：增删改查业务
public interface UserService {
   void add();
   void delete();
   void update();
   void query();
}
```

2、我们需要一个真实对象来完成这些增删改查操作

```java
//真实对象，完成增删改查操作的人
public class UserServiceImpl implements UserService {

   public void add() {
       System.out.println("增加了一个用户");
  }

   public void delete() {
       System.out.println("删除了一个用户");
  }

   public void update() {
       System.out.println("更新了一个用户");
  }

   public void query() {
       System.out.println("查询了一个用户");
  }
}
```

3、需求来了，现在我们需要增加一个日志功能，怎么实现！

- 思路1 ：在实现类上增加代码 【麻烦！】
- 思路2：使用代理来做，能够不改变原来的业务情况下，实现此功能就是最好的了！

4、设置一个代理类来处理日志！代理角色

```java
//代理角色，在这里面增加日志的实现
public class UserServiceProxy implements UserService {
   private UserServiceImpl userServiceImpl;

   public void setUserServiceImpl(UserServiceImpl userServiceImpl) {
       this.userServiceImpl = userServiceImpl;
  }

   public void add() {
       log("add");
       userServiceImpl.add();
  }

   public void delete() {
       log("delete");
       userServiceImpl.delete();
  }

   public void update() {
       log("update");
       userServiceImpl.update();
  }

   public void query() {
       log("query");
       userServiceImpl.query();
  }

   public void log(String msg){
       System.out.println("执行了"+msg+"方法");
  }

}
```

5、测试访问类：

```java
public class Client {
   public static void main(String[] args) {
       //真实业务
       UserServiceImpl userServiceImpl = new UserServiceImpl();
       //代理类
       UserServiceProxy proxy = new UserServiceProxy();
       //使用代理类实现日志功能！
       proxy.setUserServiceImpl(userServiceImpl);

       proxy.add();
  }
}
```

**我们在不改变原来的代码的情况下，实现了对原有功能的增强，这是AOP中最核心的思想**

![image-20240124161225961](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401241612625.png)

### 动态代理

- 动态代理的角色和静态代理的一样 ，
- 动态代理的代理类是动态生成的 ，静态代理的代理类是我们提前写好的
- 动态代理分为两类 : 一类是**基于接口动态代理** , 一类是**基于类的动态代理**
- - 基于接口的动态代理----JDK动态代理（原生）
  - 基于类的动态代理--cglib
  - 现在用的比较多的是 javasist 即java字节码 来实现动态代理

此处以 JDK动态代理 实现为例

**JDK的动态代理需要了解两个类 InvocationHandler 和 Proxy**

![image-20240124162039860](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401241620409.png)

Proxy

![image-20240124162117207](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401241621745.png)

代码实现

Rent . java 即抽象角色

```java
//抽象角色：租房
public interface Rent {
   public void rent();
}
```

Host . java 即真实角色

```java
//真实角色: 房东，房东要出租房子
public class Host implements Rent{
   public void rent() {
       System.out.println("房屋出租");
  }
}
```

ProxyInvocationHandler. java 即代理

```java
public class ProxyInvocationHandler implements InvocationHandler {
   private Rent rent;

   public void setRent(Rent rent) {
       this.rent = rent;
  }

   //生成代理类，重点是第二个参数，获取要代理的抽象角色！之前都是一个角色，现在可以代理一类角色
   public Object getProxy(){
       return Proxy.newProxyInstance(this.getClass().getClassLoader(),
               rent.getClass().getInterfaces(),this);
  }

   // proxy : 代理类 method : 代理类的调用处理程序的方法对象.
   // 处理代理实例上的方法调用并返回结果
   @Override
   public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
       seeHouse();
       //核心：本质利用反射实现！
       Object result = method.invoke(rent, args);
       fare();
       return result;
  }

   //看房
   public void seeHouse(){
       System.out.println("带房客看房");
  }
   //收中介费
   public void fare(){
       System.out.println("收中介费");
  }

}
```

Client . java

```java
//租客
public class Client {

   public static void main(String[] args) {
       //真实角色
       Host host = new Host();
       //代理实例的调用处理程序
       ProxyInvocationHandler pih = new ProxyInvocationHandler();
       pih.setRent(host); //将真实角色放置进去！
       Rent proxy = (Rent)pih.getProxy(); //动态生成对应的代理类！
       proxy.rent();
  }

}
```

#### 重点解释

```
Proxy.newProxyInstance(x,x,x);
第一个参数：一个类加载器，用于确定代理类的类加载环境
第二个参数：希望代理对象实现的接口，即被代理的接口
第三个参数：指定在代理对象的方法调用时要执行的逻辑
```

我们传入第一个参数为`this.getClass().getClassLoader()`，因为该方法调用会返回一个代理对象，第一个参数用于保证代理对象的类加载环境和当前一致，从而可以在执行被代理对象方法前执行invoke方法。

第二个传入的参数为`rent.getClass().getInterfaces()`，用于指定代理类要实现被代理的接口

第三个传入的参数为`this`,为了指定在代理对象的方法调用时要执行的逻辑,即invoke方法具体实现类

**一个动态代理类代理的是一个接口, 一个动态代理可以代理多个类，只要实现的是同一个接口**



## AOP

Aspect Oriented Programming：面向切面编程

### 什么是AOP

AOP通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401271537055.webp)

### Spring中的AOP

提供声明式事务；允许用户自定义切面

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志 , 安全 , 缓存 , 事务等等 ....。**比如需要在中间加日志**
- 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，它是一个类。**比如日志类**
- 通知（Advice）：切面必须要完成的工作。即，它是类中的一个方法。**比如日志类中的一个方法**
- 目标（Target）：被通知对象。**比如需要加日志的对象**
- 代理（Proxy）：向目标对象应用通知之后创建的对象。**比如代理对象**
- 切入点（PointCut）：切面通知 执行的 “地点”。**比如日志方法可以在哪里调用**
- 连接点（JointPoint）：与切入点匹配的执行点。**比如日志方法的具体调用，连接点是切入点的实例**

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401271546330.webp)

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice:

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401271547477.webp)

Aop 在 不改变原有代码的情况下 , 去增加新的功能 .

### Spring实现AOP

使用AOP织入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
   <groupId>org.aspectj</groupId>
   <artifactId>aspectjweaver</artifactId>
   <version>1.9.4</version>
</dependency>
```

#### 方式一 springApi

![image-20240227140042382](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240227140042382.png)

首先编写我们的业务接口和实现类

```java
public interface UserService {
    void add();
    void delete();
    void update();
    void query();
}
public class UserServiceImpl implements UserService{

    @Override
    public void add() {
        System.out.println("添加用户");
    }

    @Override
    public void delete() {
        System.out.println("删除用户");
    }

    @Override
    public void update() {
        System.out.println("更新用户");
    }

    @Override
    public void query() {
        System.out.println("查询用户");
    }
}
```

然后去写我们的增强类 , 我们编写两个 , 一个前置增强 一个后置增强,其实就是前置日志和后置日志

```java
package com.balance.log;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class Log implements MethodBeforeAdvice {
     /**
          * 在方法执行之前调用的方法。
          *
          * @param  method   被调用的方法
          * @param  objects  传递给方法的参数
          * @param  o        目标对象
          * @throws Throwable 如果在执行之前发生错误
     */
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println(o.getClass().getName() + "调用了" + method.getName() + "方法");
    }
}
```

```java
package com.balance.afterlog;

import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {
    /**
      * 在成功调用目标对象中的方法之后调用的方法。
      *
      * @param  o       方法的返回值
      * @param  method  正在调用的 Method 对象
      * @param  objects 方法的参数
      * @param  o1      目标对象
      * @throws Throwable  如果发生错误
      */
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println(o1.getClass().getName() + "调用了" + method.getName() + "方法"+"返回值为"+o);
    }
}
```

最后去spring的文件中注册 , 并实现aop切入实现 , 注意导入约束 .

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--注册bean-->
    <bean id="log" class="com.balance.log.Log"/>
    <bean id="afterLog" class="com.balance.afterlog.AfterLog"/>
    <bean id="userService" class="com.balance.service.UserServiceImpl"/>
    <!--aop的配置-->
    <aop:config>
        <!--
             execution(修饰符  返回值  包名.类名/接口名.方法名(参数列表))
             注意老师忽略了修饰符
            (..)可以代表所有参数,(*)代表一个参数,(*,String)代表第一个参数为任何值,第二个参数为String类型.
            切入点 expression:表达式匹配要执行的方法
        -->
        <aop:pointcut id="pointcut" expression="execution(public * com.balance.service.UserServiceImpl.*(..))"/>
        <!--执行环绕; advice-ref执行方法  pointcut-ref切入点-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>

</beans>
```

测试

```java
public class Test {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        UserService userService = (UserService) context.getBean("userService");
        userService.add();
    }
}
```

#### 方式二 自定义类

目标业务类不变依旧是userServiceImpl

第一步 : 写我们自己的一个切入类

```java
public class Diy {
    public void before() {
        System.out.println("方法执行前");
    }
    public void after() {
        System.out.println("方法执行后");
    }
}
```

去spring中配置

```xml
<bean id="diy" class="com.balance.diy.Diy"/>

<aop:config>
    <aop:aspect ref="diy">
        <aop:pointcut id="pointcut" expression="execution(public * com.balance.service.UserServiceImpl.*(..))"/>
        <aop:before method="before" pointcut-ref="pointcut"/>
        <aop:after method="after" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```

测试

```java
public class Test {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        UserService userService = (UserService) context.getBean("userService");
        userService.add();
    }
}
```

**可以看到第二种更简单，但是第二种是我们自定义的方法，没有springApi中方法功能强大**

#### 方式三 使用注解

第一步：编写一个注解实现的增强类

```java
@Aspect
public class AnnotationPointCut {
    @Before("execution(public * com.balance.service.UserServiceImpl.*(..))")
    //多个切入execution表达式可以使用 ||  && 运算符
    public void before() {
        System.out.println("方法执行前  AnnotationPointCut");
    }
    @After("execution(public * com.balance.service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("方法执行后   AnnotationPointCut");
    }
}
```

第二步：在Spring配置文件中，注册bean，并增加支持注解的配置

```xml
<bean id="annotationPointCut" class="com.balance.diy.AnnotationPointCut"/>
<aop:aspectj-autoproxy/>
```

* 通过aop命名空间的`<aop:aspectj-autoproxy/>`声明自动为spring容器中那些配置@aspectJ切面的bean创建代理，织入切面。
  当然，spring 在内部依旧采用AnnotationAwareAspectJAutoProxyCreator进行自动代理的创建工作，但具体实现的细节已经被<aop:aspectj-autoproxy />隐藏起来了

* <aop:aspectj-autoproxy />有一个proxy-target-class属性，默认为false，表示使用jdk动态代理织入增强。
  当为<aop:aspectj-autoproxy  poxy-target-class="true"/>时，表示使用CGLib动态代理技术织入增强。
  不过即使proxy-target-class设置为false，如果目标类没有声明接口，则spring将自动使用CGLib动态代理

![image-20240407210023703](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240407210023703.png)

## 整合Mybatis

### 导入相关jar包

mybatis集成spring版本：

如果spring是6以上版本 ，那么JDK请用17  并且mybatis-spring的版本3.0以上 tomcat需要是10版本 并且servlet版本是Jakarta  不是javax

如果是spring4、5版本  那么JDK8以上，但是mybatis-spring版本只能2.0以上不能是3.0以上 tomcat是9版本

否则报错 

```xml
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
        <!--spring相关依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.20</version>
        </dependency>
        <!--spring操作数据库所需-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.20</version>
        </dependency>
        <!--spring实现AOP所需-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
        <!--spring整合mybatis所需-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>
   	   <!--lomb-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
        </dependency>
    </dependencies>
```

配置Maven静态资源过滤问题！

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

### 回忆流程

先建pojo类

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

实现mybatis的配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <typeAliases>
        <package name="com.balance.pojo"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="com/balance/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

UserMapper接口编写

```java
public interface UserMapper {
    List<User> getAllUsers();
}
```

接口对应的Mapper映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.balance.mapper.UserMapper">
    <select id="getAllUsers" resultType="user">
        select * from users
    </select>
</mapper>
```

测试

```java
public class Demo {
    @Test
    public void test() throws IOException {
        String res="mybatis-config.xml";
        InputStream in = Resources.getResourceAsStream(res);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> allUsers = mapper.getAllUsers();
        for (User allUser : allUsers) {
            System.out.println(allUser);
        }
        sqlSession.close();
    }
}
```

### MyBatis-Spring学习

引入Spring之前需要了解mybatis-spring包中的一些重要类；

http://www.mybatis.org/spring/zh/index.html

 要和 Spring 一起使用 MyBatis，需要在 Spring 应用上下文中定义至少两样东西：

* 一个 SqlSessionFactory 和至少一个数据映射器类。

在 MyBatis-Spring 中，可使用SqlSessionFactoryBean来创建 SqlSessionFactory。要配置这个工厂 bean，只需要把下面代码放在 Spring 的 XML 配置文件中：

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
 <property name="dataSource" ref="dataSource" />
</bean>
```

注意：**SqlSessionFactory需要一个 DataSource（数据源）**。这可以是任意的 DataSource，只需要和配置其它 Spring 数据库连接一样配置它就可以了。

在基础的 MyBatis 用法中，是通过 SqlSessionFactoryBuilder 来创建 SqlSessionFactory 的。而在 MyBatis-Spring 中，则使用 SqlSessionFactoryBean 来创建。

**一个常用的属性是 configLocation，它用来指定 MyBatis 的 XML 配置文件路径**。它在需要修改 MyBatis 的基础配置非常有用。通常，基础配置指的是 < settings> 或 < typeAliases>元素。

**SqlSessionTemplate 是 MyBatis-Spring 的核心**。作为 SqlSession 的一个实现，这意味着可以使用它无缝代替你代码中已经在使用的 SqlSession。

模板可以参与到 Spring 的事务管理中，并且由于其是线程安全的，可以供多个映射器类使用，你应该总是用 SqlSessionTemplate 来替换 MyBatis 默认的 DefaultSqlSession 实现。

可以使用 SqlSessionFactory 作为构造方法的参数来创建 SqlSessionTemplate 对象。

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
 <constructor-arg index="0" ref="sqlSessionFactory" />
</bean>
```

现在，这个 bean 就可以直接注入到你的 DAO bean 中了。你需要在你的 bean 中添加一个 SqlSession 属性，就像下面这样：

```java
public class UserDaoImpl implements UserDao {

 private SqlSession sqlSession;

 public void setSqlSession(SqlSession sqlSession) {
   this.sqlSession = sqlSession;
}

 public User getUser(String userId) {
   return sqlSession.getMapper...;
}
}
```

按下面这样，注入 SqlSessionTemplate：

```xml
<bean id="userDao" class="org.mybatis.spring.sample.dao.UserDaoImpl">
 <property name="sqlSession" ref="sqlSession" />
</bean>
```

#### 实现方式一

其实就是把mybatis配置的东西用spring实现

新建Spring数据库配置文件spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

配置数据源替换mybaits的数据源

```xml
<!--首先配好数据源-->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/test?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</bean>
```

配置SqlSessionFactory，关联MyBatis

```xml
<!--然后创建sqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--关联mybatis配置文件，以及注册mapper映射-->
    <property name="configLocation" value="mybatis-config.xml"/>
    <property name="mapperLocations" value="com/balance/mapper/*.xml"/>
</bean>
```

注册sqlSessionTemplate，关联sqlSessionFactory；**这其实就是sqlsession**，spring称之为xxxTemplate

```xml
<!--构建-->
<bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
    <!--构造器注入-->
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

增加Dao接口的实现类；私有化sqlSessionTemplate

```java
public class UserMapperImpl implements UserMapper {
    private SqlSessionTemplate sqlSessionTemplate;

    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    @Override
    public List<User> getAllUsers() {
        UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
        return mapper.getAllUsers();
    }
}
```

再新建Spring总的配置文件 applicationContext.xml ，注册实现类

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!--之后就可以分任务配置，然后导入到一个配置文件中-->
    <import resource="spring-dao.xml"/>
    
    <bean id="userMapperImpl" class="com.balance.mapper.UserMapperImpl">
        <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
    </bean>
</beans>
```

测试

```java
public class Demo {
    @Test
    public void test() throws IOException {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapperImpl userMapperImpl = context.getBean("userMapperImpl", UserMapperImpl.class);
        List<User> allUsers = userMapperImpl.getAllUsers();
        for (User allUser : allUsers) {
            System.out.println(allUser);
        }
    }
}
```

现在我们的Mybatis配置文件的状态！发现都可以被Spring整合！

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    
    <!--此后mybatis配置文件只剩一些别名，日志之类的配置-->
    <typeAliases>
        <package name="com.balance.pojo"/>
    </typeAliases>
    
</configuration>
```

#### 实现方式二

mybatis-spring1.2.3版以上的才有这个 .

官方文档截图 :

dao继承Support类 , 直接利用 getSqlSession() 获得 , 然后直接注入SqlSessionFactory .

 **比起方式1 , 不需要管理SqlSessionTemplate , 而且对事务的支持更加友好 . 可跟踪源码查看**

[![img](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7JXnZ2vicYHdibF3mvk0w89Cq6qzPguHd15ribJeF3ickXAy98VG8YFjxwftEdsvvCLWkWLPeiaiaVibbPxQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7JXnZ2vicYHdibF3mvk0w89Cq6qzPguHd15ribJeF3ickXAy98VG8YFjxwftEdsvvCLWkWLPeiaiaVibbPxQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其实就是在其基础上省略了

```xml
<!--构建-->
<bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
    <!--构造器注入-->
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

以及改进了

```java
public class UserMapperImpl implements UserMapper {
    private SqlSessionTemplate sqlSessionTemplate;

    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    @Override
    public List<User> getAllUsers() {
        UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
        return mapper.getAllUsers();
    }
}
```

将我们上面写的UserMapperImpl修改一下

```java
public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper {
    @Override
    public List<User> getAllUsers() {
        return getSqlSession().getMapper(UserMapper.class).getAllUsers();
    }
}
```

修改bean的配置 applicationContext

```xml
<bean id="userMapperImpl2" class="com.balance.mapper.UserMapperImpl">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

**现在就可以删除 spring-dao中的SqlsessionTemplate了**

测试

```java
@Test
public void test2() throws IOException {
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserMapperImpl mapper = context.getBean("userMapperImpl2", UserMapperImpl.class);
    List<User> allUsers = mapper.getAllUsers();
    for (User allUser : allUsers) {
        System.out.println(allUser);
    }
}
```

**总结 : 整合到spring以后可以完全不要mybatis的配置文件，除了这些方式可以实现整合之外，我们还可以使用注解来实现，这个等我们后面学习SpringBoot的时候还会测试整合！**

## 事务

### 回顾事务

- 事务在项目开发过程非常重要，涉及到数据的一致性的问题，不容马虎！
- 事务管理是企业级应用程序开发中必备技术，用来确保数据的完整性和一致性。

**事务就是把一系列的动作当成一个独立的工作单元，这些动作要么全部完成，要么全部不起作用。**

**事务四个属性ACID**

1. 原子性（atomicity）
   1. 事务是原子性操作，由一系列动作组成，事务的原子性确保动作要么全部完成，要么完全不起作用
2. 一致性（consistency）
   1. 一旦所有事务动作完成，事务就要被提交。数据和资源处于一种满足业务规则的一致性状态中
3. 隔离性（isolation）
   1. 可能多个事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏
4. 持久性（durability）
   1. 事务一旦完成，无论系统发生什么错误，结果都不会受到影响。通常情况下，事务的结果被写到持久化存储器中

### 案例

将上面的代码拷贝到一个新项目中

在之前的案例中，我们给userMapper接口新增两个方法，删除和增加用户；

```java
//添加一个用户
int addUser(User user);

//根据id删除用户
int deleteUser(int id);
```

mapper文件，我们故意把 deletes 写错，测试！

```xml
<insert id="addUser" parameterType="com.balance.pojo.User">
    insert into users (id,name,password) values (#{id},#{name},#{password})
</insert>
<delete id="deleteUser" parameterType="int">
    deletes from users where id = #{id}
</delete>
```

编写接口的实现类，在实现类中，我们去操作一波

```java
public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper {
    @Override
    public List<User> getAllUsers() {
        UserMapper mapper = getSqlSession().getMapper(UserMapper.class);
        User xiaozhang = new User(4, "xiaozhang", "123");
        mapper.addUser(xiaozhang);
        mapper.deleteUser(4);
        return mapper.getAllUsers();
    }

    @Override
    public int addUser(User user) {
        return getSqlSession().getMapper(UserMapper.class).addUser(user);
    }

    @Override
    public int deleteUser(int id) {
        return getSqlSession().getMapper(UserMapper.class).deleteUser(id);
    }
}
```

测试

```java
public class MyTest {
    @Test
    public void test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapperImpl", UserMapper.class);
        List<User> allUsers = userMapper.getAllUsers();
        System.out.println(allUsers);
    }
}
```

报错：sql异常，delete写错了

结果 ：插入成功！

没有进行事务的管理；我们想让他们都成功才成功，有一个失败，就都失败，我们就应该需要**事务！**

以前我们都需要自己手动管理事务，十分麻烦！

但是Spring给我们提供了事务管理，我们只需要配置即可；

### Spring中的事务管理

Spring在不同的事务管理API之上定义了一个抽象层，使得开发人员不必了解底层的事务管理API就可以使用Spring的事务管理机制。Spring支持编程式事务管理和声明式的事务管理。

**编程式事务管理**

- 将事务管理代码嵌到业务方法中来控制事务的提交和回滚
- 缺点：必须在每个事务操作业务逻辑中包含额外的事务管理代码

**声明式事务管理** **主要使用**

- 一般情况下比编程式事务好用。
- 将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理。
- 将事务管理作为横切关注点，通过aop方法模块化。Spring中通过Spring AOP框架支持声明式事务管理。

**使用Spring管理事务，注意头文件的约束导入 : tx**

```xml
xmlns:tx="http://www.springframework.org/schema/tx"

http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd">
```

**事务管理器**

- 无论使用Spring的哪种事务管理策略（编程式或者声明式）事务管理器都是必须的。
- 就是 Spring的核心事务管理抽象，管理封装了一组独立于技术的方法。

**JDBC事务**

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="dataSource" />
</bean>
```

**配置好事务管理器后我们需要去配置事务的通知**

```xml
<!--配置事务通知-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!--配置哪些方法使用什么样的事务,配置事务的传播特性 REQUIRED表明有事务就使用，没有就新建-->
        <!--这里的name是以其值开头的方法名都会使用-->
        <tx:method name="add" propagation="REQUIRED"/>
        <tx:method name="delete" propagation="REQUIRED"/>
        <tx:method name="update" propagation="REQUIRED"/>
        <tx:method name="search*" propagation="REQUIRED"/>
        <tx:method name="get" read-only="true"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```

**spring事务传播特性：**

事务传播行为就是多个事务方法相互调用时，事务如何在这些方法间传播。spring支持7种事务传播行为：

- propagation_requierd：如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中，这是最常见的选择。
- propagation_supports：支持当前事务，如果没有当前事务，就以非事务方法执行。
- propagation_mandatory：使用当前事务，如果没有当前事务，就抛出异常。
- propagation_required_new：新建事务，如果当前存在事务，把当前事务挂起。
- propagation_not_supported：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- propagation_never：以非事务方式执行操作，如果当前事务存在则抛出异常。
- propagation_nested：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与propagation_required类似的操作

Spring 默认的事务传播行为是 PROPAGATION_REQUIRED，它适合于绝大多数的情况。

**事务管理**

在Java Spring框架中，以下是关于事务管理的三个重要配置：

1. **tx:advice**: 这个配置用于定义事务通知的行为。在Spring中，通知是在方法调用前后执行的代码，例如，执行事务的代码。tx:advice配置定义了这些通知的类型、顺序和条件。
2. **tx:annotation-driven**: 这个配置是用来启用基于注解的事务管理。通过在方法上添加注解，如`@Transactional`，就可以标识哪些方法应该在调用时启用事务管理。tx:annotation-driven配置告诉Spring要扫描这些注解，并在运行时为它们创建相应的事务代理。
3. **tx:jta-transaction-manager**: 这个配置是用于配置Spring事务管理器，以便与Java Transaction API (JTA) 兼容。JTA是Java EE平台的一部分，它提供了一种在分布式环境下管理事务的标准方法。tx:jta-transaction-manager配置告诉Spring使用JTA事务管理器来管理分布式事务。

**配置AOP**

导入aop的头文件！

```xml
<!--配置aop织入事务-->
<aop:config>
    <aop:pointcut id="txPointcut" expression="execution(* com.balance.mapper.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
</aop:config>
```

**进行测试**

删掉刚才插入的数据，再次测试！

```java
public class MyTest {
    @Test
    public void test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapperImpl", UserMapper.class);
        List<User> allUsers = userMapper.getAllUsers();
        System.out.println(allUsers);
    }
}
```

为什么需要配置事务？

- 如果不配置，就需要我们手动提交控制事务；
- 事务在项目开发过程非常重要，涉及到数据的一致性的问题，不容马虎！
