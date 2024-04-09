# Mybatis-plus

* 需要的基础:spring,spring mvc,mybatis
* 作用:可以节省大量的工作时间,所有的CRUD代码都可以自动完成,简化Mybatis
* MyBatis-Plus (opens new window)（简称 MP）是一个 MyBatis (opens new window) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

![image-20240325134453731](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325134453731.png)

**特性：**

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 SQL 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

## 快速开始

> https://baomidou.com/pages/226c21/#%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8

1.导入对应依赖

2.研究依赖如何配置

3.代码如何编写

4.提供扩展技术能力

**第一步创建数据库 并且插入数据**

```sql
DROP TABLE IF EXISTS `user`;

CREATE TABLE `user`
(
    id BIGINT NOT NULL COMMENT '主键ID',
    name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
    age INT NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
);
```

```sql
INSERT INTO `user` (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

**第二步初始化空的springboot工程 导入依赖**

```xml
<!--数据库驱动-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<!--mybatis_plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <!--下面坐标根据自己使用的SpringBoot版本二选一-->
    <!--SpringBoot2使用此版本-->
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.7</version>
</dependency>
```

**第三步添加数据库配置 application.yml** 

```yml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mybatis-plus?useSSL=false&useUnicode=true&characterEncoding=utf-8
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
```

**第四步使用**

- pojo

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

- mapper接口

```java
@Repository //可以不用这个，只是标识一下是持久层
public interface UserMapper extends BaseMapper<User> {
}
```

- 使用

先在主启动类上加上mapperscan扫描

```java
@MapperScan("com.balance.mapper")
@SpringBootApplication
public class Springboot07MybatisPlusApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot07MybatisPlusApplication.class, args);
    }

}
```

后进行测试

```java
@SpringBootTest
class Springboot07MybatisPlusApplicationTests {
    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        List<User> users = userMapper.selectList(null);//这里需要一个wrapper，条件构造器，这里先不用 null
        System.out.println(users);//users.forEach(System.out::println); 弄清楚这个
    }

}
```

![image-20240325143808272](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325143808272.png)

## 配置日志

我们所有的sql现在是不可见的,我们希望知道它是这么执行的,所以我们必须要看日志

```yml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## CRUD扩展

### insert

编写测试代码

```java
@Test
void testInsert() {
    int jack = userMapper.insert(new User(null, "jack", 18, "jack@qq.com"));
    System.out.println(jack);
}
```

![image-20240325145658938](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325145658938.png)

> 这个id是怎么来的？分布式系统唯一id生成 https://www.cnblogs.com/haoxinyue/p/5208136.html

首先介绍主键生成策略

- uuid
- 自增id
- 雪花算法(snowflake算法)

snowflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心:北京,上海，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。具体实现的代码可以参看https://github.com/twitter/snowflake。雪花算法支持的TPS可以达到419万左右（2^22*1000）。

雪花算法在工程实现上有单机版本和分布式版本。单机版本如下，分布式版本可以参看美团leaf算法：https://github.com/Meituan-Dianping/Leaf

- redis
- zookeeper

实际上mybatis-plus默认设置了id的自增策略，我们可以更改策略

```java
@TableId(type = IdType.AUTO)
private Long id;
```

```java
public enum IdType {
    AUTO(0),//自增
    NONE(1),//未设置主键
    INPUT(2),//手动输入id
    ID_WORKER(3),//默认全局唯一id 雪花算法
    UUID(4),//全球唯一id
    ID_WORKER_STR(5);//ID_WORKER的字符串表示

    private final int key;

    private IdType(int key) {
        this.key = key;
    }

    public int getKey() {
        return this.key;
    }
}
```

**当设置为自增时，需要将数据库字段设置为自增，**然后我们再执行一次

![image-20240325150822892](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325150822892.png)

### update

编写测试代码

```java
@Test
void testUpdate() {
    userMapper.updateById(new User(2L, "rose", 18, "rose@qq.com"));
}
```

![image-20240325151516789](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325151516789.png)

### 自动填充

* 创建时间,修改时间,这些操作一般都是自动化完成的,不希望手动更新
* 比如数据表: gmt_create , gmt_modified , isdelete 几乎所有的表都要配置上,便于追踪

#### 方式一 数据库级别 不建议使用

在表中新增字段create_time,update_time 两个字段都需要current_timestamp更新

![image-20240325153651685](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240325153651685.png)

更新实体类

```java
private Date createTime;
private Date updateTime; 
```

再次测试插入

![image-20240325153711096](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325153711096.png)

#### 方式二 代码级别

删除数据库的默认值,更新操作

![image-20240325153809376](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325153809376.png)

实体类字段属性上需要增加注解

```java
@TableField(fill = FieldFill.INSERT)//也可以通过value属性指定不一样的字段
private Date createTime;
@TableField(fill = FieldFill.INSERT_UPDATE)//也可以通过value属性指定不一样的字段
private Date updateTime;
```

上面的注解表示在插入时和插入/更新时执行填充

**然后编写处理器处理这个注解即可!**

新建handler包，在其下新建 MyMetaObjectHandler 类

```java
@Slf4j //日志的注解
@Component //标注为spring的组件
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        //插入填充策略
        log.info("start insert fill");
        this.setFieldValByName("createTime", new Date(), metaObject);
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        //更新填充策略
        log.info("start update fill");
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }
}
```

再次执行测试 插入

![image-20240325154549393](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325154549393.png)

再次执行测试 更新

![image-20240325154743262](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325154743262.png)

### 乐观锁

> 乐观锁: 顾名思义十分乐观,他总是认为不会出现问题,无论干什么都不去上锁!如果出现了问题,再次更新值测试
> 悲观锁;顾名思义十分悲观,他总是认为出现问题,无论干什么都会上锁!再去操作!

乐观锁实现方式:

- 取出记录时,获取当前version
- 更新时,带上这个version
- 执行更新时,set version = newVersion where version = oldVersion
- 如果version不对,就更新失败

测试一下MybatisPlus的乐观锁插件

**1.给数据库加上version字段**

![image-20240325161603631](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325161603631.png)

**2.我们实体类增加对应的字段**

```java
@Version //代表这是一个乐观锁
private Integer version;
```

**3.注册组件**

新建config包，新建 MybatisPlusConfig 类

```java
@MapperScan("com.balance.mapper")
@EnableTransactionManagement//启用注解驱动的事务管理。
@Configuration
public class MybatisPlusConfig {
    /**
     * 旧版
     */
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }

    /**
     * 新版
     */
//    @Bean
//    public MybatisPlusInterceptor mybatisPlusInterceptor() {
//        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
//        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
//        return mybatisPlusInterceptor;
//    }
}
```

**4.测试乐观锁**

```java
@Test
void testOptimizeLock() {
    //查询用户信息 步一
    User user = userMapper.selectById(1L);
    //修改用户信息 步二
    user.setAge(20);
    user.setName("balance");
    userMapper.updateById(user);
}
```

![image-20240325162908728](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325162908728.png)

**5.测试乐观锁失败 多线程下** 

当多线程条件下,当先查询出来的用户修改完，此时另一个线程已经修改了该用户，此时查询的版本不对，所以未成功

```java
@Test
void testOptimizeLock2() {
    //查询用户信息 步一
    User user = userMapper.selectById(1L);
    //修改用户信息 步二
    user.setAge(21);
    user.setName("乐观锁1111");

    //模拟线程2执行插队操作
    User user1 = userMapper.selectById(1L);
    user1.setAge(22);
    user1.setName("乐观锁2222");
    userMapper.updateById(user1);

    //线程一进行操作
    userMapper.updateById(user);
}
```

![image-20240325163828243](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325163828243.png)

![image-20240325163942308](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325163942308.png)

### 查询

```java
//普通查询
@Test
void testSelect(){
    User user = userMapper.selectById(1L);
    System.out.println(user);
}

//测试批量查询
@Test
void testBatchSelect(){
    List<User> users = userMapper.selectBatchIds(Arrays.asList(1L, 2L, 3L));
    users.forEach(System.out::println);
}

//条件查询之 map
@Test
void testConditionSelect(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name","rose");
    List<User> users = userMapper.selectByMap(map);
    users.forEach(System.out::println);
}
```

#### 分页查询

- 原始的limit分页
- pageHelper第三方插件
- Mp内置了分页插件

如何使用
在config之中配置拦截器即可

旧版：

```java
// 旧版分页插件
@Bean
public PaginationInterceptor paginationInterceptor() {
    return new PaginationInterceptor();
}
```

新版：新版其实就是放在一个方法之中了

```java
/**
  * 添加分页插件
*/
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));//如果配置多个插件,切记分页最后添加
    //interceptor.addInnerInterceptor(new PaginationInnerInterceptor()); 如果有多数据源可以不配具体类型 否则都建议配上具体的DbType
    return interceptor;
}
```

**测试分页查询**

```java
//测试分页查询
@Test
void testPage(){
    //参数一：当前页
    //参数二：页面大小
    Page<User> userPage = new Page<>(1,5);
    userMapper.selectPage(userPage, null);
    userPage.getRecords().forEach(System.out::println);
}
```

![image-20240325170333071](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325170333071.png)

![image-20240325170436256](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325170436256.png)

### 删除

> 物理删除:从数据库中直接移除
> 逻辑删除:没有从数据库中移除,而是通过一个变量来让它失效!deleted = 0 ==> deleted = 1

#### 直接删除

```java
@Test
void testDeleteById(){
    userMapper.deleteById(1L);
}
@Test
void testDeleteBatchId(){
    userMapper.deleteBatchIds(Arrays.asList(1L,2L,3L));
}
@Test
void testDeleteMap(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name","rose");
    userMapper.deleteByMap(map)
}
```

#### 逻辑删除

在数据库表中增加一个 delete 字段，来表示某条记录是否被删除

![image-20240325171901154](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325171901154.png)

实体类中增加属性

```java
@TableLogic //逻辑删除标识注解
private Integer deleted;
```

配置

```java
//注册逻辑删除 旧版 3.0.7
@Bean
public ISqlInjector sqlInjector(){
    return new LogicSqlInjector();
}
```

```yml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      logic-delete-value: 1
      logic-not-delete-value: 0
```

> 注意新版可以直接在 yml 中配置

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

**测试删除**

![image-20240325172519617](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325172519617.png)

再次查询1号，无法查询出来，因为自动拼接了 deleted=0 的条件

![image-20240325172600583](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325172600583.png)

## 性能分析插件

新版本去掉此插件 此处略，可以参见 https://www.cnblogs.com/feng-zhi/p/14846560.html

## 条件构造器 wrapper

**十分重要:Wrapper**

我们写一些复杂的sql就可以用它来进行替代

| 条件构造器   | 含义               | 相关方法                                                     |
| ------------ | ------------------ | ------------------------------------------------------------ |
| allEq        | 全部相等或个别NULL | `allEq({id:1,name:"老王",age:null})`--->`id = 1 and name = '老王' and age is null`<br />`allEq({id:1,name:"老王",age:null}, false)`--->`id = 1 and name = '老王'` |
| eq           | 相等               | `eq("name", "老王")`--->`name = '老王'`                      |
| ne           | 不等               | `ne("name", "老王")`--->`name <> '老王'`                     |
| gt           | 大于               | `gt("age", 18)`--->`age > 18`                                |
| ge           | 大于等于           | `ge("age", 18)`--->`age >= 18`                               |
| lt           | 小于               | `lt("age", 18)`--->`age < 18`                                |
| le           | 小于等于           | `le("age", 18)`--->`age <= 18`                               |
| between      | 在区间内           | `between("age", 18, 30)`--->`age between 18 and 30`          |
| notBetween   | 不在区间内         | `notBetween("age", 18, 30)`--->`age not between 18 and 30`   |
| like         | 模糊查询           | `like("name", "王")`--->`name like '%王%'`                   |
| notLike      | 模糊查询           | `notLike("name", "王")`--->`name not like '%王%'`            |
| likeLeft     | 模糊查询           | `likeLeft("name", "王")`--->`name like '%王'`                |
| likeRight    | 模糊查询           | `likeRight("name", "王")`--->`name like '王%'`               |
| notLikeLeft  | 模糊查询           | `notLikeLeft("name", "王")`--->`name not like '%王'`         |
| notLikeRight | 模糊查询           | `notLikeRight("name", "王")`--->`name not like '王%'`        |
| isNull       | 字段为NULL         | `isNull("name")`--->`name is null`                           |
| isNotNull    | 字段不为NULL       | `isNotNull("name")`--->`name is not null`                    |
| in           | 在给定集合内       | `in("age",{1,2,3})`--->`age in (1,2,3)`<br />`in("age", 1, 2, 3)`--->`age in (1,2,3)` |
| notIn        | 不在给定集合内     | `notIn("age",{1,2,3})`--->`age not in (1,2,3)`<br />`notIn("age", 1, 2, 3)`--->`age not in (1,2,3)` |
| inSql        | 在给定sql结果下    | `inSql("id", "select id from table where id < 3")`--->`id in (select id from table where id < 3)` |
| notInSql     | 不在给定sql结果下  | `notInSql("age", "1,2,3,4,5,6")`--->`age not in (1,2,3,4,5,6)` |
| groupBy      | 分组               | `groupBy("id", "name")`--->`group by id,name`                |
| orderByAsc   | 升序               | `orderByAsc("id", "name")`--->`order by id ASC,name ASC`     |
| orderByDesc  | 降序               | `orderByDesc("id", "name")`--->`order by id DESC,name DESC`  |
| orderBy      | 默认               | `orderBy(true, true, "id", "name")`--->`order by id ASC,name ASC` |
| having       | 分组相关           | `having("sum(age) > 10")`--->`having sum(age) > 10`          |
| func         |                    | `func(i -> if(true) {i.eq("id", 1)} else {i.ne("id", 1)})`   |
| or           | 拼接和嵌套         | `eq("id",1).or().eq("name","老王")`--->`id = 1 or name = '老王'`<br />`or(i -> i.eq("name", "李白").ne("status", "活着"))`--->`or (name = '李白' and status <> '活着')` |
| and          | and嵌套            | `and(i -> i.eq("name", "李白").ne("status", "活着"))`--->`and (name = '李白' and status <> '活着')` |
| nested       | 正常嵌套           | `nested(i -> i.eq("name", "李白").ne("status", "活着"))`--->`(name = '李白' and status <> '活着')` |

以一个简单查询的wrapper为例

![image-20240325184506697](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240325184506697.png)

## 代码自动生成器

详见官网相关页面

[代码生成器（新） | MyBatis-Plus (baomidou.com)](https://baomidou.com/pages/779a6e/)