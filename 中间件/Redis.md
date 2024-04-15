# Redis

> Redis 是一种通用的数据存储系统，可以用作数据库、缓存、消息队列等，而缓存是一种特定的数据存储技术，用于提高数据访问速度和性能。Redis 可以作为缓存的一种选择，但它不限于缓存，还可以用于许多其他用途。
>
> Redis诞生于2009年全称是RemoteDictionaryServer，远程词典服务器，是一个基于内存的键值型NoSQL数据库。

Redis是一个**基于内存**的**key-value结构**数据库

- 基于内存存储，读写性能高，支持数据持久化到磁盘
- 适合存储热点数据（热点商品、资讯、新闻)   因为内存有限
- 企业应用广泛
- **6.0后对网络请求是多线程，但核心命令的执行仍旧为单线程**
- 低延迟，速度快（基于内存、I0多路复用、良好的编码）
- 支持主从集群、分片集群

> mysql 持久化到磁盘

|          | SQL                                                   | NOSQL                                              |
| -------- | ----------------------------------------------------- | -------------------------------------------------- |
| 数据结构 | 结构化                                                | 非结构化                                           |
| 数据关联 | 关联的                                                | 无关联                                             |
| 查询方式 | SQL查询                                               | 非SQL                                              |
| 事务特性 | ACID                                                  | BASE                                               |
| 存储方式 | 磁盘                                                  | 内存                                               |
| 扩展性   | 垂直                                                  | 水平                                               |
| 使用场景 | 1.数据结构固定 2.相关业务对数据安全性、一致性要求较高 | 数据结构不固定 对一致性安全性要求不高 对性能有要求 |

常见NOSQL数据库：

* **键值型 Redis**
* 文档型 MongDB
* 列类型 HBase
* Graph类型 Neo4j

## 安装Redis -windows

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

## Redis -Linux

直接docker安装就可以

先拉取镜像

```shell
docker pull redis
```

需要redis的配置文件，这里最好还是去redis的官方去下载一个redis使用里面的配置文件即可

> [redis/redis.conf at 6.2.6 · redis/redis · GitHub](https://github.com/redis/redis/blob/6.2.6/redis.conf)
>
> [最详细的docker中安装并配置redis-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1670205)

```shell
# 允许访问的地址，默认是127.0.0.1，会导致只能在本地访问。修改为0.0.0.0则可以在任意IP访问，生产环境不要设置为0.0.0.0
bind 0.0.0.0
# 守护进程，修改为yes后即可后台运行 可以docker指定 -d 
daemonize no
# 密码，设置后访问Redis必须输入密码
requirepass 123456

# 其它常见配置
# 监听的端口
port 6379
# 工作目录，默认是当前目录，也就是运行redis-server时的命令，日志、持久化等文件会保存在这个目录
dir .
# 数据库数量，设置为1，代表只使用1个库，默认有16个库，编号0~15
databases 1
# 设置redis能够使用的最大内存
maxmemory 512mb
# 日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```

创建本地与docker映射的目录，即本地存放的位置，这里主要讲conf文件和docker文件关联

在root目录下新建redis目录，其下新建conf目录存放我们自定义的conf文件

根据配置文件，创建redis容器并运行 容器内客户端在`/usr/local/bin`

```shell
docker run -d -v ./redis/conf:/usr/local/etc/redis --name redis -p 6379:6379 redis redis-server /usr/local/etc/redis/redis.conf
```

现在默认已经运行了，我们可以进入容器内部，使用redis-cli测试

```shell
docker exec -it redis bash

cd /usr/local/bin

redis-cli -h ip地址 -p 6379
```

![image-20240415134549119](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415134549119.png)

注意，真正验证是以用户名 密码，因为我们没设置用户名，所以可以直接密码登录

```
auth username password
```

方便起见，可以使用图形化界面 `annother redis desktop`

![image-20240415135021373](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415135021373.png)

## Redis 常用数据类型

Redis存储的是key-value结构的数据，其中**key是字符串类型**，value有5种常用的数据类型：

* 字符串 string 
  * 普通字符串
* 哈希 hash 
  * 类似于Java中的HashMap结构
* 列表list 
  * 按照插入顺序排序，可以有重复元素，类似于Java中的LinkedList 双向
  * 插入删除速度快，查询速度一般
* 集合 set 
  * 无序集合，没有重复元素，类似于Java中的HashSet
* 有序集合 sorted set/zset 
  * 集合中每个元素关联一个分数（score），根据分数升序排序，没有重复元素

![image-20240410154100115](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410154100115.png)

![image-20240415141326431](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415141326431.png)

## Redis 常用命令

### 字符串

| 常用命令                               | 含义                                            |
| -------------------------------------- | ----------------------------------------------- |
| SET key value                          | 设置指定key的值                                 |
| GET key                                | 获取指定key的值                                 |
| SETEX key seconds value                | 设置指定key的值，并将key的过期时间设为seconds秒 |
| SETNX key value                        | 只有在key不存在时设置key的值 返回0              |
| MSET                                   | 设置多个                                        |
| MGET                                   | 获取多个                                        |
| incr / incrby  decr/decrby incrbyfloat | 整型自增1和自增指定                             |

### 哈希

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象

![image-20240415141644204](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415141644204.png)

| 常用命令                    | 含义                                                         |
| --------------------------- | ------------------------------------------------------------ |
| HSET key field value        | 将哈希表 key 中的字段 field 的值设为 value 相同field会覆盖value |
| HGET key field              | 获取存储在哈希表中指定字段的值                               |
| HDEL key field              | 删除存储在哈希表中的指定字段                                 |
| HKEYS key                   | 获取哈希表中所有字段                                         |
| HVALS key                   | 获取哈希表中所有值                                           |
| HMGET / HMSET / HGETALL等等 |                                                              |

### 列表

Redis 列表是简单的字符串列表，按照插入顺序排序

| 常用命令                  | 含义                                        |
| ------------------------- | ------------------------------------------- |
| LPUSH key value1 [value2] | 将一个或多个值插入到列表头部(左边) 相应右边 |
| LRANGE key start stop     | 获取列表指定范围内的元素                    |
| RPOP key                  | 移除并获取列表最后一个元素(右边) 相应左边   |
| LLEN key                  | 获取列表长度                                |

BLPOP和BRPOP：与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil

### 集合 set

Redis set 是string类型的无序集合。集合成员是唯一的，集合中不能出现重复的数据。

支持交集，并集，差集等功能，**好友列表可以通过此实现**

![image-20240415143457830](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415143457830.png)

| 常用命令                   | 含义                        |
| -------------------------- | --------------------------- |
| SADD key member1 [member2] | 向集合添加一个或多个成员    |
| SMEMBERS key               | 返回集合中的所有成员        |
| SCARD key                  | 获取集合的成员数            |
| SINTER key1 [key2]         | 返回给定所有集合的交集      |
| SUNION key1 [key2]         | 返回所有给定集合的并集 去重 |
| SDIFF key1 [key2]          | 差集                        |
| SREM key member1 [member2] | 删除集合中一个或多个成员    |
| sismembers                 | 获取set中的所有元素         |

### 有序集合 sorted set/zset 

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数，**跳表和hash表**

**多用于排行榜等功能**

默认升序

| 常用命令                                 | 含义                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| ZADD key score1 member1 [score2 member2] | 向有序集合添加一个或多个成员                                 |
| ZRANGE key start stop [WITHSCORES]       | 通过索引区间返回有序集合中指定区间内的成员 [WITHSCORES]用于是否显示分数 |
| ZINCRBY key increment member             | 有序集合中对指定成员的分数加上 increment                     |
| ZREM key member [member ...]             | 移除有序集合中的一个或多个成员                               |
| ZSCORE key member                        | 查询指定元素分数                                             |
| ZRank key member                         | 查询指定元素排名                                             |
| ZCARD key                                | 元素个数                                                     |
| Zcount key min max                       | 统计SCORE值在给定范围内的所有元素个数                        |
| ZRANGE key min max                       | 按score值排序后，获取指定范围内的元素                        |
| ZDIFF ZINTER ZUNION                      | 差 交 并                                                     |

注意：所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可

### 通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令

| 常用命令       | 含义                                                    |
| -------------- | ------------------------------------------------------- |
| KEYS pattern   | 查找所有符合给定模式( pattern)的 key  `keys * 返回所有` |
| EXISTS key     | 检查给定 key 是否存在                                   |
| TYPE key       | 返回 key 所储存的值的类型                               |
| DEL key        | 该命令用于在 key 存在时删除 key                         |
| select 0       | 选择0号数据库                                           |
| help [command] | 获取 命令 帮助文档                                      |
| EXPIRE key     | 给某个key设置有效期                                     |
| TTL key        | 查看 key 的有效期                                       |

## Java中的redis

Redis 的 Java 客户端很多，常用的几种：

- Jedis
  - 以Redis命令作为方法名称，学习成本低，简单实用，但是**Jedis实例是线程不安全的，多线程环境下需要基于连接池来使用**

- Lettuce
  - Lettuce是基于Netty实现的，支持同步、异步和响应式编程方式，并且**是线程安全的**。支持Redis的哨兵模式、集群模式和管道模式

- Redisson
  - Redisson是一个基于Redis**实现的分布式、可伸缩的Java数据结构集合**。包含了诸如Map、Queue、Lock、Semaphore、AtomicLong等强大功能

- Spring Data Redis

Spring Data Redis 是 Spring 的一部分，对 Redis 底层开发包进行了高度封装。

**更类似java原生方法** 

![image-20240415151804042](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415151804042.png)

![image-20240415151854204](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415151854204.png)

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

**编写配置类，创建 RedisTemplate 对象** 不编写配置类也可以直接使用，但就不能设置序列化器了！！！！

> 为了节省内存空间，我们并不会使用JSON序列化器来处理value，而是统一使用String序列化器，要求只能存储String类型的key和value。当需要存储Java对象时，手动完成对象的序列化和反序列化。
>
> Spring默认提供了一个StringRedisTemplate类，它的key和value的序列化方式默认就是String方式。省去了我们自定义RedisTemplate的过程：
>
> 我们自己序列化自己反序列化对象从而节省占用的内存空间（自动序列化会存类名）

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

![image-20240415154330383](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415154330383.png)

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

## Redis实战

### 短信登录

> redis 的共享session应用



### 商户查询缓存

> 企业的缓存使用技巧缓存雪崩、穿透等问题解决



### 达人探店

> 基于List的点赞列表
>
> 基于SortedSet的点赞排行榜



### 优惠卷秒杀

> Redis的计数器、Lua脚本
>
> Redis分布式锁
>
> Redis的三种消息队列



###  好友关注

> 基于Set集合的关注、取关、共同关注消息推送等功能



### 附近的商户

> Redis的GeoHash的应用



### 用户签到

> Redis的BitMap数据统计功能



### UV统计

> Redis的HyperLogLog的统计功能

