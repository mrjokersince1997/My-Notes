# 消息系统之 Kafka

---

## 什么是消息系统

消息系统是专用的中间件，负责将数据从一个应用传递到另外一个应用。使应用只需关注于数据，无需关注数据在两个或多个应用间是如何传递的。

消息系统一般基于可靠的消息队列来实现，使用点对点模式或发布订阅模式。数据实时在消息系统中传递，被看作流。

---

## 为什么使用消息系统

使用消息系统具有以下优势：

1. **解耦**：发送方和接收方统一使用消息系统提供的接口进行通信，易修改易扩展。
2. **持久化**：传递过程中消息存储到本地磁盘，防止处理数据失败导致数据丢失。
3. **均衡负载**：分布式系统能根据负载灵活调整机器数量，能够处理高吞吐量和流量突增的情况。

除此之外，消息系统还可以保障：

4. **保障有序**：数据处理的顺序不被打乱。
5. **传递加速**：通过缓冲层控制和优化数据流经过系统的速度。
6. **延时处理**：提供了异步处理机制，允许用户把消息放入队列，但并不立即处理它。

---

## 什么是 Kafka 

Kafka 作为当前最常用的消息系统之一，一般用于日志收集的离线系统。采用发布订阅模式，由通过高性能 TCP 网络协议进行通信的服务器和客户端组成。

Kafka 使用 scala 开发，由 LinkedIn 开源，目前已捐献给 Apache 基金会。

> Kafka 官网 http://Kafka.apache.org/

---

## Kafka 的优劣势

**优势**

1. 快速持久化，可以在O(1)的系统开销下进行消息持久化；
2. IO 吞吐量高，使用 partition 把队列流量均匀分散在多台机器上，单台服务器可以达到 10W/s 的吞吐速率。

**劣势**

1. 不进行消息重复性检查，可能导致消费重复数据或者异常情况下的数据丢失。
2. 实时性方面也存在少量延迟。

---

## 生产者/消费者模式

Kafka 是一个分布式系统，由服务器和客户端组成，之间通过高性能 TCP 网络协议进行通信。

1. 服务器以 `Cluster` 为单位向外提供服务，由多个 `Broker` 组成。Broker 作为 Kafka 的服务节点，接收外部生产的数据，在本地磁盘对数据进行备份，并提供数据给指定的接收者。

2. 客户端分为以下两种类型：

    - `Producer`: 数据生产者，向 Kafka 集群生产数据。
    - `Consumer`：数据消费者，读取 Kafka 集群生产者生产的消息。

3. 组件之间通过 `Zookeeper` 进行协调。ZooKeeper 会保存 Broker 和 Consumer 的元数据信息，并进行数据变更的监控。并负责选举出 Cluster 内的 Controller （其中一个 Broker），管理 Zookeeper 上的元数据信息。

---

## 数据分片模型

Kafka 消息按照 `Topic` 进行数据的组织和隔离，Producer/Consumer 会向指定的 Topic 收发数据。

在服务器端，Topic 则按 `Patition` 进行分区，同一个 Topic 的 Partition 会散落在多个 Broker 上，存储为一个阻塞队列，从而达到了数据分布式存储的目的。Producer 可以指定发送的 Partition 以保证消息有序到达。

![Kafka](Kafka.jpeg)

每个 `Consumer Group` 都会消费一个 Topic 全量的数据，彼此之间互不干扰。同一个 Consumer Group 下的 Consumer 只能消费到其中一部分 Partition ，通过多个 Consumer 可以达到并行消费的目的。Partition 数量推荐设为 Consumer 数量的整数倍，便于均分。

![group](group.jpeg)

---

## 多副本模型


为了提高可用性，避免 Broker 损坏导致的 Partition 不可用或者丢失问题，Kafka 会对每个 Partition 提供多个副本（默认为 3 个），其中有且仅有一个作为 `Leader`，负责数据的读写。其他副本 `Follower` 将存放在不同的 Broker 上，通过接收 Leader 广播将数据同步到本地。

每个 Leader Partition 维护一个独立的 `ISR` 列表，记录当前同步的 Follower 集合：

1. 如果 Follower 不能及时同步（延迟时间高或延迟条数超过阈值）就会被暂时踢出 ISR 。
2. 如果 Leader 不可用将从 ISR 中选出一个 Follower 担任 Leader 。


---


## 消息定位

### 定位方式

kafka 用 `Offset` 表示 Message 在 Partition 中的偏移量，通过 Offset 可以唯一确定 Partition 中的一条 Message 。

1. **生产者 Offset (current position)**

每个 Partition 只有一个，表示当前消息生产到的位置。


2. **消费者 Offset (committed offset)** 

每个 Partition 可以有多个，取决于消费的 ConsumeGroup 数量。消费者 Offset 会记录到 Kafka 自带 Topic(__consumer_offsets) 内，表示当前消费到的位置。

参数|含义
-|-
Group | 消费者组
Topic | topic 名称
Pid | partition ID
Offset | 消费者在对应分区上已消费消息数
logSize | 已经写到该分区的消息数
Lag | 还有多少消息未读取（Lag = logSize - Offset）
Owner | 分区所属 broker



---

## 搭建 Broker 

在服务器搭建 Broker ，需要通过指令来完成。本文所有的操作都是在MacOS系统上使用。如果是在Linux操作系统下进行实验，使用的命令是相同的；如果是在windows操作系统下进行实验，则需要使用对应的bin/windows目录下的bat文件。


```sh
# 最大offset
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic test_topic --time -1

# 最小offset
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic test_topic --time -2

# offset
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic test_topic
```


```sh
# 列出当前 kafka 所有的 topic
bin/kafka-topics.sh --zookeeper localhost:2181 --list

# 创建 topic
bin/kafka-topics.sh --create --zookeeper localhost:2181 --topic test_topic --replication-factor 1 --partitions 1 

bin/kafka-topics.sh --create --zookeeper localhost:2181 --topic test_topic --replication-factor 3 --partitions 10 --config cleanup.policy=compact

bin/kafka-topics.sh --create --zookeeper localhost:2181  --topic test_topic --partitions 1   --replication-factor 1 --config max.message.bytes=64000 --config flush.messages=1

# 查看某 topic 具体情况
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic test_topic

# 修改 topic （分区数、特殊配置如compact属性、数据保留时间等）
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --partitions 3  --config cleanup.policy=compact --topic test_topic

# 修改 topic （也可以用这种）
bin/kafka-configs.sh --alter --zookeeper localhost:2181 --entity-name test_topic --entity-type topics --add-config cleanup.policy=compact
 
bin/kafka-configs.sh --alter --zookeeper localhost:2181 --entity-name test_topic --entity-type topics --delete-config cleanup.policy
```

---

## JAVA 生产信息

### 导入依赖

```xml
<!-- 导入 0.10.2 版本 Kafka -->
<dependency>
    <groupId>org.apache.Kafka</groupId>
    <artifactId>Kafka-clients</artifactId>
    <version>0.10.2.0</version>
</dependency>
```

### 配置生产者

在创建 Producer 对象前，必须配置以下属性：

属性 | 含义 | 备注
-|-|-
`bootstrap.servers`|Kafka broker 地址| 如果有多个地址用逗号分割
`key.serializer` |key 的序列化类| 必须实现 Kafka 的 Serializer 接口
`value.serializer`|value 的序列化类|必须实现 Kafka 的 Serializer 接口


开发者还可以选择配置如下属性：

属性 | 含义 | 备注
-|-|-
`request.required.acks`|指定消息系统何时向生产者返回 ACK ： `0` 不需要、 `1` 主服务器收到后、 `-1` 所有服务器收到后。| 选择不接收 ACK 时生产者能以最大速度发送消息，但如果 broker 没有收到消息，生产者将无感知。
`producer.type` |同步发送消息 `sync` 或异步发送消息 `async` 。|异步发送消息会被服务器暂存在一个阻塞队列中，被消费者拉取时再由线程取出并组装。

通过读取配置，即可生成 Producer 对象。

```java
Properties KafkaProps = new Properties();
KafkaProps.put("bootstrap.servers", "broker1:port1, broker2:port2");
KafkaProps.put("key.serializer", "org.apache.Kafka.common.StringSerializer");
KafkaProps.put("value.serializer", "org.apache.Kafka.common.StringSerializer");
producer = new KafkaProducer<String, String>(KafkaProps);
```

### 构造消息

实例化 ProducerRecord 类得到消息对象。

创建时必须指定消息所属 Topic 和消息值 Value 。消息发往哪个 Partition 通常由负载均衡机制随机选择。若指定了 Partition 则发送到指定的 Partition，如果没有指定 Partition 但指定了 Key，则由 hasy(key) 决定。

由于 Kafka 只能保证 Partition 内消息的有序性，如果需要保证消息有序到达，Producer 必须指定消息到达的 Partition ，这些消息最终只能被 ConsumeGroup 内的一个 Consumer 消费。


```java
// 三种构造方法
ProducerRecord<String, String> record = new ProducerRecord<>(topic, value);
ProducerRecord<String, String> record = new ProducerRecord<>(topic, key, value);
ProducerRecord<String, String> record = new ProducerRecord<>(topic, partition, key, value);

// 发送给消息系统
producer.send(record);
```


### 接收 ACK

发送消息后，生产者有两种方式接收消息系统返回的 ACK :

1. 通过返回的 Future 判断已经发送成功，get 方法会阻塞线程。实现同步等待。

```java
try {
    Future future = producer.send(record); 
    future.get(10000);
} catch (TimeoutException e) {
    e.printStackTrace();
}
```


2. 发送消息时传递一个回调对象，实现 Kafka 的 Callback 接口，通过回调判断是否发送成功。实现异步等待。

```java
producer.send(record, new ProducerCallback());

private class ProducerCallback implements Callback {
    @Override
    public void onCompletion(RecordMetadata recordMetadata, Exception e) {
        if (e != null)  e.printStackTrace();
    }
} 
```


### 生产示例

```java
import java.util.Properties;
import org.apache.Kafka.clients.producer.KafkaProducer;
import org.apache.Kafka.clients.producer.ProducerConfig;
import org.apache.Kafka.clients.producer.ProducerRecord;
import org.apache.Kafka.common.serialization.StringSerializer;

public class Producer {
    public static String topic = "test"; 

    public static void main(String[] args) throws InterruptedException {

        Properties p = new Properties();
        p.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.23.76:9092,192.168.23.77:9092");          
        p.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);       
        p.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);    
        p.put("request.required.acks", "-1");                        
        p.put("producer.type", "async");         

        KafkaProducer<String, String> KafkaProducer = new KafkaProducer<>(p);

        try {
            for(int i = 0; i < 100; i++) {
                String msg = "Hello," + i;
                ProducerRecord<String, String> record = new ProducerRecord<String, String>(topic, msg);   
                KafkaProducer.send(record);                                  
                Thread.sleep(500);
            }
        } finally {
            KafkaProducer.close();
        }

    }
}
```

---

## JAVA 消费消息

### 导入依赖

```xml
<!-- 导入 0.10.2 版本 Kafka -->
<dependency>
    <groupId>org.apache.Kafka</groupId>
    <artifactId>Kafka-clients</artifactId>
    <version>0.10.2.0</version>
</dependency>
```


### 配置消费者

在创建 Consumer 对象前，必须配置以下属性：

属性 | 含义 | 备注
-|-|-
`bootstrap.servers`|Kafka broker 地址| 如果有多个地址用逗号分割
`group.id`|所属消费组|
`key.deserializer` |key 的反序列化类| 必须实现 Kafka 的 Serializer 接口
`value.deserializer`|value 的反序列化类|必须实现 Kafka 的 Serializer 接口


开发者还可以选择配置如下属性：

属性 | 含义 | 备注
-|-|-
`fetch.max.bytes` | consumer 端一次拉取数据的最大字节数
`fetch.min.bytes` | consumer 端一次拉取数据的最大字节数，默认为 1B。
`max.poll.records` | consumer 端一次拉取数据的最大条数，默认为 500。
`fetch.max.wait.ms`| 服务器最大等待时间，默认为 500ms。超过时间后返回所有可用数据。

通过读取配置，即可生成 Consumer 对象。

```java
Properties kafkaProps = new Properties();
kafkaProps.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.23.76:9092");                           
kafkaProps.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);                 
kafkaProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);                     
kafkaProps.put(ConsumerConfig.GROUP_ID_CONFIG, "duanjt_test");                                           
KafkaConsumer<String, String> KafkaConsumer = new KafkaConsumer<String, String>(kafkaProps);
```


### 订阅消息

消费者可以通过以下两种方式订阅 Topic：

1. subscribe 方法：动态调整组内各个消费者与分区的关系，实现负载均衡。
2. assign 方法：订阅确定的主题和分区。

```java
// 订阅
consumer.subscribe(Collections.singletonList(Producer.topic));
consumer.assign(Collections.singletonList(new TopicPartition(partitionInfo.topic(), partitionInfo.partition())));
// 解除订阅
consumer.unsubscribe();
```


### 拉取消息

Kafka Consumer 采用主动拉取消息系统数据 poll 的方式进行消费，可以对服务器的数据进行延迟处理。以防止消息系统向 Consumer 推送数据过多，导致 Consumer 积压而不堪重负的情况。为避免在服务器无数据的时候一直轮询， Kafka 在 poll 方法有参数允许消费者请求在长轮询中阻塞，等待数据到达。

获取到消息组 ConsumerRecords 后，内部包含多个 ConsumerRecord 对象，记录消息的 topic/partition/offset/key/value 信息。

```java
// 每隔 1s 拉取一次数据
ConsumerRecords<String, String> records = KafkaConsumer.poll(1000);
// 打印数据
records.foreach(record -> {
    System.out.println(String.format("topic:%s,offset:%d,消息:%s", record.topic(), record.offset(), record.value()));
});
```

### 提交 Offset

对于消费者而言，异步模式下 committed offset 是落后于 current position 的。如果 consumer 挂掉，那么下一次消费数据又只会从 committed offset 的位置拉取数据，就会导致数据被重复消费。

消费者 offset 更新有以下两种方式：

1. **自动提交 at-most-once**

设置 enable.auto.commit=true（默认），更新的频率根据参数 auto.commit.interval.ms 来定，定时系统会根据当时 Consumer 收到的消息数量自动更新 offset 。

这可能导致两个问题：

   1. Consumer 程序崩溃，而 Offset 尚未更新。会重复消费部分数据。
   2. Consumer 程序崩溃，但 Offset 已被更新。已收到但未消费的数据永久丢失。

2. **手动提交 at-least-once**

设置 enable.auto.commit=false，Consumer 收到消息并消费后，再调用方法 consumer.commitSync() 手动更新 offset 。

如果消费失败，则 offset 也不会更新，此条消息会被重复消费。


### 消费示例

```java
import java.util.Collections;
import java.util.Properties;
import org.apache.Kafka.clients.consumer.ConsumerConfig;
import org.apache.Kafka.clients.consumer.ConsumerRecord;
import org.apache.Kafka.clients.consumer.ConsumerRecords;
import org.apache.Kafka.clients.consumer.KafkaConsumer;
import org.apache.Kafka.common.serialization.StringDeserializer;

public class Consumer {

    public static void main(String[] args) {
        Properties p = new Properties();
        p.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.23.76:9092");                           
        p.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);                 
        p.put(ConsumerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringDeserializer.class);                     
        p.put(ConsumerConfig.GROUP_ID_CONFIG, "duanjt_test");                                           

        KafkaConsumer<String, String> KafkaConsumer = new KafkaConsumer<String, String>(p);
        KafkaConsumer.subscribe(Collections.singletonList("test"));

        while (true) {
            ConsumerRecords<String, String> records = KafkaConsumer.poll(100);
            records.foreach(record -> {
                System.out.println(String.format("topic:%s,offset:%d,消息:%s", record.topic(), record.offset(), record.value()));
            });
        }
    }
}
```


---



美团技术博客：https://blog.csdn.net/lizhitao/article/details/39499283

常用指令一：https://www.cnblogs.com/itwild/p/12287850.html

常用指令二：https://blog.csdn.net/camel84/article/details/81879118