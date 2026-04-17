
# Producer API

## 消息发送流程

Kafka的 Producer发送消息采用的是 异步发送 的方式。在消息发送的过程中，涉及到了两个线程 main线程和 Sender线程 ，以及 一个线程共享变量 RecordAccumulator。main线程将消息发送给 RecordAccumulator Sender线程不断从 RecordAccumulator中拉取消息发送到 Kafka broker。

![](KafkaAPI_001.png)

Interceptor：拦截器

Serializer：序列化器

Partitioner：分区器

## 异步发送

1. 导入依赖

```
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>0.11.0.0</version>
</dependency>
```
2. 编写代码

需要用到的类：

KafkaProducer：需要创建一个生产者对象，用来发送数据

ProducerConfig：获取所需的一系列配置参数

ProducerRecord：每条数据都要封装成一个ProducerRecord 对象

1. **不带回调函数的API**

```
public class MyProducer {
    public static void main(String[] args) {
        //1.创建kafka生产者配置信息, 所有的参数都在ProducerConfig，CommonConfig，ConsumerConfig中找到
        Properties properties = new Properties();
        //2.指定连接的kafak集群,broker-list
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "hadoop102:9092");
        //3.ack应答级别
        properties.put(ProducerConfig.ACKS_CONFIG, "all");
        //4.重试次数
        properties.put("retries", 1);
        //5.批次大小
        properties.put("batch.size", 16384);
        //6.等待时间,如果数据没到16k，1ms后也会提交
        properties.put("linger.ms", 1);
        //7.RecordAccumulator 缓冲区大小
        properties.put("buffer.memory", 33554432);
        //8.kv的序列化类
        properties.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        properties.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        //9.创建生产者对象
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
        //10.发送数据
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<>("first", "xxx" + i));
        }
        //11.关闭资源
        producer.close();
    }
}
```
2. **带回调函数的 API**

回调函数会在

producer收到 ack时调用，为异步调用， 该方法有两个参数，分别是

RecordMetadata 和 Exception ，如果 Exception 为 null ，说明消息发送成功，如果

Exception 不为 null ，说明消息发送失败。

注意：消息发送失败会自动重试，不需要我们在回调函数中手动重试。

```
public class CallBackProducer {
    public static void main(String[] args) {
        //1. 创建配置信息
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "hadoop:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<String, String>("first", "atguigu" + i), new Callback() {
                @Override
                public void onCompletion(RecordMetadata metadata, Exception exception) {
                    if (exception == null) {
                        System.out.println(metadata.partition() +"--"+ metadata.offset());
                    } else
                        exception.printStackTrace();
                }
            });
        }
    }
}
```
## 同步发送

同步发送的意思就是，一条消息发送之后，会阻塞当前线程， 直至返回 ack。

由于send方法返回的是一个 Future对象，根据 Futrue对象 的特点，我们也可以实现同步发送的效果 ，只需在调用 Future对象的 get方发即可。只需在调用 Future对象的 get方发即可。

```
public class CustomProducer {
    public static void main(String[] args) throws ExecutionException,InterruptedException {
        Properties props = new Properties();
        props.put("bootstrap.servers", "hadoop102:9092");//kafka 集群， broker list
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apach e.kafka.common.serialization.StringSerializer");
        Producer<String, String> producer = new KafkaProducer<>(props);
        for (int i = 0; i < 100; i++) {
            producer.send(new ProducerRecord<String, String>("first", Integer.toString(i), Int eger.toString(i))).get();
        }
        producer.close();
	}
}
```
# Consumer API

## 自动提交offset

Consumer消费数据时的可靠性是很容易保证的，因为数据在 Kafka中是持久化的，故不用担心数据丢失问题。

由于consumer在消费过程中可能会出现断电宕机等故障， consumer恢复后，需要从故障前的位置的继续消费，所以 consumer需要实时记录自己消费到了哪个 offset，以便故障恢复后继续消费。所以offset的维护是 Consumer消费数据是必须考虑的问题。

需要用到的类：

KafkaConsumer 需要创建一个 消费 者对象，用来消费数据

ConsumerConfig 获取所需的一系列配置参数

ConsuemrRecord 每条数据都要封装成一个 ConsumerRecord对象

为了使我们能够专注于自己的业务逻辑，Kafka提供了自动提交 offset的功能 。

offset的相关参数：

enable.auto.commit 是否开启自动提交 offset功能

auto.commit.interval.ms 自动提交 offset的时间间隔

```
public class MyConsumer {
    public static void main(String[] args) {
        Properties properties = new Properties();
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "hadoop:9092");
        // 开启自动提交offset
        properties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, true);
        // 自动提交的延迟ms
        properties.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");
        // KV反序列化
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringDeserializer");
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringDeserializer");
        //消费者组
        properties.put(ConsumerConfig.GROUP_ID_CONFIG, "bigdata");

        //创建消费者
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(properties);
        //订阅主题
        consumer.subscribe(Arrays.asList("first", "second"));

        //一直拉取
        while (true) {
            //获取数据
            ConsumerRecords<String, String> consumerRecords = consumer.poll(100); //poll是在没有数据时，timeout掉的轮询间隔
            //解析并打印
            for (ConsumerRecord<String, String> consumerRecord : consumerRecords) {
                System.out.println(consumerRecord.key() + "---" + consumerRecord.key());
            }
        }
    }
}
```
## 手动提交offset

没有提交时，相当于没有写回offset，当消费者重启时，会去offset里取回先前的位置，重复消费。（只有启动时会访问外部offset，不挂掉时会在内存中维护一个offset）

虽然自动提交offset十分简介便利，但由于其是基于时间提交的 开发人员难以把握offset提交的时机。因此 Kafka还提供了手动提交 offset的 API。

手动提交offset的方法有两种：分别是 commitSync（同步提交） 和 commitAsync（异步提交） 。两者的相同点是，都会将 本次 poll的一批数据最高的偏移量提交 ；不同点是commitSync阻塞当前线程，一直到提交成功，并且会自动失败重试（ 由不可控因素导致，也会出现提交失败 ）；而 commitAsync则没有失败重试机制，故有可能提交失败。

1. **同步提交offset**

```
public class CustomConsumer {
	public static void main(String[] args) {
        Properties props = new Properties();
        //Kafka 集群
        props.put("bootstrap.servers", "hadoop102:9092");
        消费者组，只要 group.id 相同，就属于同一个消费者组
        props.put("group.id", "test");
        props.put("enable.auto.commit", "false");// 关闭自动提交 offset
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache .kafka.common.serialization.StringDeserializer");
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Arrays.asList("first"));// 消费者订阅主题
        while (true) {
            //消费者拉取数据
            ConsumerRecords<String, String> records = consumer.poll(100);
            for (ConsumerRecord<String, String> record : records) {
                System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), value());
            }
            同步提交，当前线程会阻塞 直到 offset 提交成功
            consumer.commitSync();
     	}
    }
}
```
2. **异步提交**

虽然同步提交 offset更可靠一些，但是由于其会阻塞当前线程，直到提交成功。因此，吞吐量会收到很大的影响。因此更多的情况下，会选用异步提交 offset的方式。

```
public class CustomConsumer {
    public static void main(String[] args) {
        Properties props = new Properties();
        //Kafka 集群
        props.put("bootstrap.servers", "hadoop102:9092");
        消费者组，只要 group.id 相同，就属于同一个消费者组
        props.put("group.id", "test");
        关闭自动提交 offset
        props.put("enable.auto.commit", "false");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.");
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Arrays.asList("first"));// 消费者订阅主题
        while (true) {
        	ConsumerRecords<String, String> records = consumer.poll(100);// 消费者拉取数据
        	for (ConsumerRecord<String, String> record : records) {
        		System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
            }
            //异步提交
            consumer.commitAsync(new OffsetCommitCallback() {
                @Override
                public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception exception) {
                    if (exception != null) {
                    	System.err.println("Commit failed for" +offsets);
                    }
                }
            });
        }
    }
}

```
3. **数据漏消费和重复消费分析**

无论是同步提交还是异步提交offset，都有可能会造成数据的漏消费或者重复消费。先提交 offset后消费，有可能造成数据的漏消费；而先消费后提交 offset，有可能会造成数据的重复消费。

## 自定义存储offset

除了将offset存储在zookeeper和kafka中，还可以自定义存储offset。自定义最好的业务场景是放在mysql中使用事务

当有新的消费者加入消费者组、已有的消费者推出消费者组或者所订阅的主题的分区发生变化 ，就会触发到分区的重新分配，重新分配的过程叫做 Rebalance。

消费者发生Rebalance之后，每个消费者消费的分区就会发生变 化。 因此消费者要首先获取到自己被重新分配到的分区，并且定位到每个分区最近提交的offset位置继续消费。

要实现自定义存储offset，需要借助 ConsumerRebalanceListener 以下为示例代码 ，其中提交和获取 offset的方法，需要根据所选的 offset存储系统自行实现。

```
public class CustomConsumer {
    private static Map<TopicPartition, Long> currentOffset = new HashMap<>();
    public static void main(String[] args) {
        //创建配置信息
        Properties props = new Properties();
        //Kafka 集群
        props.put("bootstrap.servers", "hadoop102:9092");
        //消费者组，只要 group.id 相同，就属于同一个消费者组
        props.put("group.id", "test");
        //关闭自动提交 offset
        props.put("enable.auto.commit", "false");
        //Key 和 Value 的反序列化类
        props.put("key.deserializer","org.apache.kafka.common.serialization.StringSe");
        props.put("value.deserializer","org.apache.kafka.common.serialization.");
        //创建一个消费者
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        //消费者订阅 主题
        consumer.subscribe(Arrays.asList("first"), new ConsumerRebalanceListener() {
        	//该方法会在 Rebalance 之前调用
            @Override
            public void onPartitionsRevoked(Collection<TopicPartition> partitions) {
                commitOffset(currentOffset);
            }
            //该方法会在 Rebalance 之后调用
            @Override
            public void onPartitionsAssigned(Collection<TopicPartition> partitions) {
                currentOffset.clear();
				for (TopicPartition partition : partitions) {
					consumer.seek(partition, getOffset(partition));//定位到最近提交的 offset 位置继续消费
                }
            }
        });
        while (true) {
			ConsumerRecords<String, String> records = consumer.poll(100);// 消费者拉取数据
			for (ConsumerRecord<String, String> record : records) {
				System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
				currentOffset.put(new TopicPartition(record.topic(),record.partition()), record.offset());
            }
			commitOffset(currentOffset);// 异步提交
        }
    }
    //获取某分区的最新 offset
	private static long getOffset(TopicPartition partition) {
        //从mysql里读
		return 0;
    }
	//提交该消费者所有分区的 offset
	private static void commitOffset(Map<TopicPartition, Long> currentOffset) {
        //往mysql里写
    }
}
```
## 消费者重置offset

命令行中可以添加--from-begin标签让消费者从头开始消费，API中？

ConsumerConfig中 AUTO\_OFFSET\_RESET\_CONFIG="auto.offset.reset" 默认值是latest，要从头消费改成earliest，重置消费者offset

earliest生效条件：当不在之前同一个消费者组中时，或者offset已经删除，数据被删除时，可以重置

# 自定义Interceptor

## 拦截器原理

Producer拦截器 (interceptor)是在 Kafka 0.10版本被引入的，主要用于实现 clients端的定制化控制逻辑。

对于producer而言， interceptor使得用户在消息发送前以及 producer回调逻辑前有机会对消息做一些定制化需求，比如 修改消息 等。同时， producer允许用户指定多个 interceptor按序作用于同一条消息从而形成一个拦截链 (interceptor chain)。 Intercetpor的实现接口是org.apache.kafka.clients.producer.ProducerInterceptor，其 定义的方法包括：

1. configure(configs)

获取配置信息和初始化数据时调用 。

2. onSend(ProduceRecord)

该方法封装进KafkaProducer.send方法中，即它运行在用户主线程中。Producer确保在消息被序列化以及计算分区前调用该方法。

用户可以在该方法中对消息做任何操作，但最好保证不要修改消息所属的 topic和分区， 否则会影响目标分区的计算 。

3. onAcknowledgement(RecordMetadata, Exception)

该方法会在消息从 RecordAccumulator成功 发送到 Kafka Broker之后，或者在发送过程中失败时调用。 并且通常都是在 producer回调逻辑触发之前。 onAcknowledgement运行在producer的 IO线程中，因此不要在该方法中放入很重的逻辑，否则会拖慢 producer的消息发送效率 。

4. close

关闭interceptor，主要用于执行一些资源清理工作。如前所述，interceptor可能被运行在多个线程中，因此在具体实现时用户需要自行确保线程安全。另外 倘若指定了多个 interceptor，则 producer将按照指定顺序调用它们 ，并仅仅是捕获每个 interceptor可能抛出的异常记录到错误日志中而非在向上传递。这在使用过程中要特别留意。

