# Redis

> Redis 是一种通用的数据存储系统，可以用作数据库、缓存、消息队列等，而缓存是一种特定的数据存储技术，用于提高数据访问速度和性能。Redis 可以作为缓存的一种选择，但它不限于缓存，还可以用于许多其他用途。

Redis是一个**基于内存**的**key-value结构**数据库

- 基于内存存储，读写性能高
- 适合存储热点数据（热点商品、资讯、新闻)   因为内存有限
- 企业应用广泛

> mysql 持久化到磁盘

## 安装Redis

[Releases · microsoftarchive/redis (github.com)](https://github.com/microsoftarchive/redis/releases)

默认端口号：6379

![image-20240410145034406](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410145034406.png)

控制台键入 `redis-server.exe` 即可运行

![image-20240410145437865](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410145437865.png)

然后通过客户端连接到redis服务 指定 ip 和 对应端口

```shell
redis-cli.exe -h localhost -p 6379
```

现在是无需密码即可连接，显然并不安全，可以修改配置文件，加入密码

```shell
# requirepass foobared
requirepass 123456 # 后跟具体密码
```

注意需要指定配置文件开启服务，否则依旧可以直接登录

```shell
redis-server.exe redis.windows.conf
# 命令行 -a 后面加上密码
redis-cli.exe -h localhost -p 6379 -a 123456
```

## Redis 数据类型

Redis存储的是key-value结构的数据，其中**key是字符串类型**，value有5种常用的数据类型：

* 字符串 string 
  * 普通字符串
* 哈希 hash 
  * 类似于Java中的HashMap结构
* 列表list 
  * 按照插入顺序排序，可以有重复元素，类似于Java中的LinkedList
* 集合 set 
  * 无序集合，没有重复元素，类似于Java中的HashSet
* 有序集合 sorted set/zset 
  * 集合中每个元素关联一个分数（score），根据分数升序排序，没有重复元素

![image-20240410154100115](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410154100115.png)

## Redis 常用命令

### 字符串

| 常用命令                | 含义                                            |
| ----------------------- | ----------------------------------------------- |
| SET key value           | 设置指定key的值                                 |
| GET key                 | 获取指定key的值                                 |
| SETEX key seconds value | 设置指定key的值，并将key的过期时间设为seconds秒 |
| SETNX key value         | 只有在key不存在时设置key的值 返回0              |

### 哈希

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象

| 常用命令             | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| HSET key field value | 将哈希表 key 中的字段 field 的值设为 value 相同field会覆盖value |
| HGET key field       | 获取存储在哈希表中指定字段的值                               |
| HDEL key field       | 删除存储在哈希表中的指定字段                                 |
| HKEYS key            | 获取哈希表中所有字段                                         |
| HVALS key            | 获取哈希表中所有值                                           |

### 列表

Redis 列表是简单的字符串列表，按照插入顺序排序

| 常用命令                  | 含义                               |
| ------------------------- | ---------------------------------- |
| LPUSH key value1 [value2] | 将一个或多个值插入到列表头部(左边) |
| LRANGE key start stop     | 获取列表指定范围内的元素           |
| RPOP key                  | 移除并获取列表最后一个元素(右边)   |
| LLEN key                  | 获取列表长度                       |

### 集合 set

Redis set 是string类型的无序集合。集合成员是唯一的，集合中不能出现重复的数据

| 常用命令                   | 含义                        |
| -------------------------- | --------------------------- |
| SADD key member1 [member2] | 向集合添加一个或多个成员    |
| SMEMBERS key               | 返回集合中的所有成员        |
| SCARD key                  | 获取集合的成员数            |
| SINTER key1 [key2]         | 返回给定所有集合的交集      |
| SUNION key1 [key2]         | 返回所有给定集合的并集 去重 |
| SREM key member1 [member2] | 删除集合中一个或多个成员    |

### 有序集合 sorted set/zset 

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数

默认升序

| 常用命令                                 | 含义                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| ZADD key score1 member1 [score2 member2] | 向有序集合添加一个或多个成员                                 |
| ZRANGE key start stop [WITHSCORES]       | 通过索引区间返回有序集合中指定区间内的成员 [WITHSCORES]用于是否显示分数 |
| ZINCRBY key increment member             | 有序集合中对指定成员的分数加上 increment                     |
| ZREM key member [member ...]             | 移除有序集合中的一个或多个成员                               |

### 通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令

| 常用命令     | 含义                                                  |
| ------------ | ----------------------------------------------------- |
| KEYS pattern | 查找所有符合给定模式( pattern)的 key  keys * 返回所有 |
| EXISTS key   | 检查给定 key 是否存在                                 |
| TYPE key     | 返回 key 所储存的值的类型                             |
| DEL key      | 该命令用于在 key 存在是删除 key                       |

## Java中的redis

Redis 的 Java 客户端很多，常用的几种：

- Jedis
- Lettuce
- Spring Data Redis

Spring Data Redis 是 Spring 的一部分，对 Redis 底层开发包进行了高度封装。

**更类似java原生方法**

**引入依赖**

```xml
<dependency>    
    <groupId>org.springframework.boot</groupId>   
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

**配置数据源**

```yml
spring: 
	redis:   
		host: localhost    
		port: 6379    
		password: 123456
```

**编写配置类，创建 RedisTemplate 对象**

```java
@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        log.info("初始化 redisTemplate");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置 redis 的 连接工厂对象  starter已经创建好并注入进spring中了 我们直接使用
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置redis 序列化器 这样存储到redis中 key value 不会乱码
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        return redisTemplate;
    }

}
```

**通过 RedisTemplate 对象操作Redis**

RedisTemplate 针对大量api进行了归类封装 ，将同一数据类型的操作封装为对应的Operation接口，具体分类如下：

* ValueOperations：string数据操作
* ListOperations：list类型的数据操作
* HashOperations：hash类型的数据操作 

* SetOperations：set类型数据操作

* ZSetOperations：zset类型数据操作

```java
ValueOperations valueOperations = redisTemplate.opsForValue(); //字符串

ListOperations listOperations = redisTemplate.opsForList();//列表

HashOperations hashOperations = redisTemplate.opsForHash();//哈希

SetOperations setOperations = redisTemplate.opsForSet();//集合

ZSetOperations zSetOperations = redisTemplate.opsForZSet();//有序集合
```

### 字符串

```java
@SpringBootTest
public class TestRedis {
    @Autowired
    RedisTemplate redisTemplate;

    @Test
    public void testRedisString(){
        ValueOperations valueOperations = redisTemplate.opsForValue();
        //设置值
        valueOperations.set("name","sky");
        //获取值
        String name = (String) valueOperations.get("name");
        System.out.println(name);

        //设置值 且设置有效期
        valueOperations.set("code","1234",3, TimeUnit.MINUTES);
        //注意java中实际上可以传入对象，但是最后转为字符串存储在redis中

    }
}
```

### 哈希

```java
@Test
public void testRedisHash(){
    HashOperations hashOperations = redisTemplate.opsForHash();

    hashOperations.put("hash","name","sky");
    hashOperations.put("hash","age","20");

    System.out.println(hashOperations.get("hash","name"));

    //hashOperations.delete("hash","name");

    //获取所有 key
    System.out.println(hashOperations.keys("hash"));
    //获取所有 value
    System.out.println(hashOperations.values("hash"));
    //获取所有 key-value
    System.out.println(hashOperations.entries("hash"));
}
```

### 列表

```java
@Test
public void testRedisList(){
    ListOperations listOperations = redisTemplate.opsForList();

    listOperations.leftPushAll("list","a","b","c");

    System.out.println(listOperations.leftPop("list"));

    System.out.println(listOperations.range("list", 0, -1));

    System.out.println(listOperations.size("list"));
}
```

### 集合

```java
@Test
public void testRedisSet(){
    SetOperations setOperations = redisTemplate.opsForSet();
    setOperations.add("set1","a","b","c");
    setOperations.add("set2","d","e","f");
    //获取对应集合元素
    System.out.println(setOperations.members("set1"));
    //获取对应集合元素的个数
    System.out.println(setOperations.size("set1"));

    //取交集
    System.out.println(setOperations.intersect("set1", "set2"));

    //取并集
    System.out.println(setOperations.union("set1", "set2"));

    //删除集合元素
    setOperations.remove("set1","a");
}
```

### 有序集合

```java
@Test
public void testRedisZSet(){
    ZSetOperations zSetOperations = redisTemplate.opsForZSet();
    zSetOperations.add("zset","a",1);
    zSetOperations.add("zset","b",2);
    zSetOperations.add("zset","c",3);

    //输出有序集合元素
    System.out.println(zSetOperations.range("zset", 0, -1));
    //加分数
    zSetOperations.incrementScore("zset","a",5);

    zSetOperations.remove("zset","c");

}
```

### 通用命令

```java
@Test
public void testRedisCommon(){
    // keys pattern
    System.out.println(redisTemplate.keys("set*"));

    //exists
    System.out.println(redisTemplate.hasKey("list"));

    //type
    System.out.println(redisTemplate.type("list"));

    //del
    redisTemplate.delete("list");
}
```



