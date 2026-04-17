
redis版本的MQ 消息中间件

redis5之前的痛点：两种方案

1. List实现消息队列 太简单点对点的模式
2. Pub/Sub 无法持久化，也没有Ack机制保证可靠性

5.0之后：

# 底层原理

![](Stream_001.png)

一个消息链表，将所有加入的消息都串起来，每个消息都有一个唯一的 ID 和对应的内容

|  |  |
| --- | --- |
| Message Content | 消息内容 |
| Consumer group | 消费组，通过XGROUP CREATE 命令创建，同一个消费组可以有多个消费者 |
| Last\_delivered\_id | 游标，每个消费组会有个游标 last\_delivered\_id，任意一个消费者读取了消息都会使游标 last\_delivered\_id 往前移动。 |
| Consumer | 消费者，消费组中的消费者 |
| Pending\_ids | 消费者会有一个状态变量，用于记录被当前消费已读取但未ack的消息Id，如果客户端没有ack，这个变量里面的消息ID会越来越多，一旦某个消息被ack它就开始减少。这个pending\_ids变量在Redis官方被称之为 PEL(Pending Entries List)，记录了当前已经被客户端读取的消息，但是还没有 ack (Acknowledge character：确认字符），它用来确保客户端至少消费了消息一次，而不会在网络传输的中途丢失了没处理 |

# XADD

XADD 用于向Stream 队列中添加消息，如果指定的Stream 队列不存在，则该命令执行时会新建一个Stream 队列

```
# * 表示自增主键
127.0.0.1:6379> xadd mystream * id 11 cname zs
"1693106994870-0" # 消息ID 毫秒时间戳-该时间下的第几条消息
127.0.0.1:6379> xadd mystream * id 12 cname ls
"1693107008360-0"
127.0.0.1:6379> xadd mystream * id 13 cname ww
"1693107020528-0"
# 消息的内容类似kv键值对
127.0.0.1:6379> xadd mystream * k1 v1 k2 v2 k3 v3
"1693107051600-0"

# 自定义id 必须以上面的格式
127.0.0.1:6379> xadd mystream 1 k1 v1 k2 v2 k3 v3
(error) ERR The ID specified in XADD is equal or smaller than the target stream top item
# 不能重复
127.0.0.1:6379> xadd mystream 1693107051600-0 k1 v1 k2 v2 k3 v3
(error) ERR The ID specified in XADD is equal or smaller than the target stream top item
127.0.0.1:6379> xadd mystream 1693107051600-1 k1 v1 k2 v2 k3 v3
"1693107051600-1"'

127.0.0.1:6379> type mystream
stream
```
# XRANGE

```
127.0.0.1:6379> xrange mystream - +
1) 1) "1693106994870-0"
   2) 1) "id"
      2) "11"
      3) "cname"
      4) "zs"
2) 1) "1693107008360-0"
   2) 1) "id"
      2) "12"
      3) "cname"
      4) "ls"
3) 1) "1693107020528-0"
   2) 1) "id"
      2) "13"
      3) "cname"
      4) "ww"
4) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
5) 1) "1693107051600-1"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"

# count 表示条数
127.0.0.1:6379> xrange mystream - + count 1
1) 1) "1693106994870-0"
   2) 1) "id"
      2) "11"
      3) "cname"
      4) "zs"
```
# xrevrange

```
127.0.0.1:6379> xrevrange mystream + -
1) 1) "1693107051600-1"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
2) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
3) 1) "1693107020528-0"
   2) 1) "id"
      2) "13"
      3) "cname"
      4) "ww"
4) 1) "1693107008360-0"
   2) 1) "id"
      2) "12"
      3) "cname"
      4) "ls"
5) 1) "1693106994870-0"
   2) 1) "id"
      2) "11"
      3) "cname"
      4) "zs"
```
# xdel

```
127.0.0.1:6379> xdel mystream 1693107051600-1
(integer) 1
127.0.0.1:6379> xrange mystream - +
1) 1) "1693106994870-0"
   2) 1) "id"
      2) "11"
      3) "cname"
      4) "zs"
2) 1) "1693107008360-0"
   2) 1) "id"
      2) "12"
      3) "cname"
      4) "ls"
3) 1) "1693107020528-0"
   2) 1) "id"
      2) "13"
      3) "cname"
      4) "ww"
4) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
```
# xtrim

```
# 允许的最大长度，从时间戳小的开始修剪
127.0.0.1:6379> xtrim mystream maxlen 2
(integer) 2
127.0.0.1:6379> xrange mystream - +
1) 1) "1693107020528-0"
   2) 1) "id"
      2) "13"
      3) "cname"
      4) "ww"
2) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"

# 允许的最小id
127.0.0.1:6379> xrange mystream - +
1) 1) "1693107020528-0"
   2) 1) "id"
      2) "13"
      3) "cname"
      4) "ww"
2) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
3) 1) "1693189001630-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
4) 1) "1693189005959-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"

127.0.0.1:6379> xtrim mystream minid 1693107051600-0
(integer) 1

127.0.0.1:6379> xrange mystream - +
1) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
2) 1) "1693189001630-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
3) 1) "1693189005959-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
```
# xread

用于获取消息，只会返回大于指定id的消息

```
# count表示读几条消息 不指定会返回所有
# $表示特殊id，当前stream已经存储的最大id作为最后一个id，$表示大于当前最大id的消息
127.0.0.1:6379> xread count 2 streams mystream $
(nil)
# 0-0代表最小id开始获取stream中的消息 也可以使用0(00/000...)代替
127.0.0.1:6379> xread count 2 streams mystream 0-0
1) 1) "mystream"
   2) 1) 1) "1693107051600-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
            5) "k3"
            6) "v3"
      2) 1) "1693189001630-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"

# block 是否以阻塞方式读，默认不阻塞，millisecondes设置为0表示永远阻塞
127.0.0.1:6379> xread count 1 block 0 streams mystream $
...
# 等到有消息进来，就会读到

```
![](Stream_002.png)

# xgroup

```
# $表示从尾消费（时间最晚 队尾新来）
127.0.0.1:6379> xgroup create mystream groupX $
OK
# 0表示从头消费（时间最早）
127.0.0.1:6379> xgroup create mystream groupY 0
OK
```
# xreadgroup

```
# > 表示从第一条尚未被消费的消息开始读取
127.0.0.1:6379> xreadgroup group groupY consumer1 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1693107051600-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
            5) "k3"
            6) "v3"
      2) 1) "1693189001630-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
      3) 1) "1693189005959-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
# groupY中的消费者已经读完了已有的消息，组内的其他消费者不能重复消费
127.0.0.1:6379> xreadgroup group groupY consumer2 streams mystream >
(nil)

# 另一个消费者组中的还能读
127.0.0.1:6379> xreadgroup group groupZ consumer2 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1693107051600-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
            5) "k3"
            6) "v3"
      2) 1) "1693189001630-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
      3) 1) "1693189005959-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
```
通常会让组内的每个消费者，同时读取部分消息，实现消息均衡负载

```
127.0.0.1:6379> xreadgroup group groupR consumer1 count 1 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1693107051600-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
            5) "k3"
            6) "v3"
127.0.0.1:6379> xreadgroup group groupR consumer2 count 2 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1693189001630-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
      2) 1) "1693189005959-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
```
# 断点续读

![](Stream_003.png)

# XPENDING

```
127.0.0.1:6379> xpending mystream groupR
1) (integer) 3
2) "1693107051600-0"
3) "1693189005959-0"
4) 1) 1) "consumer1" # consumer1读一条
      2) "1"
   2) 1) "consumer2" # consumer1读两条
      2) "2"

# consumer读取的两条
127.0.0.1:6379> xpending mystream groupR - + 10 consumer2
1) 1) "1693189001630-0"
   2) "consumer2"
   3) (integer) 469952
   4) (integer) 1
2) 1) "1693189005959-0"
   2) "consumer2"
   3) (integer) 469952
   4) (integer) 1
```
# xack

```
# 确认签收一条
127.0.0.1:6379> xack mystream groupR 1693189001630-0
(integer) 1
127.0.0.1:6379> xpending mystream groupR - + 10 consumer2
1) 1) "1693189005959-0"
   2) "consumer2"
   3) (integer) 560522
   4) (integer) 1

# 此时groupR中的pending少了一条
127.0.0.1:6379> xpending mystream groupR
1) (integer) 2
2) "1693107051600-0"
3) "1693189005959-0"
4) 1) 1) "consumer1"
      2) "1"
   2) 1) "consumer2"
      2) "1"

# xrange里没有少
127.0.0.1:6379> xrange mystream - +
1) 1) "1693107051600-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
      5) "k3"
      6) "v3"
2) 1) "1693189001630-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
3) 1) "1693189005959-0"
   2) 1) "k1"
      2) "v1"
      3) "k2"
      4) "v2"
```
# xinfo

```
127.0.0.1:6379> xinfo stream mystream
 1) "length"
 2) (integer) 3
 3) "radix-tree-keys"
 4) (integer) 1
 5) "radix-tree-nodes"
 6) (integer) 2
 7) "last-generated-id"
 8) "1693189005959-0"
 9) "max-deleted-entry-id"
10) "1693107051600-1"
11) "entries-added"
12) (integer) 7
13) "recorded-first-entry-id"
14) "1693107051600-0"
15) "groups"
16) (integer) 4
17) "first-entry"
18) 1) "1693107051600-0"
    2) 1) "k1"
       2) "v1"
       3) "k2"
       4) "v2"
       5) "k3"
       6) "v3"
19) "last-entry"
20) 1) "1693189005959-0"
    2) 1) "k1"
       2) "v1"
       3) "k2"
       4) "v2"
```
