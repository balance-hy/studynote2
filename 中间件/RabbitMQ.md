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
>
> AMQP（Advanced Message Queuing Protocol，高级消息队列协议）是一个进程间传递异步消息的网络协议。

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

Spring的消息发送代码接收的消息体是一个Object：

![image-20240603093313016](http://img.balance.wiki//blog/image-20240603093313016.png)

而在数据传输时，它会把你发送的消息序列化为字节发送给MQ，接收消息的时候，还会把字节反序列化为Java对象。

只不过，**默认情况下Spring采用的序列化方式是JDK序列化**。众所周知，JDK序列化存在下列问题：

- 数据体积过大
- 有安全漏洞
- 可读性差

我们可以来测试一下

##### 测试默认转换器

> 需求：测试利用SpringAMQP发送对象类型的消息
>
> - 声明一个队列，名为object.queue
> - 编写单元测试，向队列中直接发送一条消息，消息类型为Map
> - 在控制台查看消息，总结你能发现的问题

队列直接在控制台声明即可，略

我们在publisher模块的SpringAmqpTest中新增一个消息发送的代码，发送一个Map对象

```java
@Test
public void testConvert(){
    //队列名称
    String queueName = "object.queue";
    //消息
    HashMap<String, Object> map = new HashMap<>();
    map.put("name","小明");
    map.put("age",18);
    //发送消息
    rabbitTemplate.convertAndSend(queueName,map);
}
```

发送消息后查看控制台：

![image-20240603095139818](http://img.balance.wiki//blog/image-20240603095139818.png)

可以看到消息格式非常不友好。

##### 配置JSON转换器

显然，JDK序列化方式并不合适。我们希望消息体的体积更小、可读性更高，因此可以使用JSON方式来做序列化和反序列化。

在`publisher`和`consumer`两个服务中都引入依赖：

```XML
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.9.10</version>
</dependency>
```

> 注意，如果项目中引入了`spring-boot-starter-web`依赖，则无需再次引入`Jackson`依赖。

配置消息转换器，在`publisher`和`consumer`两个服务的启动类中添加一个Bean即可：

```Java
@Bean
public MessageConverter messageConverter(){
    // 1.定义消息转换器
    Jackson2JsonMessageConverter jackson2JsonMessageConverter = new Jackson2JsonMessageConverter();
    // 2.配置自动创建消息id，用于识别不同消息，也可以在业务中基于ID判断是否是重复消息
    jackson2JsonMessageConverter.setCreateMessageIds(true);
    return jackson2JsonMessageConverter;
}
```

消息转换器中添加的messageId可以便于我们将来做幂等性判断。

此时，我们到MQ控制台**删除**`object.queue`中的旧的消息。然后再次执行刚才的消息发送的代码，到MQ的控制台查看消息结构：

![image-20240603100340723](http://img.balance.wiki//blog/image-20240603100340723.png)

##### 消费者接收Object

我们在consumer服务中定义一个新的消费者，publisher是用Map发送，那么消费者也一定要用Map接收，格式如下：

```Java
@RabbitListener(queues = "object.queue")
public void listenObjectQueue(Map<String, Object> map) {
    map.forEach((k,v)->{
        System.out.println(k+" "+v);
    });
}
```

### 业务改造-实战

案例需求：改造余额支付功能，将支付成功后基于OpenFeign的交易服务的更新订单状态接口的同步调用，改为基于RabbitMQ的异步通知。

如图：

![image-20240603101917984](http://img.balance.wiki//blog/image-20240603101917984.png)

说明：目前没有通知服务和积分服务，因此我们只关注交易服务，步骤如下：

- 定义`direct`类型交换机，命名为`pay.direct`
- 定义消息队列，命名为`trade.pay.success.queue`
- 将`trade.pay.success.queue`与`pay.direct`绑定，`BindingKey`为`pay.success`
- 支付成功时不再调用交易服务更新订单状态的接口，而是发送一条消息到`pay.direct`，发送消息的`RoutingKey`  为`pay.success`，消息内容是订单id
- 交易服务监听`trade.pay.success.queue`队列，接收到消息后更新订单状态为已支付

#### 配置MQ

不管是生产者还是消费者，都需要配置MQ的基本信息。分为两步：

1）添加依赖：

```XML
<!--消息发送-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

2）配置MQ地址：

```YAML
spring:
  rabbitmq:
    host: 192.168.40.129 # 你的虚拟机IP
    port: 5672 # 端口
    virtual-host: /hmall # 虚拟主机
    username: hmall # 用户名
    password: 123456 # 密码
```

这可以抽取出来放到nacos中。

#### 配置消息转换器

因为每一个微服务都会需要，所以放在hm-common中，在config包下新建MqConfig

```java
@Configuration
public class MqConfig {
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
```

> 注意此时无法生效，因为无法扫描到该包下的配置，可以利用spring的自动装配原理，在resources目录下新建META-INF文件夹，其下新建spring.factories文件，里面新增该类全限定类名即可

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.hmall.common.config.MyBatisConfig,\
  com.hmall.common.config.MvcConfig,\
  com.hmall.common.config.JsonConfig,\
  com.hmall.common.config.MqConfig
```

#### 接收消息

在trade-service服务中定义一个消息监听类：

```java
package com.hmall.trade.listener;

import com.hmall.trade.service.IOrderService;
import lombok.RequiredArgsConstructor;
import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
public class PayStatusListener {
    private final IOrderService orderService;

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(name = "trade.pay.success.queue",durable = "true"),
            exchange = @Exchange(name="pay.topic"),
            key = "pay.success"
    ))
    public void listenPaySuccess(Long orderId){
        orderService.markOrderPaySuccess(orderId);
    }

}
```

#### 发送消息

修改`pay-service`服务下的`com.hmall.pay.service.impl.PayOrderServiceImpl`类中的`tryPayOrderByBalance`方法

```java
    @Override
    @Transactional
    public void tryPayOrderByBalance(PayOrderFormDTO payOrderFormDTO) {
        // 1.查询支付单
        PayOrder po = getById(payOrderFormDTO.getId());
        // 2.判断状态
        if(!PayStatus.WAIT_BUYER_PAY.equalsValue(po.getStatus())){
            // 订单不是未支付，状态异常
            throw new BizIllegalException("交易已支付或关闭！");
        }
        // 3.尝试扣减余额
        userClient.deductMoney(payOrderFormDTO.getPw(), po.getAmount());
        // 4.修改支付单状态
        boolean success = markPayOrderSuccess(payOrderFormDTO.getId(), LocalDateTime.now());
        if (!success) {
            throw new BizIllegalException("交易已支付或关闭！");
        }
        // 5.修改订单状态
//        Order order = new Order();
//        order.setId(po.getBizOrderNo());
//        order.setStatus(2);
//        order.setPayTime(LocalDateTime.now());
//        tradeClient.updateById(order);
//        tradeClient.markOrderPaySuccess(po.getBizOrderNo());
        try {
            rabbitTemplate.convertAndSend("pay.direct", "pay.success", po.getBizOrderNo());
        }catch (Exception e){
            log.info("修改订单状态消息发送失败");
        }
            
    }
```

#### 测试

此时启动发现报错

```
Failed to introspect Class [com.hmall.common.config.MqConfig] from ClassLoader [jdk.internal.loader.ClassLoaders$AppClassLoader@1f89ab83]
```

这是因为所有的服务都依赖于common，当MqConfig配置到Spring时，除去trade和pay服务，其他服务也会自动注入MqConfig中的消息转换器Bean，但其他服务并没有引入amqp的依赖，自然注入失败报错。两种解决方法都行，一种是common里面引amqp的依赖，另一种就是ConditionalOnClass。

```java
@ConditionalOnClass(RabbitTemplate.class)//只有其它地方用到了RabbitTemplate，这个MqConfig才会生效
```

这里简便起见直接引依赖即可

现在就可以正常使用啦！

### 练习

改造下单功能，将基于OpenFeign的清理购物车同步调用，改为基于RabbitMQ的异步通知：

- 定义topic类型交换机，命名为`trade.direct`
- 定义消息队列，命名为`cart.clear.queue`
- 将`cart.clear.queue`与`trade.topic`绑定，`BindingKey`为`order.create`
- 下单成功时不再调用清理购物车接口，而是发送一条消息到`trade.topic`，发送消息的`RoutingKey`  为`order.create`，消息内容是下单的具体商品、当前登录用户信息
- 购物车服务监听`cart.clear.queue`队列，接收到消息后清理指定用户的购物车中的指定商品

![image-20240603143426790](http://img.balance.wiki//blog/image-20240603143426790.png)

![image-20240603143510168](http://img.balance.wiki//blog/image-20240603143510168.png)

### 小结

某些业务中，需要根据登录用户信息处理业务，而基于MQ的异步调用并不会传递登录用户信息。前面我们的做法比较麻烦，至少要做两件事：

- 消息发送者在消息体中传递登录用户
- 消费者获取消息体中的登录用户，处理业务

这样做不仅麻烦，而且编程体验也不统一，毕竟我们之前都是使用UserContext来获取用户。

***思考一下：有没有更优雅的办法传输登录用户信息，让使用MQ的人无感知，依然采用UserContext来随时获取用户。***

> 在异步通知中，你可以将用户信息作为消息的一部分发送。具体来说，你可以将用户信息添加到你发送的消息体中，或者如果你的消息队列支持，你也可以将用户信息添加到消息的属性或头部信息中。
>
> 以下是一个使用RabbitMQ和Spring AMQP的例子，它将用户信息添加到消息的头部信息中：
>
> ```java
> MessageProperties messageProperties = new MessageProperties();
> messageProperties.setHeader("userId", UserContext.getUser());
> Message message = new Message(objectMapper.writeValueAsBytes(yourData), messageProperties);
> rabbitTemplate.send("exchangeName", "routingKey", message);
> ```
>
> 在上述代码中，`UserContext.getUser()`是获取用户信息的方法，`yourData`是你要发送的数据，`objectMapper`是Jackson的`ObjectMapper`实例，用于将对象转换为JSON格式的字节。
>
> 在接收端，你可以从消息的头部信息中获取用户信息：
>
> ```java
> @RabbitListener(queues = "queueName")
> public void handleMessage(Message message) {
>     String userId = (String) message.getMessageProperties().getHeaders().get("userId");
>     byte[] body = message.getBody();
>     YourDataType yourData = objectMapper.readValue(body, YourDataType.class);
>     // ...
> }
> ```
>
> 在上述代码中，`YourDataType`是你的数据类型，你需要将其替换为实际的类型。
>
> 这样，你就可以在异步通知中传递用户信息了。

## MQ高级

在昨天的练习作业中，我们改造了余额支付功能，在支付成功后利用`RabbitMQ`通知交易服务，更新业务订单状态为已支付。

但是大家思考一下，如果这里**MQ通知失败，支付服务中支付流水显示支付成功，而交易服务中的订单状态却显示未支付，数据出现了不一致。**

此时前端发送请求查询支付状态时，肯定是查询交易服务状态，会发现业务订单未支付，而用户自己知道已经支付成功，这就导致用户体验不一致。

因此，这里***我们必须尽可能确保MQ消息的可靠性，即：消息应该至少被消费者处理1次***

那么问题来了：

- **我们该如何确保MQ消息的可靠性**？
- **如果真的发送失败，有没有其它的兜底方案？**

> 不是所有的业务都有这样的可靠性要求，比如用户下单支付去清理购物车，那么清理购物车就算失败了，对用户也没有什么太大损失，但是我们知道99%的情况下是不会失败的，这种场景就不用考虑这个问题，就正常发送消息就行了，不用管那么多。
>
> 而对于订单状态来讲，这个对于可靠性要求很高，总不能说用户支付成功了，最后用户来查看订单依然显示未支付，这对用户来讲就有影响，这种对一致性就有一定的要求，所以可靠性就要保证好

### 发送者可靠性

首先，我们一起分析一下消息丢失的可能性有哪些。

消息从发送者发送消息，到消费者处理消息，需要经过的流程是这样的：

![image-20240603145215291](http://img.balance.wiki//blog/image-20240603145215291.png)

消息从生产者到消费者的每一步都可能导致消息丢失：

- 发送消息时丢失：
  - 生产者发送消息时连接MQ失败
  - 生产者发送消息到达MQ后未找到`Exchange`
  - 生产者发送消息到达MQ的`Exchange`后，未找到合适的`Queue`
  - 消息到达MQ后，处理消息的进程发生异常
- MQ导致消息丢失：
  - 消息到达MQ，保存到队列后，尚未消费就突然宕机
- 消费者处理消息时：
  - 消息接收后尚未处理突然宕机
  - 消息接收后处理过程中抛出异常

**综上，我们要解决消息丢失问题，保证MQ的可靠性，就必须从3个方面入手：**

- **确保生产者一定把消息发送到MQ**
- **确保MQ不会将消息弄丢**
- **确保消费者一定要处理消息**

#### 生产者重试机制

首先第一种情况，就是生产者发送消息时，出现了网络故障，导致与MQ的连接中断。

为了解决这个问题，SpringAMQP提供的消息发送时的重试机制。即：当`RabbitTemplate`与MQ连接超时后，多次重试。

修改`publisher`模块的`application.yaml`文件，添加下面的内容：

```YAML
spring:
  rabbitmq:
    connection-timeout: 1s # 设置MQ的连接超时时间
    template:
      retry:
        enabled: true # 开启超时重试机制
        initial-interval: 1000ms # 失败后的初始等待时间
        multiplier: 1 # 失败后下次的等待时长倍数，下次等待时长 = initial-interval * multiplier
        max-attempts: 3 # 最大重试次数
```

我们利用命令停掉RabbitMQ服务：

```Shell
docker stop mq
```

然后测试发送一条消息，会发现会每隔1秒重试1次，总共重试了3次。消息发送的超时重试机制配置成功了！

> **注意**：当网络不稳定的时候，利用重试机制可以有效提高消息发送的成功率。不过SpringAMQP提供的重试机制是**阻塞式**的重试，也就是说多次重试等待的过程中，当前线程是被阻塞的。
>
> 如果对于业务性能有要求，建议禁用重试机制。如果一定要使用，请合理配置等待时长和重试次数，当然也可以考虑使用异步线程来执行发送消息的代码。

#### 生产者确认机制

一般情况下，只要生产者与MQ之间的网路连接顺畅，基本不会出现发送消息丢失的情况，因此大多数情况下我们无需考虑这种问题。

不过，在少数情况下，也会出现消息发送到MQ之后丢失的现象，比如：

- MQ内部处理消息的进程发生了异常
- 生产者发送消息到达MQ后未找到`Exchange`
- 生产者发送消息到达MQ的`Exchange`后，未找到合适的`Queue`，因此无法路由

针对上述情况，RabbitMQ提供了生产者消息确认机制，包括`Publisher Confirm`和`Publisher Return`两种。在开启确认机制的情况下，当生产者发送消息给MQ后，MQ会根据消息处理的情况返回不同的**回执**。

具体如图所示：

![image-20240603150326829](http://img.balance.wiki//blog/image-20240603150326829.png)

总结如下：

- 当消息投递到MQ，但是**路由失败**时，通过**Publisher Return**返回异常信息，同时返回ack的确认信息，代表投递成功
- 临时消息投递到了MQ，并且入队成功，返回ACK，告知投递成功
- 持久消息投递到了MQ，并且入队完成持久化，返回ACK ，告知投递成功
- 其它情况都会返回NACK，告知投递失败

其中`ack`和`nack`属于**Publisher Confirm**机制，`ack`是投递成功；`nack`是投递失败。而`return`则属于**Publisher Return**机制。

默认两种机制都是关闭状态，需要通过配置文件来开启。

#### 实现生产者确认

##### 开启生产者确认

在publisher模块的`application.yaml`中添加配置：

```YAML
spring:
  rabbitmq:
    publisher-confirm-type: correlated # 开启publisher confirm机制，并设置confirm类型
    publisher-returns: true # 开启publisher return机制
```

这里`publisher-confirm-type`有三种模式可选：

- `none`：关闭confirm机制
- `simple`：同步阻塞等待MQ的回执
- `correlated`：MQ异步回调返回回执

一般我们推荐使用`correlated`，回调机制。

##### 定义ReturnCallBack

每个`RabbitTemplate`只需要配置一个`ReturnCallback`，因此我们可以在配置类中统一设置。我们在publisher模块定义一个配置类：

![image-20240604165713456](http://img.balance.wiki//blog/image-20240604165713456.png)

内容如下：

```Java
package com.itheima.publisher.config;

import lombok.AllArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.ReturnedMessage;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.context.annotation.Configuration;

import javax.annotation.PostConstruct;

@Slf4j
@AllArgsConstructor
@Configuration
public class MqConfig {
    private final RabbitTemplate rabbitTemplate;

    @PostConstruct
    public void init(){
        rabbitTemplate.setReturnsCallback(new RabbitTemplate.ReturnsCallback() {
            @Override
            public void returnedMessage(ReturnedMessage returned) {
                log.error("监听到了消息的return callback,");
                log.debug("exchange: {}", returned.getExchange());
                log.debug("routingKey: {}", returned.getRoutingKey());
                log.debug("message: {}", returned.getMessage());
                log.debug("replyCode: {}", returned.getReplyCode());
                log.debug("replyText: {}", returned.getReplyText());
            }
        });
    }
}
```

##### 定义ConfirmCallback

由于每个消息发送时的处理逻辑不一定相同，因此ConfirmCallback需要在每次发消息时定义。具体来说，是在调用RabbitTemplate中的convertAndSend方法时，**多传递一个参数,以此唯一确定一个消息**：

![image-20240604165926038](http://img.balance.wiki//blog/image-20240604165926038.png)

这里的CorrelationData中包含两个核心的东西：

- `id`：消息的唯一标示，MQ对不同的消息的回执以此做判断，避免混淆
- `SettableListenableFuture`：回执结果的Future对象

将来MQ的回执就会通过这个`Future`来返回，我们可以提前给`CorrelationData`中的`Future`添加回调函数来处理消息回执：

![image-20240604165943305](http://img.balance.wiki//blog/image-20240604165943305.png)

我们新建一个测试，向系统自带的交换机发送消息，并且添加`ConfirmCallback`：

```Java
@Test
void testPublisherConfirm() {
    // 1.创建CorrelationData
    CorrelationData cd = new CorrelationData(UUID.randomUUID().toString);//唯一id
    // 2.给Future添加ConfirmCallback
    cd.getFuture().addCallback(new ListenableFutureCallback<CorrelationData.Confirm>() {
        @Override
        public void onFailure(Throwable ex) {
            // 2.1.Future发生异常时的处理逻辑，基本不会触发
            log.error("spring amqp处理确认结果异常", ex);
        }
        @Override
        public void onSuccess(CorrelationData.Confirm result) {
            // 2.2.Future接收到回执的处理逻辑，参数中的result就是回执内容
            if(result.isAck()){ // result.isAck()，boolean类型，true代表ack回执，false 代表 nack回执
                log.debug("发送消息成功，收到 ack!");
            }else{ // result.getReason()，String类型，返回nack时的异常描述
                log.error("发送消息失败，收到 nack, reason : {}", result.getReason());
            }
        }
    });
    // 3.发送消息
    rabbitTemplate.convertAndSend("hmall.direct", "q", "hello", cd);
    //注意这是测试，方法结束jvm就销毁了，没有充足的时间接收回调
    Thread.sleep(2000);//休眠两秒，接收回调
}
```

执行结果如下：

![image-20240604171913086](http://img.balance.wiki//blog/image-20240604171913086.png)

可以看到，由于传递的`RoutingKey`是错误的，路由失败后，触发了`return callback`，同时也收到了ack。

当我们修改为正确的`RoutingKey`以后，就不会触发`return callback`了，只收到ack。

而如果连交换机都是错误的，则只会收到nack。

> **注意**：
>
> 开启生产者确认比较消耗MQ性能，一般不建议开启。而且大家思考一下触发确认的几种情况：
>
> - 路由失败：一般是因为RoutingKey错误导致，往往是编程导致
> - 交换机名称错误：同样是编程错误导致
> - MQ内部故障：这种需要处理，但概率往往较低。因此只有对消息可靠性要求非常高的业务才需要开启，而且仅仅需要开启ConfirmCallback处理nack就可以了。

### MQ的可靠性

在默认情况下，RabbitMQ会将接收到的信息保存在内存中以降低消息收发的延迟。这样会导致两个问题：

- 一旦MQ宕机，内存中的消息会丢失
- 内存空间有限，当消费者故障或处理过慢时，会导致消息积压，引发MQ阻塞

#### 数据持久化

为了提升性能，默认情况下MQ的数据都是在内存存储的临时数据，重启后就会消失。为了保证数据的可靠性，必须配置数据持久化，包括：

- 交换机持久化
- 队列持久化
- 消息持久化

我们以控制台界面为例来说明。

> 注意，mq默认当内存达上限向磁盘写入，即持久化，此时会造成较长时间中断，这里我们所说的持久化是每隔一段时间批量持久化，而非达限才写。

##### 交换机持久化

在控制台的`Exchanges`页面，添加交换机时可以配置交换机的`Durability`参数：

![image-20240605151503855](http://img.balance.wiki//blog/image-20240605151503855.png)

设置为`Durable`就是持久化模式，`Transient`就是临时模式。

>spring amqp 默认创建的就是持久化的交换机

##### 队列持久化

在控制台的Queues页面，添加队列时，同样可以配置队列的`Durability`参数：

![image-20240605151558369](http://img.balance.wiki//blog/image-20240605151558369.png)

除了持久化以外，你可以看到队列还有很多其它参数，有一些我们会在后期学习。

> spring amqp 默认创建的就是持久化的队列

##### 消息持久化

在控制台发送消息的时候，可以添加很多参数，而消息的持久化是要配置一个`properties`：

![image-20240605151646856](http://img.balance.wiki//blog/image-20240605151646856.png)

> **说明**：在开启持久化机制以后，如果同时还开启了生产者确认，**那么MQ会在消息持久化以后才发送ACK回执，进一步确保消息的可靠性。**
>
> 不过出于性能考虑，为了减少IO次数，发送到MQ的消息并不是逐条持久化到数据库的，而是每隔一段时间批量持久化。一般间隔在100毫秒左右，这就会导致ACK有一定的延迟，因此建议生产者确认全部采用异步方式。

***注意：同样，spring amqp 默认创建的就是持久化的消息，如果想创建非持久化的消息，可以如下创建：***

```java
@Test
void testPageOut(）{
    Message messagee= MessageBuilder
        .withBody("hello".getBytes(StandardCharsets.UTF_8))
        .setDeliveryMode(MesSageDeliveryMode.NON_PERSISTENT).build();//自己构建消息，底层就是创建一个message对象传输
    rabbitTemplate.convertAndSend("simple.queue",message);
}
```

#### LazyQueue 3.12后无需配置

从RabbitMQ的3.6.0版本开始，就增加了LazyQueue的概念，也就是**惰性队列**。

惰性队列的特征如下:

- 接收到消息后直接存入磁盘，不再存储到内存
- 消费者要消费消息时才会从磁盘中读取并加载到内存（**可以提前缓存部分消息到内存，最多2048条**）

**在3.12版本后，所有队列都是Lazy Queue模式，无法更改。**

> 因为磁盘读写慢，更换为全部磁盘存储会影响并发处理效率，所以采用了缓存的方式去缓解

##### 控制台配置Lazy模式

在添加队列的时候，添加`x-queue-mod=lazy`参数即可设置队列为Lazy模式：

![image-20240605152506890](http://img.balance.wiki//blog/image-20240605152506890.png)

##### 代码配置Lazy模式

在利用SpringAMQP声明队列的时候，添加`x-queue-mod=lazy`参数也可设置队列为Lazy模式：

```Java
@Bean
public Queue lazyQueue(){
    return QueueBuilder
            .durable("lazy.queue")
            .lazy() // 开启Lazy模式
            .build();
}
```

当然，我们也可以**基于注解来声明队列**并设置为Lazy模式：

```Java
@RabbitListener(queuesToDeclare = @Queue(
        name = "lazy.queue",
        durable = "true",
        arguments = @Argument(name = "x-queue-mode", value = "lazy") 
))
public void listenLazyQueue(String msg){
    log.info("接收到 lazy.queue的消息：{}", msg);
}
```

##### 更新已有队列为lazy模式

对于已经存在的队列，也可以配置为lazy模式，但是要通过设置policy实现。

可以基于命令行设置policy：

```Shell
rabbitmqctl set_policy Lazy "^lazy-queue$" '{"queue-mode":"lazy"}' --apply-to queues  
```

命令解读：

- `rabbitmqctl` ：RabbitMQ的命令行工具
- `set_policy` ：添加一个策略
- `Lazy` ：策略名称，可以自定义
- `"^lazy-queue$"` ：用正则表达式匹配队列的名字
- `'{"queue-mode":"lazy"}'` ：设置队列模式为lazy模式
- `--apply-to queues`：策略的作用对象，是所有的队列

当然，也可以在控制台配置policy，进入在控制台的`Admin`页面，点击`Policies`，即可添加配置：

![image-20240605152636632](http://img.balance.wiki//blog/image-20240605152636632.png)



### 消费者的可靠性

当RabbitMQ向消费者投递消息以后，需要知道消费者的处理状态如何。因为消息投递给消费者并不代表就一定被正确消费了，可能出现的故障有很多，比如：

- 消息投递的过程中出现了网络故障
- 消费者接收到消息后突然宕机
- 消费者接收到消息后，因处理不当导致异常
- ...

***一旦发生上述情况，消息也会丢失。因此，RabbitMQ必须知道消费者的处理状态，一旦消息处理失败才能重新投递消息。***

***但问题来了：RabbitMQ如何得知消费者的处理状态呢？***

#### 消费者确认机制

消费者确认机制（ConsumerAcknowledgement）是为了确认消费者是否成功处理消息。当消费者处理消息结束后应该向RabbitMQ发送一个回执，告知RabbitMQ自己消息处理状态：

回执有三种可选值：

- **ack**：成功处理消息，RabbitMQ从队列中删除该消息
- **nack**：消息处理失败，RabbitMQ需要再次投递消息
- **reject**：消息处理失败并拒绝该消息，RabbitMQ从队列中删除该消息

一般**reject**方式用的较少，除非是消息格式有问题，那就是开发问题了。因此大多数情况下我们需要将消息处理的代码通过`try catch`机制捕获，**消息处理成功时返回ack，处理失败时返回nack.**

> 由于消息回执的处理代码比较统一，因此SpringAMQP帮我们实现了消息确认。并允许我们通过配置文件设置ACK处理方式，有三种模式：
>
> - **`none`**：不处理。即消息投递给消费者后立刻ack，消息会立刻从MQ删除。非常不安全，不建议使用
> - **`manual`**：手动模式。需要自己在业务代码中调用api，发送`ack`或`reject`，存在业务入侵，但更灵活
> - **`auto`**：自动模式。SpringAMQP利用AOP对我们的消息处理逻辑做了环绕增强，当业务正常执行时则自动返回`ack`.  当业务出现异常时，根据异常判断返回不同结果：
>   - 如果是**业务异常**，会自动返回`nack`；
>   - 如果是**消息处理或校验异常**，自动返回`reject`;

通过下面的配置可以修改SpringAMQP的ACK处理方式：

```YAML
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: none # 不做处理
```

修改consumer服务的SpringRabbitListener类中的方法，模拟一个消息处理的异常：

```Java
@RabbitListener(queues = "simple.queue")
public void listenSimpleQueueMessage(String msg) throws InterruptedException {
    log.info("spring 消费者接收到消息：【" + msg + "】");
    if (true) {
        throw new MessageConversionException("故意的");
    }
    log.info("消息处理完成");
}
```

测试可以发现：当消息处理发生异常时，消息依然被RabbitMQ删除了。

我们再次把确认机制修改为auto：

```YAML
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: auto # 自动ack
```

在异常位置打断点，再次发送消息，程序卡在断点时，可以发现此时消息状态为`unacked`（未确定状态）：

![image-20240605154120885](http://img.balance.wiki//blog/image-20240605154120885.png)

放行以后，由于抛出的是**消息转换异常**，因此Spring会自动返回`reject`，所以消息依然会被删除：

![image-20240605154147093](http://img.balance.wiki//blog/image-20240605154147093.png)

我们将异常改为RuntimeException类型：

```Java
@RabbitListener(queues = "simple.queue")
public void listenSimpleQueueMessage(String msg) throws InterruptedException {
    log.info("spring 消费者接收到消息：【" + msg + "】");
    if (true) {
        throw new RuntimeException("故意的");
    }
    log.info("消息处理完成");
}
```

放行以后，由于抛出的是业务异常，所以Spring返回`ack`，最终消息恢复至`Ready`状态，并且没有被RabbitMQ删除

![image-20240605154246530](http://img.balance.wiki//blog/image-20240605154246530.png)

当我们把配置改为`auto`时，消息处理失败后，会回到RabbitMQ，并重新投递到消费者。

#### 失败重试机制

当消费者出现异常后，消息会不断requeue（重入队）到队列，再重新发送给消费者。如果消费者再次执行依然出错，消息会再次requeue到队列，再次投递，直到消息处理成功为止。

**极端情况就是消费者一直无法执行成功，那么消息requeue就会无限循环，导致mq的消息处理飙升，带来不必要的压力：**

为了应对上述情况**Spring又提供了消费者失败重试机制**：在消费者出现异常时利用本地重试，而不是无限制的requeue到mq队列。

修改consumer服务的application.yml文件，添加内容：

```YAML
spring:
  rabbitmq:
    listener:
      simple:
        retry:
          enabled: true # 开启消费者失败重试
          initial-interval: 1000ms # 初识的失败等待时长为1秒
          multiplier: 1 # 失败的等待时长倍数，下次等待时长 = multiplier * last-interval
          max-attempts: 3 # 最大重试次数
          stateless: true # true无状态；false有状态。如果业务中包含事务，这里改为false
```

重启consumer服务，重复之前的测试。可以发现：

- 消费者在失败后消息没有重新回到MQ无限重新投递，而是在**本地重试**了3次
- 本地重试3次以后，抛出了`AmqpRejectAndDontRequeueException`异常。查看RabbitMQ控制台，发现消息被删除了，说明最后SpringAMQP返回的是`reject`

结论：

- 开启本地重试时，消息处理过程中抛出异常，不会requeue到队列，而是在**消费者本地重试**
- 重试达到最大次数后，Spring会返回reject，消息会被丢弃

> 这里的重试是指在本地重试！！！！！

#### 失败处理策略-重试后

在之前的测试中，本地测试达到最大重试次数后，消息会被丢弃。这在某些对于消息可靠性要求较高的业务场景下，显然不太合适了。

因此Spring允许我们自定义重试次数耗尽后的消息处理策略，这个策略是由`MessageRecovery`接口来定义的，它有3个不同实现：

-  `RejectAndDontRequeueRecoverer`：重试耗尽后，直接`reject`，丢弃消息。默认就是这种方式 
-  `ImmediateRequeueMessageRecoverer`：重试耗尽后，返回`nack`，消息重新入队 
-  `RepublishMessageRecoverer`：重试耗尽后，将失败消息投递到指定的交换机 

比较优雅的一种处理方案是`RepublishMessageRecoverer`，失败后将消息投递到一个指定的，专门存放异常消息的队列，后续由人工集中处理。

1）在consumer服务中定义处理失败消息的交换机和队列

```Java
@Bean
public DirectExchange errorMessageExchange(){
    return new DirectExchange("error.direct");
}
@Bean
public Queue errorQueue(){
    return new Queue("error.queue", true);
}
@Bean
public Binding errorBinding(Queue errorQueue, DirectExchange errorMessageExchange){
    return BindingBuilder.bind(errorQueue).to(errorMessageExchange).with("error");
}
```

2）定义一个RepublishMessageRecoverer，关联队列和交换机

```Java
@Bean
public MessageRecoverer republishMessageRecoverer(RabbitTemplate rabbitTemplate){
    return new RepublishMessageRecoverer(rabbitTemplate, "error.direct", "error");
}
```

完整代码如下：

```Java
package com.itheima.consumer.config;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.DirectExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.retry.MessageRecoverer;
import org.springframework.amqp.rabbit.retry.RepublishMessageRecoverer;
import org.springframework.context.annotation.Bean;

@Configuration
@ConditionalOnProperty(name = "spring.rabbitmq.listener.simple.retry.enabled", havingValue = "true")
public class ErrorMessageConfig {
    @Bean
    public DirectExchange errorMessageExchange(){
        return new DirectExchange("error.direct");
    }
    @Bean
    public Queue errorQueue(){
        return new Queue("error.queue", true);
    }
    @Bean
    public Binding errorBinding(Queue errorQueue, DirectExchange errorMessageExchange){
        return BindingBuilder.bind(errorQueue).to(errorMessageExchange).with("error");
    }

    @Bean
    public MessageRecoverer republishMessageRecoverer(RabbitTemplate rabbitTemplate){
        return new RepublishMessageRecoverer(rabbitTemplate, "error.direct", "error");
    }
}
```

#### 业务幂等性-重复处理

何为幂等性？

**幂等**是一个数学概念，用函数表达式来描述是这样的：`f(x) = f(f(x))`，例如求绝对值函数。

在程序开发中，则是指同一个业务，**执行一次或多次对业务状态的影响是一致的**。例如：

- 根据id删除数据
- 查询数据
- 新增数据

但数据的更新往往不是幂等的，如果重复执行可能造成不一样的后果。比如：

- 取消订单，恢复库存的业务。如果多次恢复就会出现库存重复增加的情况
- 退款业务。重复退款对商家而言会有经济损失。

我们在用户支付成功后会发送MQ消息到交易服务，修改订单状态为已支付，就可能出现消息重复投递的情况。如果消费者不做判断，**很有可能导致消息被消费多次，出现业务故障。**

举例：

1. 假如用户刚刚支付完成，并且投递消息到交易服务，交易服务更改订单为**已支付**状态。
2. 由于某种原因，例如网络故障导致生产者没有得到确认，隔了一段时间后**重新投递**给交易服务。
3. 但是，在新投递的消息被消费之前，用户选择了退款，将订单状态改为了**已退款**状态。
4. 退款完成后，新投递的消息才被消费，那么订单状态会被再次改为**已支付**。业务异常。

> 这里貌似有问题，应该按序处理消息来解决，而不是重复消费消息导致的问题。

**因此，我们必须想办法保证消息处理的幂等性。这里给出两种方案：**

- **唯一消息ID**
- **业务状态判断**

##### 唯一消息ID

这个思路非常简单：

1. 每一条消息都生成一个唯一的id，与消息一起投递给消费者。
2. 消费者接收到消息后处理自己的业务，业务处理成功后**将消息ID保存到数据库**
3. 如果下次又收到相同消息，去数据库查询判断是否存在，存在则为重复消息放弃处理。

我们该如何给消息添加唯一ID呢？

其实很简单，SpringAMQP的`MessageConverter`自带了`MessageID`的功能，我们只要开启这个功能即可。

以Jackson的消息转换器为例：

```java
@Bean
public MessageConverter messageConverter(){
    // 1.定义消息转换器
    Jackson2JsonMessageConverter jjmc = new Jackson2JsonMessageConverter();
    // 2.配置自动创建消息id，用于识别不同消息，也可以在业务中基于ID判断是否是重复消息
    jjmc.setCreateMessageIds(true);
    return jjmc;
}
```

##### 业务判断

业务判断就是基于业务本身的逻辑或状态来判断是否是重复的请求或消息，不同的业务场景判断的思路也不一样。

例如我们当前案例中，处理消息的业务逻辑是把订单状态从未支付修改为已支付。因此我们就可以在执行业务时判断订单状态是否是未支付，如果不是则证明订单已经被处理过，无需重复处理。

相比较而言，消息ID的方案需要改造原有的数据库，所以我更推荐使用业务判断的方案。

以支付修改订单的业务为例，我们需要修改`OrderServiceImpl`中的`markOrderPaySuccess`方法：

```Java
    @Override
    public void markOrderPaySuccess(Long orderId) {
        // 1.查询订单
        Order old = getById(orderId);
        // 2.判断订单状态
        if (old == null || old.getStatus() != 1) {
            // 订单不存在或者订单状态不是1，放弃处理
            return;
        }
        // 3.尝试更新订单
        Order order = new Order();
        order.setId(orderId);
        order.setStatus(2);
        order.setPayTime(LocalDateTime.now());
        updateById(order);
    }
//具体来说，当一个线程在检查订单状态后（if (old == null || old.getStatus() != 1)），并且在更新订单状态前（updateById(order);），
//另一个线程可能已经更改了订单状态。这将导致第一个线程基于过时的信息进行操作，可能会导致数据不一致。
```

上述代码逻辑上符合了幂等判断的需求，但是由于判断和更新是两步动作，因此在极小概率下可能存在线程安全问题。

我们可以合并上述操作为这样：

```Java
@Override
public void markOrderPaySuccess(Long orderId) {
    // UPDATE `order` SET status = ? , pay_time = ? WHERE id = ? AND status = 1
    lambdaUpdate()
            .set(Order::getStatus, 2)
            .set(Order::getPayTime, LocalDateTime.now())
            .eq(Order::getId, orderId)
            .eq(Order::getStatus, 1)
            .update();
}
```

注意看，上述代码等同于这样的SQL语句：

```SQL
UPDATE `order` SET status = ? , pay_time = ? WHERE id = ? AND status = 1
```

我们在where条件中除了判断id以外，还加上了status必须为1的条件。如果条件不符（说明订单已支付），则SQL匹配不到数据，根本不会执行。

#### 兜底方案

虽然我们利用各种机制尽可能增加了消息的可靠性，但也不好说能保证消息100%的可靠。万一真的MQ通知失败该怎么办呢？

有没有其它兜底方案，能够确保订单的支付状态一致呢？

其实思想很简单：既然MQ通知不一定发送到交易服务，那么交易服务就必须自己**主动去查询**支付状态。这样即便支付服务的MQ通知失败，我们依然能通过主动查询来保证订单状态的一致。

> 双向查询！

流程如下：

![image-20240605164521374](http://img.balance.wiki//blog/image-20240605164521374.png)

图中黄色线圈起来的部分就是MQ通知失败后的兜底处理方案，由交易服务自己主动去查询支付状态。

不过需要注意的是，交易服务并不知道用户会在什么时候支付，如果查询的时机不正确（比如查询的时候用户正在支付中），可能查询到的支付状态也不正确。

那么**问题来了，我们到底该在什么时间主动查询支付状态呢？**

这个时间是无法确定的，因此，通常我们采取的措施就是利用**定时任务**定期查询，例如每隔20秒就查询一次，并判断支付状态。如果发现订单已经支付，则立刻更新订单状态为已支付即可。

> 至此，消息可靠性的问题已经解决了。
>
> 综上，支付服务与交易服务之间的订单状态一致性是如何保证的？
>
> - 首先，支付服务会正在用户支付成功以后利用MQ消息通知交易服务，完成订单状态同步。
> - 其次，为了保证MQ消息的可靠性，我们采用了生产者确认机制、消费者确认、消费者失败重试等策略，确保消息投递的可靠性
> - 最后，我们还在交易服务设置了定时任务，定期查询订单支付状态。这样即便MQ通知失败，还可以利用定时任务作为兜底方案，确保订单支付状态的最终一致性。

### 延迟消息



#### 死信交换机



#### 延迟消息插件



#### 实战-延迟消息

