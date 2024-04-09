# Mybatis

## 简介

### 什么是Mybatis

- MyBatis 是一款优秀的持久层框架，是一个半自动化的 ORM 框架(ORM 是Object Relational Mapping 的缩写，译为“对象关系映射”框架。 所谓的ORM 框架就是**一种为了解决面向对象与关系型数据库中数据类型不匹配的技术**，)

- 不同于其他的对象关系映射框架，MyBatis 并未将 Java 对象和数据库表关联，而是将 Java 方法与 SQL 语句关联。

- 它支持自定义 SQL、存储过程以及高级映射。

- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
  - 对**jdbc的操作数据库的过程进行封装，使开发者只需要关注SQL本身**，而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码。

- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
  - Mybatis通过xml或注解的方式将要执行的各种statement（statement、preparedStatemnt）配置起来，并通过java对象和statement中的sql进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射成java对象并返回。
- **MyBatis 的真正强大在于它的(SQL)语句映射**

### 数据持久化

- ***持久化（Persistence） ：***将程序的数据的在持久状态和瞬时状态间转化的机制。即将数据（如内存中的对象）存储在可持久保存的存储介质上（如数据库、磁盘、文件系统等）
  - “持久化”这个概念是和“暂时”等概念相对的，数据在计算机中有一般有两个存储地，内存为暂存，因为电源关机就会数据丢失，如果需要反复使用，就要持久保存，实现持久化了。

  - 内存：断电即失

  - 数据持久化 : 将内存中的数据模型转换为存储模型,以及将存储模型转换为内存中的数据模型的统称.

    - 数据模型可以是任何数据结构或对象模型；存储模型可以是关系模型、XML、二进制流等。

    - cmp和Hibernate只是对象模型到关系模型之间转换的不同实现。只不过对象模型和关系模型应用广泛，所以就会误认为数据持久化就是对象模型到关系型数据库的转换罢了。

  - 持久化的主要应用是将内存中的对象存储在关系型的数据库中，当然也可以存储在磁盘文件中、XML数据文件中等等。

  - JDBC就是一种持久化机制。文件IO也是一种持久化机制。

  - 生活例子：冷藏、罐头

- **持久化对象 **：是指在程序中表示的对象，可以存储在持久化存储介质（如数据库、文件系统等）中并能够长期保留。简单来说，持久化对象是指在内存中的对象通过某种方式保存到外部存储介质中，并且在需要时可以重新加载到内存中进行使用。
- ***持久层（Persistence Layer）：***专注于实现数据持久化应用领域的某个特定系统的一个逻辑层面，将数据使用者和数据实体相关联。(就是用于完成持久化工作的代码块（dao 层【DAO（Data Access Object）】）)

**为什么需要持久化？**

之所以需要持久化，是由于内存自身缺陷导致。我们知道，内存在遇到某些外界因素影响后会丢失，但是我们的一些数据是绝对不能丢失的，但我们又无法保证不收外界因素影响。

同时内存成本较高，比起硬盘、光盘等外存，其价格要高上几个数量级，而且维持成本也较高。在这种情况下，我们不得不寻求另一种方案来存储数据对象，而持久化就是其中的一种选择，我们能够通过持久化将数据缓存到外存，从而降低成本。
### Mybatis优点

- **简单易学**：自身小且简单，无任何第三方依赖（最简单安装只要两个jar文件+配置几个sql映射文件）；
- **灵活**：MyBatis 不会对应用程序或数据库的现有设计强加任何影响，写在 XML 中，便于统一管理和优化；
- **解除 SQL 与代码程序的耦合**：通过提供 DAO 层，将业务逻辑与数据访问逻辑分离，使系统设计更加清晰、易维护、易于单元测试。sql和代码的分离，提高了程序的可维护性；
- **提供 XML 标签，支持编写动态 SQL；**
- 提供映射标签，支持对象与数据库的ORM字段关系映射。

- 提供对象关系映射标签，支持对象关系组建维护。

## 第一个Mybatis程序

### 创建maven项目

需要删除src目录，主项目不需要，后导入maven依赖

pom.xml

```xml
<!--父工程-->
<dependencies>
    <!--mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.31</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.13</version>
    </dependency>
    <!--junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 创建module

配置mybatis-config.xml,**注意mysql8.0后driver为com.mysql.cj.jdbc.Driver**

**同时注意时区等问题，具体参考下面代码**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/jdbc01?useSSL=true&amp;characterEncoding=UTF-8&amp;useUnicode=true&amp;serverTimezone=GMT"/>
                <property name="username" value="root"/>
                <property name="password" value=""/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

编写Mybatis工具类，之后可以重复使用

```java
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    }

    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
    // 你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。例如：
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

![image-20231207183644883](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312071836435.png)

### 编写代码

#### 1.编写实体类

新建pojo包，包下建User类

**这里注意和数据库中字段一一对应，并给出无参，有参构造，重写toString方法，这里贴的代码略去了**

```java
public class User {
    private int id;
    private String name;
    private String password;
    private String email;
    private Date birthday;
}
```

#### 2.Dao接口，即Mapper接口

Data Access Object，数据存取对象，所谓Dao层就是封装对数据库的访问。

```java
public interface UserMapper {
    public List<User> getUserList();
}
```

#### 3.编写UserMapper.xml完成与接口的映射

**注意：这里的 Id 就是你接口中定义的方法名，这里的 resultType 就是你编写的与数据库对应的实体类，jdbc01是数据库名，users是表名**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.balance.dao.UserMapper">
    <select id="getUserList" resultType="com.balance.pojo.User">
        select * from jdbc01.users
    </select>
</mapper>
```

#### 4.在mybatis-config.xml注册UserMapper.xml

```xml
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/jdbc01?useSSL=true&amp;characterEncoding=UTF-8&amp;useUnicode=true&amp;serverTimezone=GMT"/>
                <property name="username" value="root"/>
                <property name="password" value=""/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/balance/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

#### 5.测试

先通过MybatisUtils工具类获取到sqlSession对象，利用sqlSession对象的getMapper方法获得UserMapper接口对象，之后调用方法完成查找。

**注意：不要忘记关闭sqlSession**

```java
public class UserDaoTest {
    @Test
    public void test(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }

        sqlSession.close();
    }
}
```

![image-20231207184746806](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312071847835.png)

#### 注意事项

因为我们的xml并没有写在resources目录下，会导致资源无法导出，可以在pom.xml中加上

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
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

还可以设置一下编码和默认项目SQL，防止奇怪的问题

![image-20231207185233325](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312071852185.png)

![image-20231207185145669](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312071851735.png)

## CRUD

### namespace

namespace中的包名要和Dao/Mapper 接口的包名一致！

1个Dao接口类对应1个mapper，也对应1个namespace，

1个Dao接口中的方法对应1个namespace中一个SQL语句

### 增删改查

- id：对应的namespace接口中的方法名
- resultType：SQL语句执行的返回值——承载数据的实体类
- parameterType：参数类型

#### 编写接口

```java
public interface UserMapper {
    List<User> getUserList();

    //增加用户
    int addUser(User user);

    //修改用户信息
    int updateUser(User user);

    //删除用户
    int deleteUser(int id);
}
```

**注意**：接口方法**默认是用public abstract修饰**，可省略

这里增删改会返回数据表中受影响的行数，故可以用int接收，若大于0，代表操作成功

#### 编写对应mapper.xml

```xml
<mapper namespace="com.balance.dao.UserMapper">
    <select id="getUserList" resultType="com.balance.pojo.User">
        select * from jdbc01.users
    </select>

    <update id="updateUser" parameterType="com.balance.pojo.User">
        UPDATE jdbc01.users SET name=#{name} WHERE id=#{id}
    </update>

    <insert id="addUser" parameterType="com.balance.pojo.User">
        INSERT INTO jdbc01.users VALUES(#{id},#{name},#{password},#{email},#{birthday})
    </insert>

    <delete id="deleteUser" parameterType="int">
        DELETE FROM jdbc01.users WHERE id=#{id}
    </delete>
</mapper>
```

**注意**：#{}为占位符，里面填接口中具体方法的参数名，**若参数为实体类，可以直接写字段名称,若参数为map，直接写键名**

#### 测试

```java
public class UserDaoTest {
    @Test
    public void test(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }

        sqlSession.close();
    }


    @Test
    public void add(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int hhh6 = mapper.addUser(new User(6, "hhh6", "123456", "hhh6@qq.com",new Date(Instant.now().toEpochMilli())));
        if(hhh6>0){
            System.out.println("插入用户成功");
            sqlSession.commit();
        }
        sqlSession.close();
    }

    @Test
    public void delete(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int i = mapper.deleteUser(6);
        if(i>0){
            System.out.println("删除用户成功");
            sqlSession.commit();
        }
        sqlSession.close();
    }

    @Test
    public void update() throws ParseException {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        long time = new SimpleDateFormat("yyyy-MM-dd").parse("2000-08-16").getTime();
        int balance777 = mapper.updateUser(new User(1,"balance777","123456","balance@qq.com",new Date(time)));
        if(balance777>0){
            System.out.println("修改成功");
            sqlSession.commit();
        }
        sqlSession.close();
    }
}
```

#### 几个注意事项

- 当增删改时，必须使用`sqlSession.commit()`提交事务，否则没有真正影响数据库。
- 当字段有`Date`类型时，`java.sql.date`和`java.util.date`是不一样的,因为实体类为`java.sql.date`在构造时需要接收一个long类型的参数。
  - 方式一使用`Instant.now().toEpochMilli()`这会返回一个`long`类型的值，表示自1970年1月1日午夜（GMT）以来的毫秒数。
  - 方式二使用`date.getTime()`方法用于获取`Date`对象表示的时间点（以毫秒为单位）自1970年1月1日午夜（GMT）以来的时间戳。

#### 万能的Map

刚才更新用户(其他时候也一样，比如插入，查找)的时候就引出了一个问题，当我们只想更新User中的几个字段时，我们需要构造出全部的属性，这显然很麻烦也很冗余。

可以设置parameterType=Map来解决这个问题，在map中封装好要用的字段，直接传递使用即可，示例如下

UserMapper

```java
int updateUser2(Map<String,Object> map);
```

UserMapper.xml

```xml
<update id="updateUser2" parameterType="map">
        UPDATE jdbc01.users SET name=#{name} WHERE id=#{id}
</update>
```

test

```java
@Test
public void update2() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Object> map = new HashMap<>();
    map.put("id",1);
    map.put("name","balance999");
    int balance777 = mapper.updateUser2(map);
    if(balance777>0){
        System.out.println("修改成功");
        sqlSession.commit();
    }
    sqlSession.close();
}
```

#### 模糊查询

在sql查询时，往往需要使用模糊查询，具体语句如下

```sql
SELECT * FROM users where name like '%h%'
```

这里推荐使用单引号。

那如何在java中进行模糊查询呢？

**方式一，sql语句中拼接,不推荐，有sql注入问题**

```java
List<User> getLikeUser(String name);
public void test2(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> userList = mapper.getLikeUser("h");
    for (User user : userList) {
        System.out.println(user);
    }
    sqlSession.close();
}
```

```xml
<select id="getLikeUser" parameterType="String" resultType="com.balance.pojo.User">
    select * from jdbc01.users where name like "%"#{name}"%"
</select>
```

**注意这里是双引号**

**方式二，直接写在传递的参数中**

```java
List<User> userList = mapper.getLikeUser("%h%");

<select id="getLikeUser" parameterType="String" resultType="com.balance.pojo.User">
    select * from jdbc01.users where name like #{name}
</select>
```

## Mybatis配置解析

### 核心配置 mybatis-config.xml

MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：

- configuration（配置）
  - [properties（属性）](https://mybatis.org/mybatis-3/zh/configuration.html#properties)
  - [settings（设置）](https://mybatis.org/mybatis-3/zh/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  - environments（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.org/mybatis-3/zh/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.org/mybatis-3/zh/configuration.html#mappers)

**注意，标签书写必须按序，xml中标签是有序的。**

### 环境配置

- MyBatis 可以配置成适应多种环境，根据id识别不同环境

- 尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。

- 学会配置多套运行环境-----更改id

```xml
<environments default="id">
```

- Mybatis默认的事务管理器就是JDBC，连接池：POOLED
- 连接池：使并发 Web 应用快速响应请求

```xml
<!--默认使用的环境 ID（比如：default="development"）-->
<environments default="development">
    <!--每个 environment 元素定义的环境 ID（比如：id="development"）-->
    <environment id="development">
        <!--事务管理器的配置 type="JDBC(默认)|MANAGED"）-->
        <transactionManager type="JDBC"/>
        <!--数据源的配置 type="POOLED(默认)|UNPOOLED|JDN"）连接池：使并发 Web 应用快速响应请求-->
        <dataSource type="POOLED">
            <!--driver – 这是 JDBC 驱动的 Java 类全限定名（并不是 JDBC 驱动中可能包含的数据源类）-->
            <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
            <!--url – 这是数据库的 JDBC URL 地址。 &amp;表示转义后的&-->
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
            <!--username – 登录数据库的用户名-->
            <property name="username" value="root"/>
            <!--password – 登录数据库的密码-->
            <property name="password" value="123456"/>
        </dataSource>
    </environment>
</environments>
```

- 事务管理器`<transactionManager type="[ JDBC | MANAGED ]"/>`

- 有三种内建的数据源类型` <dataSource type="[UNPOOLED|POOLED|JNDI]">`

  - unpooled： 这个数据源的实现只是每次被请求时打开和关闭连接。

  - pooled： 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来 , 这是一种使得
    并发 Web 应用快速响应请求的流行处理方式。
  - jndi：这个数据源的实现是为了能在如 Spring 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。

  - 数据源也有很多第三方的实现，比如dbcp，c3p0，druid等等…

### 属性优化

- 数据库这些属性都是可外部配置且可动态替换的

- 我们可以通过properties属性来实现引用属性文件

- 这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件【db.properties】中配置这些属性，也可以在 properties 元素的子元素中设置

可以新建db.properties作为属性的配置

```
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbc01?useSSL=true&characterEncoding=UTF-8&useUnicode=true&serverTimezone=GMT
username=root
password=
```

**注意，url中`amp;`需删除，因为这个转义字符在Properties中不需要**

然后重新编写 mybatis-config.xml

```xml
<!--核心配置文件-->
<configuration>
    <!--外部属性配置文件映射-->
    <properties resource="db.properties"/>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/balance/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

属性中具体值通过`${}`来取

也可以在mybatis-config.xml的properties标签中写配置，注意外部配置（db.properties）优先级高，其次才是properties标签中的配置

```xml
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="password" value="niit12"/>
</properties>
```

也可以在 SqlSessionFactoryBuilder.build() 方法中		传入属性值。例如：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);

// ... 或者 ...

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

**通过方法参数传递的属性具有最高优先级，resource/url 属性中指定的配置文件次之，最低优先级的则是 properties 标签中指定的属性**

###  别名优化

- 类型别名可为 Java 类型设置一个缩写名字。
- 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

#### 方法一：`<typeAlias>`标签

```xml
<!--给实体类起别名-->
<typeAliases>
    <typeAlias type="com.balance.pojo.User" alias="User"/>
</typeAliases>
```

#### 方法二：`<package>`标签

```xml
<!--给实体类起别名-->
<typeAliases>
    <package name="com.balance.pojo"/>
</typeAliases>
```

即扫描实体类所在的包，包下类的类名就是该类的别名（别名默认首字母小写，大写也行）。

每一个在包 `com.balance.pojo.User` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 `com.balance.pojo.Author` 的别名为 `author`；

**若有注解，则别名为其注解值**。如下,Author的别名为hello

```java
@Alias("hello")
public class Author {
    ...
}
```

两种方法区别：

- 实体类比较少的时候，使用第一种方式
- 如果实体类十分多，建议使用第二种
- 第一种可以DIY别名，第二种则不行，如果非要改，需要在实体类上增加注解

#### **Mybatis自带别名**

下面是一些为常见的 Java 类型内建的类型别名。它们都是不区分大小写的，注意，为了应对原始类型的命名重复，采取了特殊的命名风格。（八大基本数据类型前加下划线，对应的包装类小写即可）

| 别名                      | 映射的类型   |
| ------------------------- | ------------ |
| _byte                     | byte         |
| _char (since 3.5.10)      | char         |
| _character (since 3.5.10) | char         |
| _long                     | long         |
| _short                    | short        |
| _int                      | int          |
| _integer                  | int          |
| _double                   | double       |
| _float                    | float        |
| _boolean                  | boolean      |
| string                    | String       |
| byte                      | Byte         |
| char (since 3.5.10)       | Character    |
| character (since 3.5.10)  | Character    |
| long                      | Long         |
| short                     | Short        |
| int                       | Integer      |
| integer                   | Integer      |
| double                    | Double       |
| float                     | Float        |
| boolean                   | Boolean      |
| date                      | Date         |
| decimal                   | BigDecimal   |
| bigdecimal                | BigDecimal   |
| biginteger                | BigInteger   |
| object                    | Object       |
| date[]                    | Date[]       |
| decimal[]                 | BigDecimal[] |
| bigdecimal[]              | BigDecimal[] |
| biginteger[]              | BigInteger[] |
| object[]                  | Object[]     |
| map                       | Map          |
| hashmap                   | HashMap      |
| list                      | List         |
| arraylist                 | ArrayList    |
| collection                | Collection   |
| iterator                  | Iterator     |

### 设置 settings标签

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 下表描述了设置中各项设置的含义、默认值等。（部分重要设置，官网有完整版）

| 设置名                   | 描述                                                         | 有效值                                                       | 默认值 |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| cacheEnabled             | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true   |
| lazyLoadingEnabled       | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false                                                | false  |
| mapUnderscoreToCamelCase | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 | true \| false                                                | False  |
| logImpl                  | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J（3.5.9 起废弃） \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |

一个配置完整的 settings 元素的示例如下：

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="aggressiveLazyLoading" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="safeResultHandlerEnabled" value="true"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
  <setting name="defaultScriptingLanguage" value="org.apache.ibatis.scripting.xmltags.XMLLanguageDriver"/>
  <setting name="defaultEnumTypeHandler" value="org.apache.ibatis.type.EnumTypeHandler"/>
  <setting name="callSettersOnNulls" value="false"/>
  <setting name="returnInstanceForEmptyRow" value="false"/>
  <setting name="logPrefix" value="exampleLogPreFix_"/>
  <setting name="logImpl" value="SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING"/>
  <setting name="proxyFactory" value="CGLIB | JAVASSIST"/>
  <setting name="vfsImpl" value="org.mybatis.example.YourselfVfsImpl"/>
  <setting name="useActualParamName" value="true"/>
  <setting name="configurationFactory" value="org.mybatis.example.ConfigurationFactory"/>
</settings>
```

### 其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
- plugins（常用插件如下）
  - mybatis-generator-core
  - mybatis-plus
  - 通用mapper

### 映射 mappers标签

MapperRegistry：注册绑定我们的Mapper文件，每写一个mapper接口就要写一个Mapper文件

- 映射器 : 定义映射SQL语句文件，既然 MyBatis 的行为其他元素已经配置完了，我们现在就要定义 SQL 映射语句了。但是首先我们需要告诉 MyBatis 到哪里去找到这些语句。Java 在自动查找这方面没有提供一个很好的方法，所以最佳的方式是告诉 MyBatis 到哪里去找映射文件。

- 1张表——1个Entity类——1个EntityDao/EntityMapper接口——1个Mapper.xml——核心配置文件中的1个
- Dao/Maapper接口中的1个方法——Mapper.xml中的1个sql语句

#### 方法一：使用`resource属性`

使用相对于类路径的资源引用（推荐）

```xml
<!--每一个Mapper.xml都需要在MyBatis核心配置文件中注册-->    
<mappers>
    <mapper resource="com/balance/dao/UserMapper.xml"/>
</mappers>
```

#### 方法二：使用`class属性`

使用映射器接口实现类的完全限定类名

- 接口和它的Mapper配置文件必须同名  
- 接口和它的Mapper配置文件必须在同一个包下

```xml
<mappers>
    <!--<mapper class="com.balance.dao.UserMapper"/>--><!--报错-->
    <mapper class="com.balance.dao.UserMapper"/>
</mappers>
```

#### 方法三：`<package>元素`

将包内的映射器接口实现全部注册为映射器

- 接口和它的Mapper配置文件必须同名
- 接口和它的Mapper配置文件必须在同一个包下

```xml
<mappers>
  <package name="com.balance.dao"/>
</mappers>
```

#### 方式4：`url属性`(不常用)

```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

### 生命周期和作用域

![image-20231213165706378](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312131657858.png)

不同作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题。

- SqlSessionFactoryBuilder

  - 作用：创建 SqlSessionFactory

  - 一旦创建了 SqlSessionFactory，就不再需要它了。

  - SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。

- SqlSessionFactory

  - 可以理解为数据库连接池

  - SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。

  - 一般的应用中我们往往希望 SqlSessionFactory 作为一个单例，让它在应用中被共享。

  - SqlSessionFactory 的最佳作用域是应用作用域(Application Scope)。

  - 最简单的就是使用单例模式或者静态单例模式

- SqlSession（相当于从连接池中获取一个连接）

  - 连接到连接池的一个请求，相当于一个数据库连接（Connnection对象）

  - **SqlSession 的实例不是线程安全的，因此是不能被共享的**，它的最佳的作用域是请求或方法作用域

  - 用完之后需要赶紧关闭，否则资源被占用。用 try…catch…finally… 语句来保证其正确关闭。

  - 可以在一个事务里面执行多条 SQL，然后通过它的 commit、rollback 等方法，提交或者回滚事务。

  - 一个 SqlSession 可以多次使用它的 getMapper 方法，获取多个 mapper 接口实例

- 打个比方：

  - SqlSessionFactoryBuilder 是造车公司

  - 造了 100 台车，然后卖给租车公司（SQLSessionFactory），然后倒闭

  - SQLSession 用户 租车，使用车

  - mapper 用户的使用，用户租到车之后可以开去这，开去那，任凭使用

  - 用户（SQLSession） 执行完想做的事之后，必须归还 “汽车” 给租车公司（SQLSessionFactory）

![image-20231213165821662](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312131658864.png)

这里面的每一个 mapper 就代表一个具体的业务。

## 解决属性名和字段名不一致的问题

现在我们数据库中表的字段和实体类的属性是一一对应的

```java
private int id;
private String name;
private String password;
private String email;
private Date birthday;
```

假如不对应怎么办，比如 **password 在实体类中是 pwd**

这样查出来的结果是

```
User{id=1, name='balance1111', pwd='null', email='balance@qq.com', birthday=2000-01-01}
```

可以看到pwd为null，这肯定是错误的，如何解决呢？

**一种直接的想法是起别名，更改sql语句**

比如写成

```sql
select id,name,password as pwd,email,birthday from jdbc01.users where id =#{id}
```

但如果太多不一致，我们写sql语句都要考虑这些太麻烦，且容易出错

**Mybatis提供结果集映射来帮助我们解决这一问题：**

在UserMapper.xml的元素中添加

```xml
<!--结果集映射-->
<resultMap id="UserMap" type="User">
    <!--column:数据库中的字段;property:实体类中的属性-->
    <!--<result column="id" property="id"/>-->
    <!--<result column="name" property="name"/>-->
    <result column="pwd" property="password"/>
</resultMap>


```

我们只需要事先**定义好不一致的属性和字段间对应关系**即可。

resultMap 元素是 MyBatis 中最重要最强大的元素

- ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了

- MyBatis 会在幕后自动创建一个 ResultMap，再根据属性名来映射列到 JavaBean 的属性上,这就是**为什么是null的原因**。

- 在之前的学习中resultMap都是被隐式创建的，因此需保证实体类的属性名和数据库的列名或列别名对应相同

- select、update、insert、delete中的属性可以使用 resultType 或 resultMap，但不能同时使用。

所以UserMapper.xml可以如下书写

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.balance.dao.UserMapper">
    <resultMap id="UserMap" type="user">
        <result column="password" property="pwd"/>
    </resultMap>
    <!--修改resultType为resultMap（两者不能同时使用）-->
    <select id="getUserById"  parameterType="int" resultMap="UserMap">
        select * from jdbc01.users where id =#{id}
    </select>
</mapper>
```

## 日志

### 日志工厂

如果一个数据库操作出现了异常，我们需要排错。日志就是最好的助手！
曾经：debug、sout
现在：日志工厂

![image-20231219154116857](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312191541239.png)

```xml
<setting name="logImpl" value="STDOUT_LOGGING"/>
```

value属性只能为以下值：

- SLF4J
- LOG4J 【掌握】
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING
- STDOUT_LOGGING【掌握】
- NO_LOGGING

### `STDOUT_LOGGING`—标准日志输出

在核心配置文件中配置日志实现

```xml
<settings>
    <!--标准的日志工厂实现-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

![image-20231219154741524](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312191547597.png)

### Log4j-已废弃

什么是LOG4J

- Log4j是Apache的一个开源项目

- 通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件

- 我们可以控制每一条日志的输出格式；

- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。

- 通过**一个配置文件**来灵活地进行配置，而不需要修改应用的代码。

**导包后**在resource目录下新建log4j.properties配置文件

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=【%c】-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/balance.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=【%p】【%d{yy-MM-dd}】【%c】%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

修改mybatis-config.xml

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

运行并测试

![image-20231219161948003](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312191619109.png)

![image-20231219162035973](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312191620973.png)

#### 简单使用

1. 在要使用log4j的类中导入Apache的包

   ```java
   import org.apache.log4j.Logger;
   ```

2. 日志对象，参数为当前类的class

   ```java
   static Logger logger = Logger.getLogger(UserDaoTest.class);
   ```

3. 使用日志级别

   ```java
   @Test
   public void testLog4j(){
       //相当与sout，但输出的日志级别不同
       logger.info("info:进入testLog4j");
       logger.debug("debug:进入了testLog4j");
       logger.error("error:进入了testLog4j");
   }
   
   //控制台输出：（日志文件中也会添加以下输出）
   //21:07:25,697  INFO UserDaoTest:63 - info:进入testLog4j
   21:07:25,702 DEBUG UserDaoTest:64 - debug:进入了testLog4j
   21:07:25,702 ERROR UserDaoTest:65 - error:进入了testLog4j
   ```


## 分页

为什么要分页？减少数据的处理量

### 使用Limit分页—通过sql实现

```sql
SELECT * FROM users limit startIndex,pageSize;
SELECT * FROM users limit 0,2;--从第1行开始(包括第1行)显示2条记录，不包括0
SELECT * FROM users limit 3; --[0,n]显示前3条记录
```

编写UserMapper接口（方法的参数为Map）

```java
List<User> getUserByLimit(Map<String,Integer> map);
```

xml映射文件

```xml
<select id="getUserByLimit" parameterType="map" resultMap="UserMap">
    select * from users limit #{startIndex},#{pageSize}
</select>
```

测试

```java
@Test
public void getUserLimitTest(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Integer> map = new HashMap<>();
    map.put("startIndex",0);
    map.put("pageSize",5);
    List<User> userByLimit = mapper.getUserByLimit(map);
    for (User user : userByLimit) {
        System.out.println(user);
    }
    sqlSession.close();
}
```

### RowBounds实现分页—了解

**也就是在java代码中控制分页**

UserMapper接口

```java
List<User> getUserByRowBounds();
```

UserMapper.xml映射文件

```xml
<select id="getUserByRowBounds"  resultMap="UserMap">
    select * from users
</select>
```

测试

```java
@Test
public void getUserLimitByRowBounds(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    //RowBounds实现分页
    RowBounds rowBounds = new RowBounds(1, 2);

    List<User>
        userList=sqlSession.selectList("com.balance.dao.UserMapper.getUserByRowBounds",null,rowBounds);
    for (User user : userList) {
        System.out.println(user);
    }
    sqlSession.close();
}
```

### 分页插件【了解】

MyBatis 分页插件 PageHelper

如何使用----[如何使用分页插件](https://pagehelper.github.io/docs/howtouse/)

## 使用注解开发

#### 面向接口编程

我们之前都学过面向对象编程，也学习过接口，但在真正的开发中，很多时候我们会选择面向接口编程

- 根本原因 : 解耦 , 可拓展 , 提高复用 , 分层开发中 , 上层不用管具体的实现 , 大家都遵守共同的标准 , 使得开发变得容易 , 规范性更好

- 在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的,对系统设计人员来讲就不那么重要了；

- 而各个对象之间的协作关系则成为系统设计的关键。小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容。面向接口编程就是指按照这种思想来编程。


关于接口的理解

- 接口从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离。

- 接口的本身反映了系统设计人员对系统的抽象理解。

- 接口应有两类：

  - 第一类是对一个个体的抽象，它可对应为一个抽象体(abstract class)；

  - 第二类是对一个个体某一方面的抽象，即形成一个抽象面（interface）；

  - 一个体有可能有多个抽象面。抽象体与抽象面是有区别的。


三个面向区别

- 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法 .

- 面向过程是指，我们考虑问题时，以一个具体的流程（事务过程）为单位，考虑它的实现 .

- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题.更多的体现就是对系统整体的架构


#### 注解-简单使用

UserMapper接口

```java
@Select("select * from users")
List<User> getUsers();
```

核心配置文件中绑定接口

```xml
<!--绑定接口-->
<mappers>
    <mapper class="com.balance.dao.UserMapper"/>
</mappers>
```

测试

```java
@Test
public void getUsers(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    List<User> userList = mapper.getUsers();

    for (User user : userList) {
        System.out.println(user);
    }

    sqlSession.close();
}
```

本质：反射机制实现

底层：**动态代理**？

#### Mybatis详细的执行流程（分析源码）

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312201542242.png)

#### 注解-CRUD

我们可以在工具类**MybatisUtil中创建sqlSession对象的时候实现自动提交事务！（但实际上不推荐）**

```java
public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession(true); //事务自动提交
}
```

接口

```java
public interface UserMapper {
    @Select("select * from users")
    List<User> getUsersAnnotation();
    
    //方法存在多个参数，所有的参数前面都要加上@Param("id")
    @Select("select * from users where id=#{id}")
    List<User> getUsersByIdAnnotation(@Param("id") int id);

    @Update("update users set name=#{name},password=#{pwd} where id=#{id}")
    int updateAnnotation(User user);
    
    @Insert("insert into users(id,name,password,email,birthday) values(#{id},#{name},#{pwd},#{email},#{birthday})")
    int addUserAnnotation(User user);
    
    @Delete("delete from users where id=#{id}")
    int deleteUserAnnotation(@Param("id") int id);
}
```

配置文件绑定Mapper接口

```xml
<mappers>
    <mapper class="com.balance.dao.UserMapper"/>
</mappers>
```

测试代码略

#### `@Param`注解

[MyBatis（十五）：@Param()注解 - 谁知道水烫不烫 - 博客园](https://www.cnblogs.com/jmsstudy/p/16695315.html)

为什么要使用`@Param`注解？因为当传递多个参数时，我们无法使用parameterType去指定参数类型（只能指定一个），此时可以封装成map，但每次去封装map无疑很繁琐，而且不一定需要，此时可以**用此注解，我们就不需要去写parameterType了**。

简单总结一下：

- sql的#{}中的参数就是@Param(“”)中设置的参数，而不是方法的形参

- 传入单个基本类型或String类型的时候，使用不使用@Param()注解都可以。

- 如果参数是 JavaBean ， 则不能使用@Param。

- 需要传入多个参数时，有三种方法：JavaBean、@Param()注解、Map。个人认为优先使用JavaBean，当需要传入的数据较多但又不能完全将JavaBean利用起来的时候使用@Param()注解或Map。

- 单个基本类型，SQL对应的就是形参的名字；使用JavaBean，SQL中对应的就是JavaBean在结果集映射的属性（没有显性映射就是默认的属性）；使用@Param()，SQL中对应的就是@Param()注解中的名字；使用Map，SQL中对应的就是Map的键名。

- **使用@Param()注解的时候，Mapper.xml文件无需再设置parameterType属性。**

## Lombok

Lombok项目是一个**java库**，它可以自动插入到编辑器和构建工具中，增强java的性能。不需要再写getter、setter或equals方法，只要有一个注解，你的类就有一个功能齐全的构建器、自动记录变量等等。

> 是一个在Java开发过程中用注解的方式，简化了 JavaBean(实体类) 的编写，避免了冗余和样板式代码而出现的插件，让编写的类更加简洁。

### 使用步骤

1. 在IDEA中安装lombok插件：File —> Settings —> Plugins —> Browse repositories —> 搜索lombok（**新版idea已集成**）
2. 在pom.xml中导入lombok的maven依赖（包）

3. 在实体类上加注解即可

**常用注解如下：**

- @Data : 自动生成set/get方法，toString方法，equals方法，hashCode方法，不带参数的构造方法

- @NoArgsConstructor/@RequiredArgsConstructor/@AllArgsConstructor
  自动生成无参/有参构造方法

- @Setter/@Getter : 自动生成set和get方法

- @ToString : 自动生成toString方法

- @EqualsAndHashcode : 从对象的字段中生成hashCode和equals方法


**以下为不常用注解：**

- @NonNull : 用在成员方法或者构造方法的参数前面，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常

- @CleanUp : 自动资源管理：不用再在finally中添加资源的close方法

- @Value : 用于注解final类

- @Builder : 产生复杂的构建器api类

- @SneakyThrows : 异常处理（谨慎使用）

- @Synchronized : 同步方法安全的转化

- @Getter(lazy=true) :

- @Log: 支持各种logger对象，使用时用对应的注解，如：@Log4j

**优点:**

能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、 toString等方法，提高了一定的开发效率

让代码变得简洁，不用过多的去关注相应的方法

属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

**缺点:**

不支持多种参数构造器的重载

虽然省去了手动创建getter/seter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度

## 一对多和多对一查询

### 复杂查询环境搭建

创建示例数据表

```sql
CREATE TABLE `teacher` (
   `id` INT(10) NOT NULL,
   `name` VARCHAR(30) DEFAULT NULL,
   PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师');

CREATE TABLE `student` (
   `id` INT(10) NOT NULL,
   `name` VARCHAR(30) DEFAULT NULL,
   `tid` INT(10) DEFAULT NULL,
   PRIMARY KEY (`id`),
   KEY `fktid` (`tid`),
   CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;


INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
```

创建pojo类和对应mapper接口

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Teacher {
    private int id;
    private String name;
}

public interface TeacherMapper {
    Teacher getTeacher(@Param("id") int id);
}


@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private int id;
    private String name;

    //学生需要关联一个老师，注意数据库里是外键的形式，这里是老师类的对象
    private Teacher teacher;
}

public interface StudentMapper {
}
```

**注意Mapper.xml创建其实和mybatis-config.xml格式差不多**

如下只需要更改头部两处`!DOCTYPE configuration`中的configuration和`https://mybatis.org/dtd/mybatis-3-config.dtd`中的config，然后改下面的标签即可

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">

</mapper>

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>
    
</configuration>
```

当Mapper文件过多时，都放在dao层里显然不太合适，在resources目录下新建相同的包名，**注意要一个个建**

![image-20231221162432858](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202312211624443.png)

然后使用时注意在mybatis-config.xml中注册，以TeacherMapper.xml为例**,如果使用resources属性这里的注册也需一个个注册。MyBatis 的 `<mapper>` 元素中的 `resource` 属性不支持通配符**

```xml
<mappers>
    <mapper resource="com/balance/dao/TeacherMapper.xml"/>
</mappers>
```

**或者使用package标签，全部注册。**

这种写法可以的原因是当程序运行后，文件会被打包生成到target/classes目录下，也就是说源程序中java目录的包会原封不动输出，而resources目录下的文件会直接放在target/classes目录下，此时若java目录中文件路径和resources目录路径相同，就会合并到同一个路径下。

```xml
<mappers>
    <package name="com.balance.dao"/>
</mappers>
```

**通常情况下，推荐将 XML Mapper 文件放置在 resources 目录下，因为这是 Maven 和其他构建工具默认会扫描的位置。**

### 多对一

多对一的理解：

- 多个学生对应一个老师

- 如果对于学生这边，就是一个多对一的现象，即从学生这边**关联**一个老师！


结果映射（resultMap）：

- association

  - 一个复杂类型的关联；许多结果将包装成这种类型
  - 嵌套结果映射 —— 关联可以是 resultMap 元素，或是对其它结果映射的引用
- collection
  - 一个复杂类型的集合
  - 嵌套结果映射 —— 集合可以是 resultMap 元素，或是对其它结果映射的引用

**需求：查所有学生的信息以及其对应的老师姓名**

对应sql语句

```sql
select 
	s.id AS sid ,
	s.name AS sname ,
	t.name AS tname 
from 
	student AS s,
	teacher AS t 
where s.tid=t.id
```

#### 法一 子查询

因为student表中存放的是teacher id，而实体类只能建teacher对象，当多对一时，可以考虑使用嵌套查询，首先查询出所有学生信息，然后定义resultMap，在其中使用association标签，指定实体类属性和表字段名，以及指定实体类属性javaType，然后嵌套一个查询teacher的语句。

**也就是先查一张表，用这张表的字段去查另一张表**

```xml
<select id="getStudent" resultMap="StudentTeacher">
    select * from student
</select>
<resultMap id="StudentTeacher" type="student">
    <!--复杂的属性，需要单独处理——对象：<association>  集合：<collection>-->
    <association property="teacher" javaType="teacher" column="tid" select="getTeacher"/>
</resultMap>
<select id="getTeacher" resultType="teacher">
    select * from teacher where id=#{id}
</select>
```

#### 法二 联表查询 更直接

可以看到法一是使用了两个查询，如果我们就想写原来的sql语句应该怎么办？这时用到联表查询

```xml
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id as sid, s.name as sname,t.name as tname from student as s,teacher as t where s.tid=t.id
</select>
<resultMap id="StudentTeacher2" type="student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="teacher">
        <result property="name" column="tname"/>
    </association>
</resultMap>
```

我们从数据库查出三个字段sid，sname，tname，前两个很好对应，但第三个，我们的实体类要求是对象，使用association标签，定义其属性和其javaType，在association标签内用result进行映射。此时association中不需要写column，因为是按照结果去映射。

### 一对多

一个老师有多个学生

对应sql

```sql
SELECT t.id AS tid, t.`name` AS tname, s.id AS sid, s.`name` AS sname
FROM `teacher` AS t, `student` AS s
WHERE t.id = s.tid AND t.id = #{tid}
```

对应实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private int id;
    private String name;

    private int tid;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Teacher {
    private int id;
    private String name;

    //一个老师有许多学生，所以用集合
    private List<Student> students;
}
```

#### 法一 子查询

先查teacher表，再查student表

```xml
<select id="getTeacher2" resultMap="TeacherStudent2">
      select * from teacher where id=#{tid}
</select>
<resultMap id="TeacherStudent2" type="teacher">
    <!--前两个属性字段名一致可以省略-->
    <collection property="students" column="id" javaType="ArrayList" 			ofType="student" select="getStudentByTid"/>
</resultMap>
<select id="getStudentByTid" resultType="student">
    select * from student where tid=#{tid}
</select>
```

#### 法二 联表查询 更直接

```xml
<select id="getTeacher" resultMap="TeacherStudent">
    SELECT t.id AS tid, t.`name` AS tname, s.id AS sid, s.`name` AS sname
    FROM `teacher` AS t, `student` AS s
    WHERE t.id = s.tid AND t.id = #{tid}
</select>
<resultMap id="TeacherStudent" type="teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--
            javaType=“” 集合或关联对象的类型
            ofType： 集合或关联对象中元素的 Java 类型
			其实可以省略，mybatis会自动帮我们进行推断
        -->
    <collection property="students" ofType="student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```

## 动态sql

什么是动态SQL：**动态SQL指的是根据不同的查询条件 , 生成不同的Sql语句.**

类似JSTL标签

> 官网描述：
> MyBatis 的强大特性之一便是它的动态 SQL。如果你有使用 JDBC 或其它类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。
> 虽然在以前使用动态 SQL 并非一件易事，但正是 MyBatis 提供了可以被用在任意 SQL 映射语句中的强大的动态 SQL 语言得以改进这种情形。
> 动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似。在 MyBatis 之前的版本中，有很多元素需要花时间了解。MyBatis 3 大大精简了元素种类，现在只需学习原来一半的元素便可。MyBatis 采用功能强大的基于 OGNL 的表达式来淘汰其它大部分元素。

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

我们之前写的 SQL 语句都比较简单，如果有比较复杂的业务，我们需要写复杂的 SQL 语句，往往需要拼接，而拼接 SQL ，稍微不注意，由于引号，空格等缺失可能都会导致错误。

那么怎么去解决这个问题呢？这就要使用 mybatis 动态SQL，通过 if, choose, when, otherwise, trim, where, set, foreach等标签，可组合成非常灵活的SQL语句。
### 环境搭建

数据库表

```sql
CREATE TABLE `blog` (
`id` varchar(50) NOT NULL COMMENT '博客id',
`title` varchar(100) NOT NULL COMMENT '博客标题',
`author` varchar(30) NOT NULL COMMENT '博客作者',
`create_time` datetime NOT NULL COMMENT '创建时间',
`views` int(30) NOT NULL COMMENT '浏览量'
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

创建Mybatis基础工程

![image-20240105160754747](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401051607933.png)

IDutil工具类

```java
public class IDUtils {
    //使用UUID来获取不同的id，因为生成的是-分隔，所以替换成分割
    public static String getId(){
        return UUID.randomUUID().toString().replaceAll("-","");
    }
}
```

实体类编写 

注意：Date类为`java.util.Date`，不是`java.sql.Date`

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Blog {
    private String id;
    private String title;
    private String author;
    private Date createTime;//属性名和字段名不一致，驼峰和下划线转化可以在 mybatis-config中开启设置
    private int views;
}
```

编写Mapper接口及xml文件

BlogMapper

```java
public interface BlogMapper {
}
```

BlogMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.balance.dao.BlogMapper">

</mapper>
```

mybatis核心配置文件，**下划线驼峰自动转换**

```xml
<settings>
   <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

### if标签

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。

**也就是说，如果我们要按不同条件查询，比如作者，出版社，我们可以仅写一条查询语句，在其中嵌套多个if，从而实现需求，而不用写多个方法。**

接口

```java
public interface BlogMapper {
    //int addBlog(Blog blog);

    List<Blog> queryBlogIf(Map map);
}
```

BlogMapper.xml,**注意此处拼接 where 1=1，这样方便一点**

```xml
<select id="queryBlogIf" parameterType="map" resultType="blog">
    select * from blog where 1=1
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</select>
```

测试

```java
public class BlogTest {
    @Test
    public void queryIf(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
        HashMap<String, Object> map = new HashMap<>();
        //map.put("title","Java如此简单");
        map.put("author","狂神说");
        List<Blog> blogs = mapper.queryBlogIf(map);
        for (Blog blog : blogs) {
            System.out.println(blog);
        }
        sqlSession.close();
    }
}
```

如果我们只传title或者author，就只查title或者author，如果一起传，就查满足两个条件所有的。

### choose（when、otherwise）

有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

还是上面的例子，但是策略变为：传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。

如果两者都没有提供就使用otherwise中的语句

接口

```java
List<Blog> queryBlogChoose(Map map);
```

BlogMapper.xml,这里没有where 1=1 了使用 `where`标签代替

```xml
<select id="queryBlogChoose" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <choose>
            <when test="title != null">
                title=#{title}
            </when>
            <when test="author != null">
                author=#{author}
            </when>
            <otherwise>
                views=#{views}
            </otherwise>
        </choose>
    </where>
</select>
```

测试

```java
@Test
public void queryChoose(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap hashMap = new HashMap();
    //hashMap.put("title","Java如此简单");
    hashMap.put("author","狂神说");
    List<Blog> blogs = mapper.queryBlogChoose(hashMap);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }
}
```

### trim（where，set）

> https://mybatis.org/mybatis-3/zh_CN/dynamic-sql.html

#### where

- *where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 关键字。
- 若子句的开头为 “AND” 或 “OR”，*where* 元素也会视情况将它们去除或保留。

上面的意思就是where标签只有后面有语句才会有where字样，否则最终sql语句自动去掉。

而且，当后面有语句比如只有一句会把多余的and去掉

where在上面已经演示过了，这里说一下不使用where标签的问题，比如if标签中演示的sql语句没有1=1

```xml
<select id="queryBlogIf" parameterType="map" resultType="blog">
    select * from blog 
    where
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</select>
```

如果什么都没匹配，或匹配到一个，语句会变成

```sql
select * from blog where
select * from blog where and title=#{title}
```

都会导致出错,使用where标签，上述sql应该改为

```xml
<select id="queryBlogIf" parameterType="map" resultType="blog">
    select * from blog 
    <where>
        <if test="title != null">
            title=#{title}
        </if>
        <if test="author != null">
            and author=#{author}
        </if>
    </where>
</select>
```

#### set update

*set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。

接口

```java
int updateSet(Map map);
```

BlogMapper.xml

```xml
<update id="updateSet" parameterType="map">
    update blog
    <set>
        <if test="author != null">
            author = #{author},
        </if>
        <if test="title != null">
            title =#{title}
        </if>
    </set>
    where id=#{id}
</update>
```

测试

```java
@Test
public void updateSetTest(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap hashMap = new HashMap();
    //hashMap.put("title","Java如此简单2");
    hashMap.put("author","狂神说2");
    hashMap.put("id","208d60ebd16b40bcb71a6317320d50c5");
    int i = mapper.updateSet(hashMap);
    System.out.println(i);
    sqlSession.commit();
    sqlSession.close();
}
```

可以看到即使我们只写了author，也不会因为多一个逗号而出错

#### trim

where和set都可以被trim代替

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

###  sql片段

将需要重复编写的sql片段提取出来方便复用

以if标签中语句为例，首先抽取出来 

```xml
<sql id="if-title-author">
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</sql>
```

在sql语句中用include标签进行引用即可

```xml
<select id="queryBlogIf" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <include refid="if-title-author"/>
    </where>
</select>
```

**注意事项**

* 尽量封装简单的单表语句
* 不要在其中使用where标签

### Foreach标签

也就是遍历传入的集合，一般用于 in 或者 or 或者 and 之类

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  <where>
    <foreach item="item" index="index" collection="list"
        open="ID in (" separator="," close=")" nullable="true">
          #{item}
    </foreach>
  </where>
</select>
```

*foreach* 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符

**比如如下sql语句**

```sql
select * from blog where id=1 or id=2 or id=3
```

接口

```java
List<Blog> queryForeach(Map map);
```

BlogMappepr.xml

```xml
<select id="queryForeach" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <foreach collection="ids" item="id" index="index" open="(" separator="or" 			close=")">
            id=#{id}
        </foreach>
    </where>
</select>
```

测试

```java
@Test
public void queryForeach(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap map = new HashMap();
    ArrayList ids = new ArrayList();
    ids.add("208d60ebd16b40bcb71a6317320d50c5");
    ids.add("6daeb9c67ea04b57bc1c92cb29ad25f7");
    ids.add("b8e435d9bb0942c28ebca07b6600b079");
    map.put("ids",ids);
    List<Blog> blogs = mapper.queryForeach(map);
    for (Blog blog : blogs) {
    System.out.println(blog);
    }
    sqlSession.close();
}
```

## 缓存

### 简介

1. 什么是缓存 [ Cache ]？
   * 存在内存中的临时数据。
   * 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

2. 为什么使用缓存？
   * 减少和数据库的交互次数，减少系统开销，提高系统效率。

3. 什么样的数据能使用缓存？
   * 经常查询并且不经常改变的数据。
     

### Mybatis缓存

* MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

* MyBatis系统中默认定义了两级缓存：一级缓存和二级缓存

  * **默认情况下，只有一级缓存开启**。（SqlSession级别的缓存，也称为本地缓存）

  * 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。

  * 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

#### 一级缓存

一级缓存也叫***本地(会话)缓存***：

- 与数据库同一次会话(sqlSession)期间查询到的数据会放在本地缓存中。
- 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

**测试步骤：**

* 开启日志
  * `<setting name="logImpl" value="STDOUT_LOGGING"/>`
* 测试

```java
@Test
public void test(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    Blog blog=mapper.queryBlogById("208d60ebd16b40bcb71a6317320d50c5");
    System.out.println(blog);
    System.out.println("====================================");
    Blog blog2=mapper.queryBlogById("208d60ebd16b40bcb71a6317320d50c5");
    System.out.println(blog2);
    
    sqlSession.close();
}
```

![image-20240106163554793](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401061635020.png)

可以看到sql语句实际上只执行了一次。

**注意一级缓存是SqlSession级别的缓存，是一直开启的，我们关闭不了它；**

一级缓存失效情况：

* 查询不同的记录

* 增删改操作，**可能会改变**原来的数据，所以**必定会刷新**缓存！

* 查询不同的Mapper.xml

* **手动清理缓存！**`sqlSession.clearCache();`

![image-20240106164153596](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401061641847.png)

#### 二级缓存

二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存

基于namespace级别的缓存，一个名称空间，对应一个二级缓存；

工作机制

* 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；

* 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；

* 新的会话查询信息，就可以从二级缓存中获取内容；

* 不同的mapper查出的数据会放在自己对应的缓存（map）中；

**开启二级缓存**

在mybatis-config.xml中显式开启全局缓存

```xml
<!--显式开启全局缓存-->
<setting name="cacheEnabled" value="true"/>
```

在去每个需要缓存的mapper.xml中加上

```xml
<!--在当前mapper中使用二级缓存-->
<cache/>
```

```xml
官方示例=====>查看官方文档
<cache
 eviction="FIFO"
 flushInterval="60000"
 size="512"
 readOnly="true"/>
这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。
```

一共有四种缓存策略，详见官方文档

测试

**注意实体类需要序列化**

![image-20240106170304510](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401061703259.png)

为什么是false?

因为我们未设置readOnly属性，默认是false，即缓存给的值是拷贝值，所以两个blog判断为false

#### 缓存原理

缓存顺序：

1. 先看二级缓存中有没有

2. 再看一级缓存中有没有
3. 查询数据库

注：一二级缓存都没有，查询数据库，查询后将数据放入一级缓存

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401061758391.png)

#### 自定义缓存 ehcache

介绍：

- EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate（已淘汰）中默认的CacheProvider
- Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存
- Enchache可以使用配置文件`ehcache.xml`

**使用方法**

要在应用程序中使用Ehcache，需要引入依赖的jar包

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.3</version>
</dependency>
```

配置mapper中cache type

```xml
<mapper namespace = "com.kuang.dao.UserMapper" >
    <!--开启ehcache缓存-->
    
    <!-- 以下两个<cache>标签二选一,第一个可以输出日志,第二个不输出日志 -->
    <cache type="org.mybatis.caches.ehcache.LoggingEhcache" />

    <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
</mapper>
```

加入Ehcache的配置文件

在src/main/resources/下创建编写ehcache.xml文件，如果在加载时未找到ehcache.xml资源或出现问题，则将使用默认配置。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
        updateCheck="false">
   <!--
      diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
      user.home – 用户主目录
      user.dir – 用户当前工作目录
      java.io.tmpdir – 默认临时文件路径
    -->
   <diskStore path="./tmpdir/Tmp_EhCache"/>
   
   <defaultCache
           eternal="false"
           maxElementsInMemory="10000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="259200"
           memoryStoreEvictionPolicy="LRU"/>

   <cache
           name="cloud_user"
           eternal="false"
           maxElementsInMemory="5000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           memoryStoreEvictionPolicy="LRU"/>
   <!--
      defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
    -->
   <!--
     name:缓存名称。
     maxElementsInMemory:缓存最大数目
     maxElementsOnDisk：硬盘最大缓存个数。
     eternal:对象是否永久有效，一但设置了，timeout将不起作用。
     overflowToDisk:是否保存到磁盘，当系统宕机时
     timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
     timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
     diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
     diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
     diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
     memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
     clearOnFlush：内存数量最大时是否清除。
     memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
     FIFO，first in first out，这个是大家最熟的，先进先出。
     LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
     LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
  -->

</ehcache>
```

自己自定义缓存，实现cache接口

```java
package com.kuang.pojo;

import org.apache.ibatis.cache.Cache;

import java.util.concurrent.locks.ReadWriteLock;

public class MyCache implements Cache {
    @Override
    public String getId() {
        return null;
    }

    @Override
    public void putObject(Object key, Object value) {

    }

    @Override
    public Object getObject(Object key) {
        return null;
    }

    @Override
    public Object removeObject(Object key) {
        return null;
    }

    @Override
    public void clear() {

    }

    @Override
    public int getSize() {
        return 0;
    }

    @Override
    public ReadWriteLock getReadWriteLock() {
        return null;
    }
}
```

配置mapper中cache type

```xml
<mapper namespace = "com.kuang.pojo.UserMapper" >
      <cache type="com.kuang.pojo.MyCache"/>
</mapper>
```

