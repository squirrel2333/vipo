
# Kafka工作流程和文件存储机制

![](Kafka架构深入_001.png)

* partition区内有序，并不能保证全局有序（不一定从0号分区开始写）
* 消息以topic分类，生产者消费者都是面向topic
* topic是逻辑概念，partition是物理概念，每个partition对应一个log文件，该log文件中存储的是producer生产的数据。Producer生产的数据会不断追加到该log文件末端，每条数据都有自己的offset。消费者组中的每个消费者，都会实时记录字节消费到了哪个offset，以便出错时，从上次的位置恢复
* 由于生产者生产的消息会不断地追加到log文件末尾，为防止log文件过大导致数据定位效率低下，Kafka采取分片和索引机制，将每个partition分为多个segment。每个segment对应两个文件：.index文件和.log文件。存放在对应的partition文件夹下（上节中的firstTopic/00000000000000.index）。index和log文件名为此segment的第一条消息的offset（上一个segment最大偏移量+1）。“.index”文件存储大量的 索引信息 ，“，“.log”文件存储大量的 数据 ，索引文件中的元数据指向对应数据文件中 message的物理偏移地址 。
* 每消费一条消息，offset就会加1

分页查询：

**00000000000000000.index**

每行大小相同，存放索引信息

|  |  |
| --- | --- |
| 0 | 0 |
| 1 | 237 |
| 2 | 562（指向meeage-2的起始位置偏移量） |
| .... | ... |

**00000000000000.log**

大小不同存放数据

|  |
| --- |
| Message-0 |
| Message-1 |
| Message-2 |
| ... |

![](Kafka架构深入_002.png)

# 生产者

## 分区策略

1. 分区原因
1. 方便集群中扩展，topic可以由若干个partition组成，集群可以适应任意大小的数据
2. 可以提高并发，可以以partition为单位读写
2. 分区的原则

![](Kafka架构深入_003.png)

1. 指明 partition 的情况下，直接将指明的值直接作为 partiton 值；
2. 没有指明 partition 值但有 key 的情况下，将 key 的 hash 值与 topic 的 partition数进行取余得到 partition 值；
3. 既没有 partition 值又没有 key 值的情况下，第一次调用时随机生成一个整数（后面每次调用在这个整数上自增），将这个值与 topic 可用的 partition 总数取余得到 partition值，也就是常说的 round-robin 算法。
3. 消息进入哪个分区的原则

kafka的消息是以key-value的形式存在的。

producer根据消息的key，决定这条消息最终被分配到哪个partition。

分配分区采用的是hash算法，在分区不变的情况下，同一个key总是分配到一个分区。

## 数据可靠性保证

为保证producer发送的数据能可靠的发送到指定topic，topic的每个partition收到producer发送的数据之后，都需要向producer发送ack，收到才会进行下一轮的发送否则重发

![](Kafka架构深入_004.png)

**Kalfa选择了第二种方案**

方案1：

优点：延迟低

缺点：选取新的leadder时，容忍n台故障，需要2n+1个副本

方案2：

优点：选取新leader时，容忍n台故障，需要n+1个副本

缺点：延迟高

### ISR

采用第二种方案之后，设想以下情景： leader 收到数据，所有 follower 都开始同步数据，但有一个 follower，因为某种故障，迟迟不能与 leader 进行同步，那 leader 就要一直等下去，直到它完成同步，才能发送 ack。这个问题怎么解决呢？
 Leader 维护了一个动态的 in-sync replica set (ISR)，意为和 leader 保持同步的 follower 集合。当 ISR 中的 follower 完成数据的同步之后， leader 就会给 follower 发送 ack。如果 follower长 时 间 未 向 leader同步数据 ， 则该follower将被踢出ISR ， 该 时 间 阈 值 由 replica.lag.time.max.ms 参数设定。 Leader 发生故障之后，就会从 ISR 中选举新的 leader。

### ack应答机制

对于某些不重要数据，可靠性要求不高，能够容忍数据少量丢失，没必要等ISR中的follower全部接受成功。所以kafka设置了三种可靠性级别

**acks参数配置：**

* 0：producer不等待broker的ack，这一操作提供最低延迟，broker一接收到还没有写入磁盘就已经返回，当broker故障时有可能丢失数据
* 1：producer等待broker的ack，partition的leader落盘成功后返回ack，如果在follower同步成功之前leader故障，那么会丢失数据。
* -1(all): producer等待broker的ack，partition的leader和follower全部落盘成功后才会返回ack。但是follower同步完成后，broker发送ack之前leader故障，会造成数据重复（leader无法发ack，导致producer重传丢失的包，新上岗的leader就接收了重复的包）

### 故障处理

针对消费过程，如果A是leader接收了10条数据，B是follower接收了8条。此时A挂掉，leader变成了B，此时consumer消费到第九条，找10时，就找不到数据了。所以需要一致性原则来保证消费者消费数据的不丢失

![](Kafka架构深入_005.png)

1. **follower故障**
follower 发生故障后会被临时踢出 ISR，待该 follower 恢复后， follower 会读取本地磁盘记录的上次的HW，并将 log 文件高于 HW 的部分截取掉，从 HW 开始向 leader 进行同步。等该 follower 的 LEO 大于等于该 Partition 的 HW，即 follower 追上 leader 之后，就可以重新加入 ISR 了。
2. **leader 故障**

leader 发生故障之后，会从 ISR 中选出一个新的 leader，之后，为保证多个副本之间的 数据一致性， 其余的 follower 会先将各自的 log 文件高于 HW 的部分截掉，然后从新的 leader同步数据。

注意： 这只能保证副本之间的数据一致性，并不能保证数据不丢失或者不重复。

### Exactly Once

1. 将服务器的ACK级别设置为-1，可以保证Producer到Server之间不会丢失数据，即At Least Once语义。
2. 相对的，将服务器ACK设置为0，可以保证生产者每条消息只会被发送一次，即At Most Once。
3. 但是一些重要信息，比如交易数据，消费者要求既不重复也不丢失，即Exactly Once

 **At Least Once (不丢)** + **幂等性 (不重)** = **Exactly Once (精确一次)**

+ **如何开启**： 在 Producer 端设置 `enable.idempotence = true`。
+ **核心作用**： 生产者无论向 Server 发送了多少次重复数据（比如重试了 10 次），Server 端只持久化 **一条**。 *通俗理解：这相当于把原本需要在下游消费者（Consumer）做的“去重”工作，提前在 Broker 端自动完成了。*

#### 1. 实现原理：PID + 序列号

当开启幂等性后，Producer 会做两件事：

* **领身份证 (PID)**：Producer 初始化时，Broker 会给它分配一个唯一的 Producer ID (PID)。
* **贴流水号 (Sequence Number)**：Producer 发送给同一分区（Partition）的每条消息，都会带上一个单调递增的序列号（0, 1, 2...）。

**Broker 端的去重逻辑**： Broker 会在内存里缓存 `<PID, Partition, SeqNumber>`。

* 如果收到消息的 Seq = 5，且内存里记录当前只接收到 4，**通过**。
* 如果收到消息的 Seq = 5，但内存里已经有 5 了，Broker 判定为**重复提交，直接丢弃，不进行持久化**。

#### 2. 致命局限：只能保证“单会话、单分区”

这也是为什么原文说“无法保证跨分区跨会话的 Exactly Once”。

* **局限一：跨会话失效 (重启失效)** 如果 Producer 挂了重启，它会重新初始化，获得一个新的 PID（换了张身份证）。Broker 无法识别这个新 PID 和旧 PID 其实是同一个生产者，因此**重启后的重试会导致重复**。
* **局限二：跨分区失效** 序列号（SeqNumber）是针对每个分区的。如果你发给 Partition A 的消息重复发到了 Partition B，Broker 无法利用 Partition A 的序列号去帮助 Partition B 去重。

# 消费者

## 消费方式

consumer采用 pull（拉 模式从 broker中读取数据。push（推 模式很难适应消费速率不同的消费者，因为消息发送速率是由 broker决定的。它的目标是尽可能以最快速度传递消息，但是这样很容易造成 consumer来不及处理消息，典型的表现就是拒绝服务以及网络拥塞。而pull 模式则可以根据consumer 的消费能力以适当的速率消费消息。

pull 模式不足之处是，如果kafka 没有数据，消费者可能会陷入循环中，一直返回空数据。针对这一点，Kafka 的消费者在消费数据时会传入一个时长参数timeout，如果当前没有数据可供消费，consumer 会等待一段时间之后再返回，这段时长即为timeout。

## 分区分配策略

一个consumer group 中有多个consumer，一个 topic 有多个partition，所以必然会涉及到partition 的分配问题，即确定那个partition 由哪个consumer 来消费。**在消费者数量变化时，触发重新分配。**

写入消息时，每条消息是在一个partition中的segment写入。写入一个消息，对应的partition中的offset就会加1，消息也会按照下列的分配策略被消费者消费

分配之前，每个分区都会计算出一个哈希值，根据哈希值进行分配

Kafka 有两种分配策略，一是RoundRobin，一是Range。

![](Kafka架构深入_006.png)

### 1. RoundRobin

![](Kafka架构深入_007.png)

**面向consumer划分的。**轮询地将partition分配给消费者组中的每个消费者。这种方式的前提是，消费者组中的每个消费者订阅的主题一样。

如果有A,B两种Topic，消费者a只消费A，b只消费B就不能用上述方式

### 2. Range（默认）

![](Kafka架构深入_008.png)

**面向topicl来划分的。**7/3 ~2 => 3 2 2

带来的问题是分配不均衡，T1:0 1 2 三个partition T2:0 1 2 三个partition => A: 0 1（来自T1）0 1（来自T2） B：2（T1）2（T2）

## offset维护

由于consumer 在消费过程中可能会出现断电宕机等故障，consumer 恢复后，需要从故障前的位置的继续消费，所以consumer 需要实时记录自己消费到了哪个offset，以便故障恢复后继续消费。

offset是按照消费者组来保存的。例如消费者A消费了一半，消费者组中加入消费者B，给B分配的partition不应该从头开始消费

以下是存在zookeeper中的offset

![](Kafka架构深入_009.png)

Kafka 0.9 版本之前，consumer 默认将offset 保存在Zookeeper 中，从0.9 版本开始，consumer 默认将offset 保存在Kafka 一个内置的topic 中，该topic 为\_\_consumer\_offsets。

**想要消费offset的partition**

1. 修改配置文件consumer.properties

`exclude.internal.topics=false`

2. 读取offset

0.11.0.0 之前版本:

*bin/kafka console consumer.sh --topic \_\_consumer\_offsets --zookeeper hadoop 102 :2181 --formatter "kafka.coordinator.GroupMetadataManager\\$OffsetsMessageFormatter"*

*--consumer.config config/consumer.properties --from-beginning*

0.11.0.0之后版 本 (含 ):

*bin/kafka console consumer.sh --topic \_\_consumer\_offsets --zookeeper hadoop 102 :2181 --formatter "kafka.coordinator.group.GroupMetadataManager\\$OffsetsMessageFormatter" --consumer.config config/consumer.properties --from-beginning*

## 消费者组案例

1. 需求
1. 测试同一个消费者组中的消费者，同一时刻只能有一个消费者消费
2. 测试更改分区后，消费的数据的消费者会改变，说明有组加入时，消费者组中的partition会重新分配

# Kafka高效读写数据

1. 顺序写磁盘

Kafka的 producer生产数据，要写入到 log文件中，写的过程是一直追加到文件末端，为顺序写 。 官网有数据表明，同样的磁盘，顺序写能到 600M/s，而随机写只有 100K/s。这与磁盘的机械机构有关，顺序写之所以快，是因为其省去了 大量磁头寻址的时间 。

2. 零拷贝技术

![](Kafka架构深入_010.png)

3. 分布式并发读写
4. 字节拷贝产生高负载，因此在producer，broker，consumer间共享标准二进制消息格式
5. Kafka 以高效的批处理格式支持一批消息可以压缩在一起发送到服务器。这批消息将以压缩格式写入，并且在日志中保持压缩，只会在 consumer 消费时解压缩。

# Zookeeper在Kafka中的作用

Kafka 集群中有一个broker 会被选举为Controller，负责管理集群broker 的上下线，所有topic 的分区副本分配和leader 选举等工作。Control的选举是简单的争抢策略。谁先注册谁就是controller

Controller 的管理工作都是依赖于Zookeeper 的。

以下为partition 的leader 选举过程：

![](Kafka架构深入_011.png)

# Kafka事务

假设Producer生产了一个topic的三个Partition，前两个写入成功第三个过程中Producer挂掉。重启Producer后ProducerID就改变了。Kafka 从0.11 版本开始引入了事务支持。事务可以保证Kafka 在Exactly Once 语义的基础上，生产和消费可以跨分区和会话，要么全部成功，要么全部失败。

## Producer事务

为了实现跨分区跨会话的事务（精准一次性），需要引入一个全局唯一的Transaction ID，并将 Producer获得的 PID和 Transaction ID绑定。这样当 Producer重启后就可以通过正在进行的 Transaction ID获得原来的 PID。

为了管理Transaction Kafka引入了一个新的组件 Transaction Coordinator。 Producer就是通过和 Transaction Coordinator交互获得 Transaction ID对应的任务状态。Transaction Coordinator还负责将事务所有写入 Kafka的一个内部 Topic，这样即使整个服务重启，由于事务状态得到保存，进行中的事务状态可以得到恢复，从而继续进行。

## Consumer事务

上述事务机制主要是从Producer方面考虑，对于 Consumer而言，事务的保证就会相对较弱，尤其时无法保证 Commit的信息被精确消费。这是由于 Consumer可以通过 offset访问任意信息，而且不同的 Segment File生命周期不同，同一事务的消息可能会出现重启后被删除的情况。

