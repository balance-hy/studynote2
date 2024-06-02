# RabbitMQ

微服务一旦拆分，必然涉及到服务之间的相互调用，目前我们服务之间调用采用的都是基于OpenFeign的调用。

这种调用中，**调用者发起请求后需要等待服务提供者执行业务返回结果后，才能继续执行后面的业务**。也就是说调用者在调用过程中处于阻塞状态，因此我们成这种调用方式为**同步调用**，也可以叫**同步通讯**。

但在很多场景下，我们可能需要采用**异步通讯**的方式，为什么呢？

![image-20240531131525055](http://img.balance.wiki//blog/image-20240531131525055.png)

解读：

- 同步通讯：就如同打视频电话，双方的交互都是实时的。因此同一时刻你只能跟一个人打视频电话。
- 异步通讯：就如同发微信聊天，双方的交互不是实时的，你不需要立刻给对方回应。因此你可以多线操作，同时跟多人聊天。

两种方式各有优劣，打电话可以立即得到响应，但是你却不能跟多个人同时通话。发微信可以同时与多个人收发微信，但是往往响应会有延迟。

**所以，如果我们的业务需要实时得到服务提供方的响应，则应该选择同步通讯（同步调用）。**

**而如果我们追求更高的效率，并且不需要实时响应，则应该选择异步通讯（异步调用）。** 

同步调用的方式我们已经学过了，之前的OpenFeign调用就是。但是：

- 异步调用又该如何实现？
- 哪些业务适合用异步调用来实现呢？

## MQ基础

### 初识MQ

#### 同步调用

举个例子，我们以昨天留给大家作为作业的**余额支付功能**为例来分析，首先看下整个流程：

![image-20240531132340561](http://img.balance.wiki//blog/image-20240531132340561.png)

目前我们采用的是基于OpenFeign的同步调用，也就是说业务执行流程是这样的：

- 支付服务需要先调用用户服务完成余额扣减
- 然后支付服务自己要更新支付流水单的状态
- 然后支付服务调用交易服务，更新业务订单状态为已支付

三个步骤依次执行。

这其中就存在3个问题：

**第一**，**拓展性差**

我们目前的业务相对简单，但是随着业务规模扩大，产品的功能也在不断完善。

在大多数电商业务中，用户支付成功后都会以短信或者其它方式通知用户，告知支付成功。假如后期产品经理提出这样新的需求，你怎么办？是不是要在上述业务中再加入通知用户的业务？

某些电商项目中，还会有积分或金币的概念。假如产品经理提出需求，用户支付成功后，给用户以积分奖励或者返还金币，你怎么办？是不是要在上述业务中再加入积分业务、返还金币业务？

。。。

最终你的支付业务会越来越臃肿：

![image-20240531133046888](http://img.balance.wiki//blog/image-20240531133046888.png)

也就是说每次有新的需求，现有支付逻辑都要跟着变化，代码经常变动，不符合开闭原则（修改开放，扩展关闭），拓展性不好。

**第二**，**性能下降**

由于我们采用了同步调用，调用者需要等待服务提供者执行完返回结果后，才能继续向下执行，也就是说每次远程调用，调用者都是阻塞等待状态。最终整个业务的响应时长就是每次远程调用的执行时长之和：

![image-20240531133300811](http://img.balance.wiki//blog/image-20240531133300811.png)

假如每个微服务的执行时长都是50ms，则最终整个业务的耗时可能高达300ms，性能太差了。

**第三，级联失败**

由于我们是基于OpenFeign调用交易服务、通知服务。当交易服务、通知服务出现故障时，整个事务都会回滚，交易失败。

这其实就是同步调用的**级联失败**问题。

但是大家思考一下，我们假设用户余额充足，扣款已经成功，此时我们应该确保支付流水单更新为已支付，确保交易成功。毕竟收到手里的钱没道理再退回去吧。

因此，这里不能因为短信通知、更新订单状态失败而回滚整个事务。

综上，同步调用的方式存在下列问题：

- 拓展性差
- 性能下降
- 级联失败

而要解决这些问题，我们就必须用**异步调用**的方式来代替**同步调用**。

**优点：**

* 时效性强，等待到结果后才返回，

#### 异步调用

异步调用方式其实就是基于消息通知的方式，一般包含三个角色：

- 消息发送者：投递消息的人，就是原来的调用方
- 消息Broker（代理）：管理、暂存、转发消息，你可以把它理解成微信服务器
- 消息接收者：接收和处理消息的人，就是原来的服务提供方

![image-20240531133713579](http://img.balance.wiki//blog/image-20240531133713579.png)

在异步调用中，发送者不再直接同步调用接收者的业务接口，而是发送一条消息投递给消息Broker。然后接收者根据自己的需求从消息Broker那里订阅消息。每当发送方发送消息后，接受者都能获取消息并处理。

这样，发送消息的人和接收消息的人就完全解耦了。

还是以余额支付业务为例：

![image-20240531133939326](http://img.balance.wiki//blog/image-20240531133939326.png)

除了扣减余额、更新支付流水单状态以外，其它调用逻辑全部取消。而是改为发送一条消息到Broker。而相关的微服务都可以订阅消息通知，一旦消息到达Broker，则会分发给每一个订阅了的微服务，处理各自的业务。

假如产品经理提出了新的需求，比如要在支付成功后更新用户积分。支付代码完全不用变更，而仅仅是让积分服务也订阅消息即可：

![image-20240531134002527](http://img.balance.wiki//blog/image-20240531134002527.png)

不管后期增加了多少消息订阅者，作为支付服务来讲，执行问扣减余额、更新支付流水状态后，发送消息即可。业务耗时仅仅是这三部分业务耗时，仅仅100ms，大大提高了业务性能。

另外，不管是交易服务、通知服务，还是积分服务，他们的业务与支付关联度低。现在采用了异步调用，解除了耦合，他们即便执行过程中出现了故障，也不会影响到支付服务。

综上，异步调用的优势包括：

- 耦合度更低
- 性能更好
- 业务拓展性强
- 故障隔离，避免级联失败
- 缓存消息，流量削峰填谷

当然，异步通信也并非完美无缺，它存在下列缺点：

- **完全依赖于Broker的可靠性、安全性和性能**
- **架构复杂，后期维护和调试麻烦**

#### 技术选型

消息Broker，目前常见的实现方案就是**消息队列（MessageQueue）**，简称为MQ.

目比较常见的MQ实现：

- ActiveMQ
- RabbitMQ
- RocketMQ
- Kafka

几种常见MQ的对比：

|            | RabbitMQ                | ActiveMQ                       | RocketMQ   | Kafka      |
| ---------- | ----------------------- | ------------------------------ | ---------- | ---------- |
| 公司/社区  | Rabbit                  | Apache                         | 阿里       | Apache     |
| 开发语言   | Erlang                  | Java                           | Java       | Scala&Java |
| 协议支持   | AMQP，XMPP，SMTP，STOMP | OpenWire,STOMP，REST,XMPP,AMQP | 自定义协议 | 自定义协议 |
| 可用性     | 高                      | 一般                           | 高         | 高         |
| 单机吞吐量 | 一般                    | 差                             | 高         | 非常高     |
| 消息延迟   | 微秒级                  | 毫秒级                         | 毫秒级     | 毫秒以内   |
| 消息可靠性 | 高                      | 一般                           | 高         | 一般       |

追求可用性：Kafka、 RocketMQ 、RabbitMQ

追求可靠性：RabbitMQ、RocketMQ

追求吞吐能力：RocketMQ、Kafka

追求消息低延迟：RabbitMQ、Kafka

**据统计，目前国内消息队列使用最多的还是RabbitMQ，再加上其各方面都比较均衡，稳定性也好，因此我们课堂上选择RabbitMQ来学习。**

### RabbitMQ

RabbitMQ是基于Erlang语言开发的开源消息通信中间件，官网地址：

https://www.rabbitmq.com/

接下来，我们就学习它的基本概念和基础用法。

#### 安装

我们同样基于Docker来安装RabbitMQ，使用下面的命令即可：

```Shell
docker run \
 -e RABBITMQ_DEFAULT_USER=root \
 -e RABBITMQ_DEFAULT_PASS=123456 \
 -v mq-plugins:/plugins \
 --name mq \
 --hostname mq \
 -p 15672:15672 \
 -p 5672:5672 \
 --network hm-net\
 -d \
 rabbitmq:3.8-management
```

可以看到在安装命令中有两个映射的端口：

- 15672：RabbitMQ提供的管理控制台的端口
- 5672：RabbitMQ的消息发送处理接口

安装完成后，我们访问 http://192.168.40.129:15672即可看到管理控制台。首次访问需要登录，默认的用户名和密码在配置文件中已经指定了。

登录后即可看到管理控制台总览页面：

![image-20240531150534927](http://img.balance.wiki//blog/image-20240531150534927.png)

RabbitMQ对应的架构如图：

![image-20240531150621706](http://img.balance.wiki//blog/image-20240531150621706.png)

其中包含几个概念：

- **`publisher`**：生产者，也就是发送消息的一方
- **`consumer`**：消费者，也就是消费消息的一方
- **`queue`**：队列，存储消息。生产者投递的消息会暂存在消息队列中，等待消费者处理
- **`exchange`**：交换机，负责消息路由。生产者发送的消息由交换机决定投递到哪个队列。
- **`virtual host`**：虚拟主机，起到数据隔离的作用。每个虚拟主机相互独立，有各自的exchange、queue

#### 收发消息

需求：在RabbitMQ的控制台完成下列操作：

* 新建队列hello.queue1和hello.queue2
* 向默认的amp.fanout交换机发送一条消息
* 查看消息是否到达hello.queue1和hello.queue2
* 总结规律

![image-20240531151052134](http://img.balance.wiki//blog/image-20240531151052134.png)

##### 交换机

我们打开Exchanges选项卡，可以看到已经存在很多交换机：

![image-20240602181300831](http://img.balance.wiki//blog/image-20240602181300831.png)

我们点击任意交换机，即可进入交换机详情页面。利用控制台中的publish message 发送一条消息：

![image-20240602181359417](http://img.balance.wiki//blog/image-20240602181359417.png)

***这里是由控制台模拟了生产者发送的消息。由于没有消费者存在，路由失败，最终消息丢失了，这样说明交换机没有存储消息的能力。***

##### 队列

我们打开`Queues`选项卡，新建两个队列：

![image-20240602181028801](http://img.balance.wiki//blog/image-20240602181028801.png).

##### 绑定消息 路由-交换机

点击`Exchanges`选项卡，点击`amq.fanout`交换机，进入交换机详情页，然后点击`Bindings`菜单，在表单中填写要绑定的队列名称

![image-20240602181627438](http://img.balance.wiki//blog/image-20240602181627438.png)

将交换机和两个队列进行绑定！

##### 发送消息

再次回到exchange页面，找到刚刚绑定的`amq.fanout`，点击进入详情页，再次发送一条消息：

![image-20240602181842429](http://img.balance.wiki//blog/image-20240602181842429.png)

回到`Queues`页面，可以发现`hello.queue`中已经有一条消息了：

![image-20240602181904294](http://img.balance.wiki//blog/image-20240602181904294.png)

点击队列名称，进入详情页，查看队列详情，这次我们点击get message：

![image-20240602181922494](http://img.balance.wiki//blog/image-20240602181922494.png)

可以看到消息到达队列了：

![image-20240602181949600](http://img.balance.wiki//blog/image-20240602181949600.png)

这个时候如果有消费者监听了MQ的`hello.queue1`或`hello.queue2`队列，自然就能接收到消息了。

#### 数据隔离

需求：在RabbitMQ的控制台完成下列操作：

- 新建一个用户hmall
- 为hmall用户创建一个virtualhost
- 测试不同virtualhost之间的数据隔离现象

##### 用户管理

点击`Admin`选项卡，首先会看到RabbitMQ控制台的用户管理界面：

这里的用户都是RabbitMQ的管理或运维人员。目前只有安装RabbitMQ时添加的`itheima`这个用户。仔细观察用户表格中的字段，如下：

- `Name`：`itheima`，也就是用户名
- `Tags`：`administrator`，说明`itheima`用户是超级管理员，拥有所有权限
- `Can access virtual host`： `/`，可以访问的`virtual host`，这里的`/`是默认的`virtual host`

对于小型企业而言，出于成本考虑，我们通常只会搭建一套MQ集群，公司内的多个不同项目同时使用。这个时候为了避免互相干扰， 我们会利用`virtual host`的隔离特性，将不同项目隔离。一般会做两件事情：

- 给每个项目创建独立的运维账号，将管理权限分离。
- 给每个项目创建不同的`virtual host`，将每个项目的数据隔离。

比如，我们给黑马商城创建一个新的用户，命名为`hmall`：

![image-20240602182417262](http://img.balance.wiki//blog/image-20240602182417262.png)

你会发现此时hmall用户没有任何`virtual host`的访问权限：

![image-20240602182555994](http://img.balance.wiki//blog/image-20240602182555994.png)

##### virtual host

我们先退出登录，切换到刚刚创建的hmall用户登录，然后点击`Virtual Hosts`菜单，进入`virtual host`管理页：

可以看到目前只有一个默认的`virtual host`，名字为 `/`。

我们可以给黑马商城项目创建一个单独的`virtual host`，而不是使用默认的`/`。

创建完成后如图：

![image-20240602182815610](http://img.balance.wiki//blog/image-20240602182815610.png)

由于我们是登录`hmall`账户后创建的`virtual host`，因此回到`users`菜单，你会发现当前用户已经具备了对`/hmall`这个`virtual host`的访问权限了：

![image-20240602182835408](http://img.balance.wiki//blog/image-20240602182835408.png)

此时，点击页面右上角的`virtual host`下拉菜单，切换`virtual host`为 `/hmall`，然后再次查看queues选项卡，会发现之前的队列已经看不到了：

![image-20240602182915588](http://img.balance.wiki//blog/image-20240602182915588.png)

这就是基于`virtual host `的隔离效果。

### SpringAMQP

将来我们开发业务功能的时候，肯定不会在控制台收发消息，而是应该基于编程的方式。

> 由于`RabbitMQ`采用了AMQP协议，因此它具备跨语言的特性。任何语言只要遵循AMQP协议收发消息，都可以与`RabbitMQ`交互。并且`RabbitMQ`官方也提供了各种不同语言的客户端。

但是，RabbitMQ官方提供的Java客户端编码相对复杂，一般生产环境下我们更多会结合Spring来使用。而Spring的官方刚好基于RabbitMQ提供了这样一套消息收发的模板工具：SpringAMQP。并且还基于SpringBoot对其实现了自动装配，使用起来非常方便。

SpringAmqp的官方地址：

https://spring.io/projects/spring-amqp

SpringAMQP提供了三个功能：

- 自动声明队列、交换机及其绑定关系
- 基于注解的监听器模式，异步接收消息
- 封装了RabbitTemplate工具，用于发送消息

#### 导入Demo工程

在课前资料给大家提供了一个Demo工程，方便我们学习SpringAMQP的使用

将其复制到你的工作空间，然后用Idea打开，项目结构如图：

包括三部分：

- mq-demo：父工程，管理项目依赖
- publisher：消息的发送者
- consumer：消息的消费者

![image-20240602184526796](http://img.balance.wiki//blog/image-20240602184526796.png)

在mq-demo这个父工程中，已经配置好了SpringAMQP相关的依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.itcast.demo</groupId>
    <artifactId>mq-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>publisher</module>
        <module>consumer</module>
    </modules>
    <packaging>pom</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.12</version>
        <relativePath/>
    </parent>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--AMQP依赖，包含RabbitMQ-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <!--单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
    </dependencies>
</project>
```

因此，子工程中就可以直接使用SpringAMQP了。

#### 快速入门

在之前的案例中，我们都是经过交换机发送消息到队列，不过有时候为了测试方便，我们也可以直接向队列发送消息，跳过交换机。

在入门案例中，我们就演示这样的简单模型，如图：

![image-20240602184912787](http://img.balance.wiki//blog/image-20240602184912787.png)

也就是：

- publisher直接发送消息到队列
- 消费者监听并处理队列中的消息

> **注意**：这种模式一般测试使用，很少在生产中使用。

需求如下:

- 利用控制台创建队列simple.queue
- 在publisher服务中，利用SpringAMQP直接向simple.queue发送消息
- 在consumer服务中，利用SpringAMQP编写消费者，监听simple.queue队列

为了方便测试，我们现在控制台新建一个队列：`simple.queue`

##### 消息发送

首先在父工程中引l入spring-amqp依赖，这样publisher和consumer服务都可以使用:

```xml
<!--AMQP依赖，包含RabbitMQ-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

再配置MQ地址，在`publisher`服务的`application.yml`中添加配置：

```yaml
spring:
  rabbitmq:
    host: 192.168.40.129
    port: 5672 # 注意15672是控制台端口，5672才是收发消息端口
    username: hmall
    password: 123456
    virtual-host: /hmall
```

然后在`publisher`服务中编写测试类`SpringAmqpTest`，并利用`RabbitTemplate`实现消息发送：

> 注意测试类需包名一致，否则无法启动

```java
@SpringBootTest
public class SpringAmqpTest {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void testSimpleQueue() {
        // 队列名称
        String queueName = "simple.queue";
        // 消息
        String message = "hello, spring amqp!";
        // 发送消息
        rabbitTemplate.convertAndSend(queueName, message);
    }
}
```

打开控制台，可以看到消息已经发送到队列中：

![image-20240602191251875](http://img.balance.wiki//blog/image-20240602191251875.png)

##### 消息接收

首先配置MQ地址，在`consumer`服务的`application.yml`中添加配置：

```YAML
spring:
  rabbitmq:
    host: 192.168.40.129 # 你的虚拟机IP
    port: 5672 # 端口
    username: hmall # 用户名
    password: 123456 # 密码
    virtual-host: /hmall # 虚拟主机
```

然后在`consumer`服务的`com.itheima.consumer.listener`包中新建一个类`SpringRabbitListener`，代码如下：

```java
@Component
public class SpringRabbitListener {
        // 利用RabbitListener来声明要监听的队列信息
    // 将来一旦监听的队列中有了消息，就会推送给当前服务，调用当前方法，处理消息。
    // 可以看到方法体中接收的就是消息体的内容
    @RabbitListener(queues = "simple.queue")
    public void listenSimpleQueueMessage(String msg) throws InterruptedException {
        System.out.println("spring 消费者接收到消息：【" + msg + "】");
    }
}
```

##### 测试

启动consumer服务，然后在publisher服务中运行测试代码，发送MQ消息。最终consumer收到消息：

![image-20240602191912408](http://img.balance.wiki//blog/image-20240602191912408.png)

#### WorkQueues模型

Work queues，任务模型。简单来说就是**让多个消费绑定到一个队列，共同消费队列中的消息**。

![image-20240602192012747](http://img.balance.wiki//blog/image-20240602192012747.png)

当消息处理比较耗时的时候，可能生产消息的速度会远远大于消息的消费速度。长此以往，消息就会堆积越来越多，无法及时处理。

此时就可以使用`WorkQueues`模型，**多个消费者共同处理消息处理，消息处理的速度就能大大提高**了。

接下来，我们就来模拟这样的场景。

**首先，我们在控制台创建一个新的队列，命名为`work.queue`：**

##### 消息发送

这次我们循环发送，模拟大量消息堆积现象。

在publisher服务中的SpringAmqpTest类中添加一个测试方法：

```java
/**
     * workQueue
     * 向队列中不停发送消息，模拟消息堆积。
     */
@Test
public void testWorkQueue() throws InterruptedException {
    // 队列名称
    String queueName = "simple.queue";
    // 消息
    String message = "hello, message_";
    for (int i = 0; i < 50; i++) {
        // 发送消息，每20毫秒发送一次，相当于每秒发送50条消息
        rabbitTemplate.convertAndSend(queueName, message + i);
        Thread.sleep(20);
    }
}
```

##### 消息接收

要模拟多个消费者绑定同一个队列，我们在consumer服务的SpringRabbitListener中添加2个新的方法：

```java
@RabbitListener(queues = "work.queue")
public void testWorkQueue1(String msg){
    log.error("消费者1接收到消息 "+msg+" , "+ LocalDateTime.now());
}
@RabbitListener(queues = "work.queue")
public void testWorkQueue2(String msg){
    log.info("消费者2接收到消息 "+msg+" , "+ LocalDateTime.now());
}
```

##### 测试

启动ConsumerApplication后，在执行publisher服务中刚刚编写的发送测试方法testWorkQueue。

可以看到消费者1和消费者2每人消费了25条消息：

```
06-02 19:27:21:116  INFO 21432 --- [ntContainer#1-1] c.i.consumer.mq.SpringRabbitListener     : 消费者2接收到消息 hello, message_44 , 2024-06-02T19:27:21.116330300
06-02 19:27:21:138 ERROR 21432 --- [ntContainer#2-1] c.i.consumer.mq.SpringRabbitListener     : 消费者1接收到消息 hello, message_45 , 2024-06-02T19:27:21.138329900
06-02 19:27:21:158  INFO 21432 --- [ntContainer#1-1] c.i.consumer.mq.SpringRabbitListener     : 消费者2接收到消息 hello, message_46 , 2024-06-02T19:27:21.158824800
06-02 19:27:21:180 ERROR 21432 --- [ntContainer#2-1] c.i.consumer.mq.SpringRabbitListener     : 消费者1接收到消息 hello, message_47 , 2024-06-02T19:27:21.180808300
06-02 19:27:21:200  INFO 21432 --- [ntContainer#1-1] c.i.consumer.mq.SpringRabbitListener     : 消费者2接收到消息 hello, message_48 , 2024-06-02T19:27:21.200788
06-02 19:27:21:224 ERROR 21432 --- [ntContainer#2-1] c.i.consumer.mq.SpringRabbitListener     : 消费者1接收到消息 hello, message_49 , 2024-06-02T19:27:21.224316800
```

也就是说消息是平均分配给每个消费者

**但这并没有考虑到消费者的处理能力，比如我们让这两消费者设置了`Thead.sleep`，模拟任务耗时：**

- **消费者1 sleep了20毫秒，相当于每秒钟处理50个消息**
- **消费者2 sleep了200毫秒，相当于每秒处理5个消息**

```
消费者1接收到消息：【hello, message_0】21:06:00.869555300
消费者2........接收到消息：【hello, message_1】21:06:00.884518
消费者1接收到消息：【hello, message_2】21:06:00.907454400
消费者1接收到消息：【hello, message_4】21:06:00.953332100
消费者1接收到消息：【hello, message_6】21:06:00.997867300
消费者1接收到消息：【hello, message_8】21:06:01.042178700
消费者2........接收到消息：【hello, message_3】21:06:01.086478800
```

**还是平均分配！**

这会导致1个消费者空闲，另一个消费者忙的不可开交。没有充分利用每一个消费者的能力，最终消息处理的总耗时超过了1秒。这样显然是有问题的。

##### 能者多劳

默认情况下，RabbitMQ的会将消息依次轮询投递给绑定在队列上的每一个消费者。但这并没有考虑到消费者是否已经处理完消息，可能出现消息堆积。因此我们需要修改application.yml，设置preFetch值为1，确保同一时刻最多投递给消费者1条消息:

```yaml
spring:
  rabbitmq:
    listener: 
      simple: 
        prefetch: 1 # 每次只能获取一条消息，处理完成才能获取下一个消息
```

可以发现，由于消费者1处理速度较快，所以处理了更多的消息；消费者2处理速度较慢，只处理了6条消息。而最终总的执行耗时也在1秒左右，大大提升。

正所谓能者多劳，这样充分利用了每一个消费者的处理能力，可以有效避免消息积压问题。

##### 总结

Work模型的使用：

- 多个消费者绑定到一个队列，同一条消息只会被一个消费者处理
- 通过设置prefetch来控制消费者预取的消息数量

#### 交换机类型

在之前的两个测试案例中，都没有交换机，生产者直接发送消息到队列。而一旦引入交换机，消息发送的模式会有很大变化：

![image-20240602193906701](http://img.balance.wiki//blog/image-20240602193906701.png)

可以看到，在订阅模型中，多了一个exchange角色，而且过程略有变化：

- **Publisher**：生产者，不再发送消息到队列中，而是发给交换机
- **Exchange**：交换机，一方面，接收生产者发送的消息。另一方面，知道如何处理消息，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃。到底如何操作，取决于Exchange的类型。
- **Queue**：消息队列也与以前一样，接收消息、缓存消息。不过队列一定要与交换机绑定。
- **Consumer**：消费者，与以前一样，订阅队列，没有变化

**Exchange（交换机）只负责转发消息，不具备存储消息的能力**，因此如果没有任何队列与Exchange绑定，或者没有符合路由规则的队列，那么消息会丢失！

**交换机的类型有四种：**

- **Fanout**：广播，将消息交给所有绑定到交换机的队列。我们最早在控制台使用的正是Fanout交换机
- **Direct**：订阅，基于RoutingKey（路由key）发送给订阅了消息的队列
- **Topic**：通配符订阅，与Direct类似，只不过RoutingKey可以使用通配符
- **Headers**：头匹配，基于MQ的消息头匹配，用的较少。

> RabbitMQ引入交换机的主要原因是为了提供更灵活的消息路由机制。在没有交换机的情况下，生产者直接将消息发送到队列，这种方式在一些简单的场景下可能是可行的，但在复杂的系统中，这种方式的灵活性和扩展性都较差。
>
> 交换机的引入，使得生产者和队列之间解耦，生产者只需要将消息发送到交换机，由交换机根据预设的规则将消息路由到一个或多个队列。这样，即使系统的队列结构发生变化，生产者的代码也无需做出相应的改变。
>
> 比如如果一个消息需要多个微服务进行消费，此时就可以利用交换机的广播，实现只发一次，多个服务绑定的队列都收到消息！

#### Fanout交换机

Fanout，英文翻译是扇出，我觉得在MQ中叫广播更合适。

在广播模式下，消息发送流程是这样的：

![image-20240602195208893](http://img.balance.wiki//blog/image-20240602195208893.png)

- 1）  可以有多个队列
- 2）  每个队列都要绑定到Exchange（交换机）
- 3）  生产者发送的消息，只能发送到交换机
- 4）  交换机把消息发送给绑定过的所有队列
- 5）  订阅队列的消费者都能拿到消息

##### 声明队列和交换机

在控制台创建队列`fanout.queue1`和`fanout.queue2`：

然后再创建一个交换机：

![image-20240602195041086](http://img.balance.wiki//blog/image-20240602195041086.png)

然后绑定两个队列到交换机：

![image-20240602195458833](http://img.balance.wiki//blog/image-20240602195458833.png)

##### 消息发送

在publisher服务的SpringAmqpTest类中添加测试方法：

```java
@Test
public void testFanoutExchange() {
    // 交换机名称
    String exchangeName = "hmall.fanout";
    // 消息
    String message = "hello, everyone!";
    rabbitTemplate.convertAndSend(exchangeName, "", message);
}
```

##### 消息接收

在consumer服务的SpringRabbitListener中添加两个方法，作为消费者：

```Java
@RabbitListener(queues = "fanout.queue1")
public void listenFanoutQueue1(String msg) {
    System.out.println("消费者1接收到Fanout消息：【" + msg + "】");
}

@RabbitListener(queues = "fanout.queue2")
public void listenFanoutQueue2(String msg) {
    System.out.println("消费者2接收到Fanout消息：【" + msg + "】");
}
```

##### 总结

交换机的作用是什么？

- 接收publisher发送的消息
- 将消息按照规则路由到与之绑定的队列
- 不能缓存消息，路由失败，消息丢失
- FanoutExchange的会将消息路由到每个绑定的队列

#### Direct交换机

DirectExchange会将接收到的消息根据规则路由到指定的Queue，因此称为**定向**路由。

- 每一个Queue都与Exchange设置一个BindingKey
- 发布者发送消息时，指定消息的RoutingKey
- Exchange将消息路由到BindingKey与消息RoutingKey一致的队列

![image-20240602200851631](http://img.balance.wiki//blog/image-20240602200851631.png)

**案例需求如下：**

1. 声明一个名为`hmall.direct`的交换机
2. 声明队列`direct.queue1`，绑定`hmall.direct`，`bindingKey`为`blud`和`red`
3. 声明队列`direct.queue2`，绑定`hmall.direct`，`bindingKey`为`yellow`和`red`
4. 在`consumer`服务中，编写两个消费者方法，分别监听direct.queue1和direct.queue2 
5. 在publisher中编写测试方法，向`hmall.direct`发送消息 

> 比如用户下单，当支付完成后，可能需要积分服务，风控服务等等，但如果此时取消支付，就无需积分服务了，此时就不能使用广播。
>
> 而使用Direct交换机，可以设置多个bindingkey，消息的key与队列key之一匹配才添加到队列当中

##### 声明队列和交换机

首先在控制台声明两个队列`direct.queue1`和`direct.queue2`，这里不再展示过程

然后声明一个direct类型的交换机，命名为`hmall.direct`:

![image-20240602201951502](http://img.balance.wiki//blog/image-20240602201951502.png)

然后使用`red`和`blue`作为key，绑定`direct.queue1`到`hmall.direct`：

![image-20240602202138235](http://img.balance.wiki//blog/image-20240602202138235.png)

同理，使用`red`和`yellow`作为key，绑定`direct.queue2`到`hmall.direct`，步骤略

##### 消息接收

在consumer服务的SpringRabbitListener中添加方法：

```java
@RabbitListener(queues = "direct.queue1")
public void listenDirectQueue1(String msg) {
    System.out.println("消费者1接收到direct.queue1的消息：【" + msg + "】");
}

@RabbitListener(queues = "direct.queue2")
public void listenDirectQueue2(String msg) {
    System.out.println("消费者2接收到direct.queue2的消息：【" + msg + "】");
}
```

###### 消息发送

在publisher服务的SpringAmqpTest类中添加测试方法：

```java
@Test
public void testSendDirectExchange() {
    // 交换机名称
    String exchangeName = "hmall.direct";
    // 消息
    String message = "红色警报！日本乱排核废水，导致海洋生物变异，惊现哥斯拉！";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "red", message);
}
```

由于使用的red这个key，所以两个消费者都收到了消息：

![image-20240602202416097](http://img.balance.wiki//blog/image-20240602202416097.png)

我们再切换为blue这个key：

```java
@Test
public void testSendDirectExchange() {
    // 交换机名称
    String exchangeName = "hmall.direct";
    // 消息
    String message = "最新报道，哥斯拉是居民自治巨型气球，虚惊一场！";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "blue", message);
}
```

你会发现，只有消费者1收到了消息：

![image-20240602202438123](http://img.balance.wiki//blog/image-20240602202438123.png)

##### 总结

描述下Direct交换机与Fanout交换机的差异？

- Fanout交换机将消息路由给每一个与之绑定的队列
- Direct交换机根据RoutingKey判断路由给哪个队列
- 如果多个队列具有相同的RoutingKey，则与Fanout功能类似

#### Topic交换机

> `Topic`类型的`Exchange`与`Direct`相比，都是可以根据`RoutingKey`把消息路由到不同的队列。
>
> 只不过`Topic`类型`Exchange`可以让队列在绑定`BindingKey` 的时候使用通配符！

`BindingKey` 一般都是有一个或多个单词组成，多个单词之间以`.`分割，例如： `item.insert`

通配符规则：

- `#`：匹配一个或多个词
- `*`：匹配不多不少恰好1个词

举例：

- `item.#`：能够匹配`item.spu.insert` 或者 `item.spu`
- `item.*`：只能匹配`item.spu`

例如：

![image-20240602202658168](http://img.balance.wiki//blog/image-20240602202658168.png)

假如此时publisher发送的消息使用的`RoutingKey`共有四种：

- `china.news `代表有中国的新闻消息；
- `china.weather` 代表中国的天气消息；
- `japan.news` 则代表日本新闻
- `japan.weather` 代表日本的天气消息；

解释：

- `topic.queue1`：绑定的是`china.#` ，凡是以 `china.`开头的`routing key` 都会被匹配到，包括：
  - `china.news`
  - `china.weather`
- `topic.queue2`：绑定的是`#.news` ，凡是以 `.news`结尾的 `routing key` 都会被匹配。包括:
  - `china.news`
  - `japan.news`

接下来，我们就按照上图所示，来演示一下Topic交换机的用法。

首先，在控制台按照图示例子创建队列、交换机，并利用通配符绑定队列和交换机。此处步骤略。最终结果如下：

![image-20240602202904777](http://img.balance.wiki//blog/image-20240602202904777.png)

##### 消息发送

在publisher服务的SpringAmqpTest类中添加测试方法：

```Java
/**
 * topicExchange
 */
@Test
public void testSendTopicExchange() {
    // 交换机名称
    String exchangeName = "hmall.topic";
    // 消息
    String message = "喜报！孙悟空大战哥斯拉，胜!";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "china.news", message);
}
```

##### 消息接收

在consumer服务的SpringRabbitListener中添加方法：

```Java
@RabbitListener(queues = "topic.queue1")
public void listenTopicQueue1(String msg){
    System.out.println("消费者1接收到topic.queue1的消息：【" + msg + "】");
}

@RabbitListener(queues = "topic.queue2")
public void listenTopicQueue2(String msg){
    System.out.println("消费者2接收到topic.queue2的消息：【" + msg + "】");
}
```

##### 总结

描述下Direct交换机与Topic交换机的差异？

- Topic交换机接收的消息RoutingKey必须是多个单词，以 **`.`** 分割
- Topic交换机与队列绑定时的bindingKey可以指定通配符
- `#`：代表0个或多个词
- `*`：代表1个词

#### 声明队列和交换机

在之前我们都是基于RabbitMQ控制台来创建队列、交换机。但是在实际开发时，队列和交换机是程序员定义的，将来项目上线，又要交给运维去创建。

那么程序员就需要把程序中运行的所有队列和交换机都写下来，交给运维。在这个过程中是很容易出现错误的。

**因此推荐的做法是由程序启动时检查队列和交换机是否存在，如果不存在自动创建。**

##### 基本API

SpringAMQP提供了一个Queue类，用来创建队列：

![image-20240602203201316](http://img.balance.wiki//blog/image-20240602203201316.png)

SpringAMQP还提供了一个Exchange接口，来表示所有不同类型的交换机：

![image-20240602203218677](http://img.balance.wiki//blog/image-20240602203218677.png)

我们可以自己创建队列和交换机，不过**SpringAMQP还提供了ExchangeBuilder来简化这个过程**：

![image-20240602203252676](http://img.balance.wiki//blog/image-20240602203252676.png)

**而在绑定队列和交换机时**，则需要使用BindingBuilder来创建Binding对象：

![image-20240602203313588](http://img.balance.wiki//blog/image-20240602203313588.png)

##### fanout示例

> 一般可以在消费者这边声明队列、交换机和绑定关系，因为作为发送方来讲，发送方不需要关心队列，发送发唯一关心的是交换机，向某个交换机发消息就可以了

```java
package com.itheima.consumer.config;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.FanoutExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FanoutConfig {
    /**
     * 声明交换机
     * @return Fanout类型交换机
     */
    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange("hmall.fanout");
        //return ExchangeBuilder.fanoutExchange("hmall.fanout").build();//这样也可以
    }

    /**
     * 第1个队列
     */
    @Bean
    public Queue fanoutQueue1(){
        return new Queue("fanout.queue1");//默认durable为true
        //return QueueBuilder.durable("fanout.queue1").build();//这样也可以
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue1(Queue fanoutQueue1, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue1).to(fanoutExchange);
        //或者 return BindingBuilder.bind(fanoutQueue1()).to(fanoutExchange());不要方法参数
    }

    /**
     * 第2个队列
     */
    @Bean
    public Queue fanoutQueue2(){
        return new Queue("fanout.queue2");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue2(Queue fanoutQueue2, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue2).to(fanoutExchange);
    }
}
```

##### direct示例

direct模式由于要绑定多个KEY，会非常麻烦，每一个Key都要编写一个binding：

```java
@Configuration
public class DirectConfig {

    /**
     * 声明交换机
     * @return Direct类型交换机
     */
    @Bean
    public DirectExchange directExchange(){
        return ExchangeBuilder.directExchange("hmall.direct").build();
    }

    /**
     * 第1个队列
     */
    @Bean
    public Queue directQueue1(){
        return new Queue("direct.queue1");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue1WithRed(Queue directQueue1, DirectExchange directExchange){
        return BindingBuilder.bind(directQueue1).to(directExchange).with("red");
    }
    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue1WithBlue(Queue directQueue1, DirectExchange directExchange){
        return BindingBuilder.bind(directQueue1).to(directExchange).with("blue");
    }

    /**
     * 第2个队列
     */
    @Bean
    public Queue directQueue2(){
        return new Queue("direct.queue2");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue2WithRed(Queue directQueue2, DirectExchange directExchange){
        return BindingBuilder.bind(directQueue2).to(directExchange).with("red");
    }
    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue2WithYellow(Queue directQueue2, DirectExchange directExchange){
        return BindingBuilder.bind(directQueue2).to(directExchange).with("yellow");
    }
}
```

##### 基于注解声明

基于@Bean的方式声明队列和交换机比较麻烦，Spring还提供了基于注解方式来声明。

例如，我们同样声明Direct模式的交换机和队列：

```Java
@RabbitListener(
    bindings = @QueueBinding(
        value = @Queue(name = "direct.queue1"),
    	exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
    	key = {"red", "blue"}
))
public void listenDirectQueue1(String msg){
    System.out.println("消费者1接收到direct.queue1的消息：【" + msg + "】");
}

@RabbitListener(
    bindings = @QueueBinding(
        value = @Queue(name = "direct.queue2"),
    	exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
    	key = {"red", "yellow"}
))
public void listenDirectQueue2(String msg){
    System.out.println("消费者2接收到direct.queue2的消息：【" + msg + "】");
}
```

是不是简单多了。

再试试Topic模式：

```Java
@RabbitListener(
    bindings = @QueueBinding(value = @Queue(name = "topic.queue1"),
    exchange = @Exchange(name = "hmall.topic", type = ExchangeTypes.TOPIC),
    key = "china.#"
))
public void listenTopicQueue1(String msg){
    System.out.println("消费者1接收到topic.queue1的消息：【" + msg + "】");
}

@RabbitListener(
    bindings = @QueueBinding(
        value = @Queue(name = "topic.queue2"),
    	exchange = @Exchange(name = "hmall.topic", type = ExchangeTypes.TOPIC),
    	key = "#.news"
))
public void listenTopicQueue2(String msg){
    System.out.println("消费者2接收到topic.queue2的消息：【" + msg + "】");
}
```

#### 消息转换器









































