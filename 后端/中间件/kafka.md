### 安装

- 部署一台zookeeper服务器

- 安装jdk

- 下载kafka安装包: https://kafka.apache.org/downloads

- 上传到kafka服务器上: /usr/local/kafka

- 解压缩压缩包

- 进入到config目录内, 修改server.properties文件

  ```shell
  # broker.id属性在kafka集群中必须唯一
  broker.id=0
  # kafka部署的机器ip和提供服务的端口号
  listeners=PLAINTEXT://192.168.80.128:9092
  # kafka的消息存储文件
  log.dirs=/usr/local/kafka/data/kafka-logs
  # kafka连接zookeeper的地址
  zookeeper.connect=localhost:2181
  ```

- 进入bin目录内执行以下命令启动kafka服务器(带配置文件)

  先进入zookeeper的bin目录启动zookeeper

  ```shell
  ./zkServer.sh start
  ```

  ```shell
  ./kafka-server-start.sh -daemon ../config/server.properties
  ```

- 校验kafka是否启动成功

  进入zookeeper查看是否有kafka路径: /brokers/ids/0

### 创建topic

执行以下命令创建名为"test"的topic, 这个topic只有一个partition, 并且备份因子也设置为1

```shell
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

### 查看kafka内有哪些topic

```shell
./kafka-topics.sh --list --zookeeper localhost:2181
```

### 发送消息

```shell
./kafka-console-producer.sh --broker-list 192.168.80.128:9092 --topic test
```

### 消费消息

- 从最后一条消息的偏移量+1开始消费

```shell
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092 --topic test
```

- 从头开始消费

```shell
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092 --from-beginning --topic test
```

### 单播消息

如果多个消费者在同一消费组, 那只有一个消费者能收到订阅的topic中的消息. 即同一个消费组只能有一个消费者收到topic中的消息

```shell
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092 --consumer-property group.id=testGroup --topic test
```

### 多播消息

不同的消费组订阅同一个topic

```shell
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092 --consumer-property group.id=testGroup1 --topic test
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092 --consumer-property group.id=testGroup2 --topic test
```

### 查看消费组及信息

```shell
# 查看有哪些消费组
./kafka-consumer-groups.sh --bootstrap-server 192.168.80.128:9092 --list
# 查看消费组中的具体信息: 比如当前偏移量 最后一条消息的偏移量 堆积的消息数量
./kafka-consumer-groups.sh --bootstrap-server 192.168.80.128:9092 --describe --group testGroup
```

![9b0ece361c232e530f4ab766494b135b](C:\Users\Administrator\Documents\Tencent Files\763820835\nt_qq\nt_data\Pic\2025-01\Ori\9b0ece361c232e530f4ab766494b135b.png)

- current-offset: 最后被消费消息的偏移量 
- Log-end-offset: 消息总量(最后一条消息的偏移量)
- Lag: 积压了多少条消息

### 主题和分区的概念

####  Topic

对消息进行分类, 如果一个Topic中消息非常多, 导致文件非常大, 就有了Partition分区的概念

#### Partition

通过Partition将一个Topic中的消息分区存储: 分布式存储和并行写

### 消息日志文件

- 00000.log: 保存消息
- consumer_offsets: kafka内部自己创建的consumer_offsets主题包含50个分区, 这个主题用来存放消费者消费某个主题的偏移量
- 文件保存的消息默认7天后删除

### 搭建kafka集群

- 准备3个properties文件

```shell
broker.id=0
listeners=PLAINTEXT://192.168.80.128:9092
log.dirs=/usr/local/kafka/data/kafka-logs
```

```shell
broker.id=1
listeners=PLAINTEXT://192.168.80.128:9093
log.dirs=/usr/local/kafka/data/kafka-logs-1
```

```shell
broker.id=2
listeners=PLAINTEXT://192.168.80.128:9094
log.dirs=/usr/local/kafka/data/kafka-logs-2
```

- 启动三台brokers

```shell
./kafka-server-start.sh -daemon ../config/server.properties
./kafka-server-start.sh -daemon ../config/server1.properties
./kafka-server-start.sh -daemon ../config/server2.properties
```

- 查看是否启动成功

进入zookeeper的bin目录执行如下命令

```shell
./zkCli.sh
ls /brokers/ids
```

### 副本

- 副本是对分区的备份, 下面例子: 创建一个主题, 两个分区, 三个副本

```shell
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 2 --topic my-replicated-topic
```

- 查看主题

```shell
./kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
```

![333e5935eda67b6af56aa8b6d6190771](C:\Users\Administrator\Documents\Tencent Files\763820835\nt_qq\nt_data\Pic\2025-01\Ori\333e5935eda67b6af56aa8b6d6190771.png)

- isr: 可以同步和已同步的broker节点存放在isr集合中, 如果isr中的节点性能较差, 会被踢出isr集合

- 一个分区的多个副本在同一tipic的多个broker上, 会有一个作为leader, 其他是follower

- leader: 负责kafka的读写操作, 把数据同步给follower. 当leader挂了, 从从节点中选leader
- follower: 接收leader同步的数据

### 集群消息的发送

坑: 用localhost报错, 要用ip地址

```shell
./kafka-console-producer.sh --broker-list 192.168.80.128:9092, 192.168.80.128:9093, 192.168.80.128:9094 --topic my-replicated-topic
```

### 集群消息的消费

```shell
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092, 192.168.80.128:9093, 192.168.80.128:9094 --from-beginning --topic my-replicated-topic
```

- 指定消费组消费集群消息

```shell
./kafka-console-consumer.sh --bootstrap-server 192.168.80.128:9092, 192.168.80.128:9093, 192.168.80.128:9094 --from-beginning --consumer-property group.id=testGroup1 --topic my-replicated-topic
```

- 一个Partition只能被一个消费组的一个消费者消费, 目的是为了保证消费的顺序性, 但是多个Partition的多个消费者消费的顺序得不到保证

### java客户端实现生产者

##### 依赖

尽量和kafka版本保持一致

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.4.1</version>
</dependency>
```

##### 生产者发送消息的基本实现

- yml配置

```
spring:
  application:
    name: kafka_producer
  kafka:
    url: 192.168.80.128:9092, 192.168.80.128:9093, 192.168.80.128:9094
    topic-name: my-replicated-topic
```

- 同步发送: 未收到ACK会阻塞

```java
@Value("${spring.kafka.url}")
private String url;

@Value("${spring.kafka.topic-name}")
private String topicName;

@Test
public void producer() throws ExecutionException, InterruptedException {
    Properties props = new Properties();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, url);

    // 把发送的key从字符串序列化为字节数组
    props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    // 把发送消息value从字符串序列化为字节数组
    props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

    Producer<String, String> producer = new KafkaProducer<>(props);

    for (int i = 1; i <= 5; i++) {
        Order order = new Order((long) i, i);
        // key用于计算分区: hash(key) % partitionNum, value是具体的消息内容
        // 同步发送: 如果未收到ACK会阻塞
        ProducerRecord<String, String> producerRecord = new ProducerRecord<>(topicName,
                order.getOrderId().toString(), JSON.toJSONString(order));
        RecordMetadata metadata = producer.send(producerRecord).get();
        log.info("topic:{} partition:{} offset:{}", metadata.topic(), metadata.partition(), 									metadata.offset());
    }
}
```

- 异步发送: 无需等待ACK

```java
@Value("${spring.kafka.url}")
private String url;

@Value("${spring.kafka.topic-name}")
private String topicName;

@Test
public void producer2() throws Exception {
    Properties props = new Properties();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, url);
    // 把发送的key从字符串序列化为字节数组
    props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    // 把发送消息value从字符串序列化为字节数组
    props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    Producer<String, String> producer = new KafkaProducer<>(props);
    CountDownLatch latch = new CountDownLatch(5);
    for (int i = 1; i <= 5; i++) {
        Order order = new Order((long) i, i);
        // key用于计算分区: hash(key) % partitionNum, value是具体的消息内容
        ProducerRecord<String, String> producerRecord = new ProducerRecord<>(topicName,
                order.getOrderId().toString(), JSON.toJSONString(order));
        producer.send(producerRecord, (medata, e) -> {
            if (e != null) {
                log.error("消息发送失败");
            }
            if (medata != null) {
                log.info("异步发送消息结果:topic-{} partition-{} offset-{}",
                        medata.topic(), medata.partition(), medata.offset());
            }
            latch.countDown();
        });
    }
    latch.await(5, TimeUnit.SECONDS);
    producer.close();
}
```

- 属性设置

```java
Properties props = new Properties();
props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, url);
// 把发送的key从字符串序列化为字节数组
props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
// 把发送消息value从字符串序列化为字节数组
props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
// 重试次数
props.put(ProducerConfig.RETRIES_CONFIG, 3);
// 重试间隔
props.put(ProducerConfig.RETRY_BACKOFF_MS_CONFIG, 300);
// 设置消息发送的本地缓冲区，单位b
props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 32 * 1024 * 1024);
// 一次从本地缓冲区取消息的大小
props.put(ProducerConfig.BATCH_SIZE_CONFIG, 16 * 1024);
// x ms内没取满也发出去
props.put(ProducerConfig.LINGER_MS_CONFIG, 10);
// 设置ack属性值
props.put(ProducerConfig.ACKS_CONFIG, "1");
```

#### 生产者的ACK参数配置

- ack=0: 不需要任何broker收到消息, 就返回ACK. 最容易丢消息, 但效率最高.
- ack=1: 多副本之间的leader已经收到消息, 并写入log中. 性能和安全性最均衡.
- ack=-1/all: 里面有默认配置min.insync.replicas=1(默认为1, 推荐>=2, 表示等集群把消息写入x台broker再返回ACK). 最安全, 性能最差.

#### 其它细节

- 发送会默认重试三次, 间隔100ms
- 发送的消息会先进入到本地缓冲区 (32MB), kafka会跑一个线程, 该线程去缓冲区中取16k的数据, 发送到kafka, 如果10ms数据没取满16k也会发送一次

#### 消费者的实现细节

##### 消费者的基本实现

```java
private final static String CONSUMER_GROUP_NAME = "testGroup";

@Value("${spring.kafka.url}")
String url;

@Value("${spring.kafka.topic-name}")
String topicName;

@Test
void basic() {
    Properties props = new Properties();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, url);
    // 消费分组名
    props.put(ConsumerConfig.GROUP_ID_CONFIG, CONSUMER_GROUP_NAME);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    // 创建一个消费者的客户端
    KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
    // 消费者订阅主题列表
    consumer.subscribe(Arrays.asList(topicName));
    while (true) {
        // poll() 是拉取消息的长轮询
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
        for (ConsumerRecord<String, String> record : records) {
            log.info("收到消息:partition={} offset={} key={} value={}", record.partition(), record.offset(), record.key(), record.value());
        }
    }
}
```

#### 自动提交和手动提交offset

- 提交内容: 把 消费的某个主题 + 消费的某个分区 + 所属的消费组及偏移量 提交到集群的_consumer_offsets主题里面

- 自动提交: 消费者一poll就提交offset, 如果未消费就挂了会导致消息丢失

```java
// 是否自动提交offset, 默认true
props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "true");
// 自动提交offset的间隔时间
props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, 1000);
```

- 手动提交: 

```
spring:
  application:
    name: kafka_producer
  kafka:
    url: 192.168.80.128:9092, 192.168.80.128:9093, 192.168.80.128:9094
    topic-name: my-replicated-topic
```

```java
private final static String CONSUMER_GROUP_NAME = "testGroup";

@Value("${spring.kafka.url}")
String url;

@Value("${spring.kafka.topic-name}")
String topicName;

@Test
void hand() {
    Properties props = new Properties();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, url);
    // 消费分组名
    props.put(ConsumerConfig.GROUP_ID_CONFIG, CONSUMER_GROUP_NAME);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    // 手动提交, 需要设置自动提交为false
    props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
    // 创建一个消费者的客户端
    KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
    // 消费者订阅主题列表
    consumer.subscribe(Arrays.asList(topicName));
    while (true) {
        // poll() 是拉取消息的长轮询
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
        for (ConsumerRecord<String, String> record : records) {
            log.info("收到消息:partition={} offset={} key={} value={}", record.partition(), record.offset(), record.key(), record.value());
        }
        // 所有的消息已消费完
        if (records.count() > 0) {
            // 手动同步提交
            // consumer.commitAsync();
            // 手动异步提交
            consumer.commitAsync(new OffsetCommitCallback() {
                @Override
                public void onComplete(Map<TopicPartition, OffsetAndMetadata> map, Exception e) {
                    if (e != null) {
                        log.error("offset:{}", map);
                        e.printStackTrace();
                    }
                }
            });
        }
    }
}
```

#### 消费者poll消息的过程

- 消费者建立了与broker之间的长连接, 开始poll消息

```java
private final static String CONSUMER_GROUP_NAME = "testGroup";

@Value("${spring.kafka.url}")
String url;

@Value("${spring.kafka.topic-name}")
String topicName;

@Test
void hand() {
    Properties props = new Properties();
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, url);
    // 消费分组名
    props.put(ConsumerConfig.GROUP_ID_CONFIG, CONSUMER_GROUP_NAME);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    // 手动提交, 需要设置自动提交为false
    props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
    // consumer给broker发送心跳的时间间隔
    props.put(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG, 1000);
    // kafka如果超过10s没有收到消费者的心跳, 则会把消费者踢出消费组, 进行rebalance, 把分区分配给其他消费者
    props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10 * 1000);
    // 一次最大拉取消息的条数
    props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 500);
    // 如果两次消费的时间间隔超过30s, kafka会认为消费能力过弱, 将其踢出消费者, 将分区分配给其他消费者
    props.put(ConsumerConfig.MAX_POLL_INTERVAL_MS_CONFIG, 30 * 1000);
    // 创建一个消费者的客户端
    KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
    // 消费者订阅主题列表
    consumer.subscribe(Arrays.asList(topicName));
    while (true) {
        // poll() 是拉取消息的长轮询, 一次至多等1000ms, 少于500条也开始消费
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
        for (ConsumerRecord<String, String> record : records) {
            log.info("收到消息:partition={} offset={} key={} value={}", record.partition(), record.offset(), record.key(), record.value());
        }
        // 所有的消息已消费完
        if (records.count() > 0) {
            // 手动同步提交
             consumer.commitAsync();
        }
    }
}
```

#### 消费者的健康状态检查

消费者每隔1s向kafka集群发送心跳, 集群如果发现10s没有续约的消费者, 则踢出消费组, 触发该消费组的rebalance机制.

```java
// consumer给broker发送心跳的时间间隔
props.put(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG, 1000);
// kafka如果超过10s没有收到消费者的心跳, 则会把消费者踢出消费组, 进行rebalance, 把分区分配给其他消费者
props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10 * 1000);
```

#### 指定分区, 偏移量和时间消费

- 指定分区消费

```java
// 指定消费分区
consumer.assign(Arrays.asList(new TopicPartition(topicName, 0)));
// 从头开始消费
consumer.seekToBeginning(Arrays.asList(new TopicPartition(topicName, 0)));
```

- 指定offset消费

```java
consumer.assign(Arrays.asList(new TopicPartition(topicName, 0)));
consumer.seek(new TopicPartition(topicName, 0), 10);
```

- 指定时间消费

```java
List<PartitionInfo> partitionInfos = consumer.partitionsFor(topicName);
// 从一小时前开始消费
long fetchTime = new Date().getTime() - 1000 * 60 * 60;
Map<TopicPartition, Long> map = new HashMap<>();
for (PartitionInfo par : partitionInfos) {
    map.put(new TopicPartition(topicName, par.partition()), fetchTime);
}
Map<TopicPartition, OffsetAndTimestamp> parMap = consumer.offsetsForTimes(map);
for (Map.Entry<TopicPartition, OffsetAndTimestamp> entry : parMap.entrySet()) {
    TopicPartition key = entry.getKey();
    OffsetAndTimestamp value = entry.getValue();
    if (key == null || value == null) continue;
    long offset = value.offset();
    log.info("partition-{} offset-{}", key.partition(), offset);
    consumer.assign(Arrays.asList(key));
    consumer.seek(key, offset);
}
```

#### 新消费组的消费偏移量

- 当消费主题的是一个新的消费组
  - latest(默认): 只消费自己启动之后发送到主题的消息
  - earliest: 第一次从头开始消费, 以后按照offset记录消费, 这个需要区别于consumer.seekToBeginning(每次都从头开始消费)

```java
props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
```

### SpringBoot中使用Kafka

- 依赖

  ```xml
  <dependency>
      <groupId>org.springframework.kafka</groupId>
      <artifactId>spring-kafka</artifactId>
  </dependency>
  ```

- 配置

  ```yml
  spring:
    kafka:
      bootstrap-servers: 192.168.80.128:9093
      producer:
        retries: 3
        batch-size: 16384
        buffer-memory: 33554432
        asks: 1
        key-serializer: org.apache.kafka.common.serialization.StringSerializer
        value-serializer: org.apache.kafka.common.serialization.StringSerializer
      consumer:
        group-id: default-group
        enable-auto-commit: false
        auto-offset-reset: earliest
        key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
        value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
        max-poll-records: 500
      listener:
        ack-mode: manual_immediate
  ```

- 消息生产者

  ```java
  @RestController
  @RequestMapping("msg")
  public class MyKafkaController {
  
      private static final String topicName = "my-replicated-topic";
  
      @Resource
      private KafkaTemplate<String, String> kafkaTemplate;
  
      @GetMapping("send")
      public Result<String> send() {
          kafkaTemplate.send(topicName, 0, "key", "this is a message!");
          return Result.success();
      }
  }
  ```

- 消息消费者

  ```java
  @KafkaListener(topics = "my-replicated-topic", groupId = "MyGroup1")
  public void listenGroup(ConsumerRecord<String, String> record, Acknowledgment ack) {
      String value = record.value();
      System.out.println(value);
      System.out.println(record);
      // 手动提交offset
      ack.acknowledge();
  }
  ```
