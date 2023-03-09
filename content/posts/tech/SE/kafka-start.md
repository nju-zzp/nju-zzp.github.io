---
title: "Kafka Start" #标题
date: 2023-03-07T23:33:20+08:00 #创建时间
lastmod: 2023-03-07T23:33:20+08:00 #更新时间
author: ["River"] #作者
keywords: #关键词
-
categories: #类别
- 
tags: #标签
  - "kafka"

description: "" #描述
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "/images/kafka.png" #图片路径：posts/tech/文章1/picture.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
    
reward: true # 打赏
---

# kafka 入门指南

## 1. kafka 基本概念与基本架构

## 2. kafka 单机环境搭建 与 启动
<!--more-->
参考：

1. https://blog.csdn.net/CSDN877425287/article/details/108836682
2. https://www.cnblogs.com/qpf1/p/9161742.html

扩展（kafka生产环境部署指南）:

[kafka生产环境部署指南](https://xie.infoq.cn/article/d57f83779490c7ff62b6b59ae)

### 2.1 下载Kafka安装包上传服务器

[kafka官网](https://kafka.apache.org/downloads)

![image-20221116231239045](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20221116231239045.png)

scp 指令上传服务器

`tar xzvf kafka_2.12-2.1.0.tgz -C /home/ubuntu/environments`

### 2.2 配置zookeeper配置

无需单独下载，已经集成

`vim ./kafka_2.12-2.1.0/config/zookeeper.properties`

如果kafka和zk是单节点运行的话使用kafka的默认配置即可

```
#集群配置-单节点无需配置
server.1=192.168.0.187:2888:3888
server.2=192.168.0.188:2888:3888
server.3=192.168.0.189:2888:3888
```

![image-20221116232713399](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20221116232713399.png)

**`dataDir` zk数据存放目录**

`dataLogDir` zk日志存放目录

**`clientPort` 客户端连接zk服务的端口**

`tickTime` zk服务器之间或者客户端与服务器之间维持心跳的时间间隔

`initLimit` 允许follower（相对于Leaderer言的，客户端）连接并同步到Leader的初始化连接时间，以tickTime为单位，当初始化连接时间超过该值，则表示连接失败。

`syncLimit` Leader于Follower之间发送消息时，请求和答应时间长度，如果follow在设置时间内不能与leader通信，那么此follower将会被丢弃

`server.1=192.168.0.187:2888:3888` 2888是follower于leader减缓信息的端口，3888当leader交换信息的端口，3888是当leader挂了时用来执行选举是服务器互相通信的端口

### 2.3 配置kafka配置（server）

**如果kafka和zk是单节点运行的话使用kafka的默认配置即可**

关键：

**log.dirs和zookeeper.connect。前者是日志存放文件夹，后者是zookeeper连接地址（端口和clientPort保持一致）。**

- **listeners**
- **advertised.listeners**（对外宣扬/暴露的地址和端口，比如对生产者、消费者暴露）

在公司内网部署 kafka 集群只需要用到 listeners，内外网需要作区分时 才需要用到advertised.listeners。

advertised_listeners 监听器会注册在 zookeeper 中；

当我们对 172.17.0.10:9092 请求建立连接，kafka 服务器会通过 zookeeper 中注册的监听器，找到 INSIDE 监听器，然后通过 listeners 中找到对应的 通讯 ip 和 端口；

同理，当我们对 <公网 ip>:端口 请求建立连接，kafka 服务器会通过 zookeeper 中注册的监听器，找到 OUTSIDE 监听器，然后通过 listeners 中找到对应的 通讯 ip 和 端口 172.17.0.10:9094；

总结：advertised_listeners 是对外暴露的服务端口，真正建立连接用的是 listeners。

- 只有内网，比如在公司搭建的 kafka 集群，只有内网中的服务可以用，这种情况下，只需要用 listeners 就行。
- 内外网，比如在 docker 中或者 在类似阿里云主机上部署 kafka 集群，这种情况下是 需要用到 advertised_listeners

```
broker.id=0									每个server需要单独配置broker.id如果不配置系统会指定配置，需要和上一步ID一致
listeners=PLAINTEST://ip:9092				监听地址，格式PLAINTEST：//ip：端口
num.network.threads=3						接收和发送网络信息的线程数
num.io.threads=8							服务器用于处理请求的线程数，其中可能包括磁盘IO
socket.send.buffer.bytes=102400				套接字服务器使用的发送缓冲区（SO_SNDBUF）
socket.receive.buffer.bytes=102400			套接字服务器使用的接收缓冲区（SO_RCVBUF）
socket.request.max.bytes=104857600			套接字服务器将接收的请求最大大小（防止OOM）
log.dirs=/tmp/kafka-logs					日志文件
num.partitions=1							partitions的数量
num.recovery.threads.per.data.dir=1			启动时恢复日志，关闭时刷盘日志每个数据目录的线程的数量，默认1
offsets.topic.replication.factor=1			偏移量话题的复制因子（设置更高保证可用），为保证有效的父子，偏移量的复制因子是可配置的，在偏移话题的第一次请求的时候可用borker的数量至少为复制因子的大小，否则要么话题创建失败，要么复制因子取可用的数量和配置复制因子取最小值
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

log.retention.hours=168						日志文件删除之前保留的时间
log.segment.bytes=1073741824				单个日志文件大小默认为1073741824
log.retention.check.interval.ms=300000		检查日志端以查看是否可以根据保留策略删除他们的时间鳄梨
zookeeper.connect=zkip:2181					zk主机地址，如果是zk集群以逗号分隔
zookeeper.connection.timeout.ms=6000		连接zk的超时时间
group.initial.rebalance.delay.ms=0
```

### 2.4 后台启动zookeeper并检验是否运行成功

```shell
cd kafka_2.12-2.1.0/
nohup bin/zookeeper-server-start.sh config/zookeeper.properties &
nohup bin/zookeeper-server-start.sh config/zookeeper.properties > zookeeper-run.log 2>&1 &
```

```sh
echo conf | nc localhost 2181
echo stat | nc localhost 2181
```

![image-20221116234427027](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20221116234427027.png)

### 2.5 后台启动kafka

```sh
nohup bin/kafka-server-start.sh config/server.properties > kafka-run.log 2>&1 &
```

### 2.6 创建并查看topic

```shell
ubuntu$> ./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
Created topic "test".

ubuntu$> ./bin/kafka-topics.sh --zookeeper localhost:2181 --list
test

```

## 3. JAVA实现 Kafka Producer

### 3.1 方式一：kafka-clients

API参考：https://kafka.apache.org/33/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html

生产者详解、进阶参考：https://www.jianshu.com/p/37ea14d1d4ab（主要包含生产者参数配置、消息序列化、自定义分区）

#### 引入kafka-clients依赖

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.1.0</version>
</dependency>
```

#### 创建生产者 KafkaProducer

`KafkaProducer` API: https://kafka.apache.org/33/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html

向 Kafka 写入消息，首先要创建一个生产者对象，该对象有3个必要属性。

- `bootstrap.servers`
   指定 broker 的地址列表，地址的格式为 `host:port`。
   不需要包含所有的 broker 地址，生产者会通过给定的 broker 查找到其他 broker 的信息。建议至少提供两个 broker 的信息，防止其中一个宕机。
- `key.serializer`
   Kafka 客户端默认提供了 `ByteArraySerializer`、 `StringSerializer` 和 `IntegerSerializer`，因此如果使用常见的 Java 对象类型，不需要实现自己的序列化器 。如果要自定义序列化器，需要实现 `org.apache.kafka.common.serialization.Serializer` 接口。
   需要注意，key.serializer 是必须设置的，即使只发送值类型。
- `value.serializer`
   与 `key.serializer` 一样， `value.serializer` 指定的类会将值序列化发送给 broker。

```java
        Properties kafkaProps = new Properties();
        kafkaProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "kafka-broker-ip:9092");
        kafkaProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        kafkaProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        KafkaProducer<String, String> producer = new KafkaProducer<String, String>(kafkaProps);
```

#### 发送消息

实例化生产者对象后，就可以开始送消息了。发送消息主要有以下 3 种方式：

- 发送并忘记（fire-and-forget）
   把消息发送给服务器，不关心是否正常到达。大多数情况下，消息会正常到达，因为  Kafka 是高可用的，而且生产者会自动尝试重发。不过，使用这种方式有时候也会丢失一些消息。
- 同步发送（async）
   使用 `send()` 方法发送消息，会返回一个 Future 对象，调用 get() 方法阻塞等待返回，返回结果知道是否发送成功。
- **异步发送（sync）**（最简单最常用）
   使用 `send()` 方法并指定一个回调函数，服务器在返回响应时调用该函数。

核心对象：

- 消息对象 `ProducerRecord` : https://kafka.apache.org/33/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html
- 消息记录的存储信息 `RecordMetadata`： https://kafka.apache.org/33/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html（`send(...).get()`返回对象）

例子使用了ScheduledExecutorServive（即ScheduledThreadPool）来进行定时任务

```java
        ScheduledExecutorService scheduledExecutor = Executors.newScheduledThreadPool(5);
        scheduledExecutor.scheduleAtFixedRate(() -> {
            try {
                RecordMetadata recordMetadata = producer.send(new ProducerRecord<String, String>("test", "time", new Date().toString())).get();
                System.out.println("Success: OFFSET-"+recordMetadata.offset());
            } catch (InterruptedException e) {
                System.out.println("ERROR");
                throw new RuntimeException(e);
            } catch (ExecutionException e) {
                System.out.println("ERROR");
                throw new RuntimeException(e);
            }
        }, 0, 2, TimeUnit.SECONDS);
```

## 4. JAVA实现 Kafka Consumer

### 4.1 方式一：kaf登录，文件提交后自动重命名，支持收集任意格式文件。
搭配坚果云ka-clients

API 参考：https://kafka.apache.org/33/javadoc/org/apache/kafka/clients/consumer/package-frame.html

消费者详解、进阶参考：https://www.jianshu.com/p/1f9e18e926f6（主要包括消费者参数配置

#### 引入kafka-clients依赖

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.1.0</version>
</dependency>
```

#### 创建消费者 KafkaConsumer

- KafkaConsumer对象API：https://kafka.apache.org/33/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html
- ConsumerConfig API：https://kafka.apache.org/33/javadoc/org/apache/kafka/clients/consumer/ConsumerConfig.html

**auto.offset.reset**：当不存在初始偏移量或服务器上不再存在当前偏移量时，此属性是必需的。只有以下三个值可用于重置偏移值：

What to do when there is no initial offset in Kafka or if the current offset does not exist any more on the server (e.g. because that data has been deleted):

<ul><li>earliest: automatically reset the offset to the earliest offset<li>latest: automatically reset the offset to the latest offset</li><li>none: throw exception to the consumer if no previous offset is found for the consumer\'s group</li><li>anything else: throw exception to the consumer.</li></ul>

指定了消费者在读取一个没有偏移量（offset）的分区或者偏移量无效的情况下（因消费者长时间失效，包含偏移量的记录已经过时井被删除）该作何处理，**默认值是 `latest`，表示在 offset 无效的情况下，消费者将从最新的记录开始读取数据（在消费者启动之后生成的记录）**。

```java
        Properties props = new Properties();

        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "124.222.233.153:9092");
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "191870267-0");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
		props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, "10");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);
```

#### 订阅主题Topic

```java
consumer.subscribe(Collections.singleton("test"));
```

#### 消费轮询（核心）

消息轮询是消费者的核心，通过轮询向服务器请求数据。消息轮询 API 会处理所有的细节，包括群组协调、分区再均衡、发送心跳和获取数据，开发者只需要处理从分区返回的数据。

```java
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(5));
            for(ConsumerRecord<String, String> record : records) {
                System.out.println("{offset=" + record.offset() + " , key=" + record.key() + " , value=" + record.value() + " , timestamp=" + record.timestamp() + " }");
            }
            System.out.println();
        }
```



