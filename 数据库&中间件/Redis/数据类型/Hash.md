
Redis hash 是一个 string 类型的 field（字段） 和 value（值） 的映射表`HashMap<String, HashMap<String,Object>>`，hash 特别适合用于存储对象。

Redis 中每个 hash 可以存储 232 - 1 键值对（40多亿）。

# hset/hget/hgetall

```
127.0.0.1:6379> hset user:01 id 01 name hqw age 22
(integer) 3
127.0.0.1:6379> hget user:01
(error) ERR wrong number of arguments for 'hget' command
127.0.0.1:6379> hget user:01 id
"01"
127.0.0.1:6379> hgetall user:01
1) "id"
2) "01"
3) "name"
4) "hqw"
5) "age"
6) "22"
```
# hmset/hmget

```
127.0.0.1:6379> hmset user:02 id 02 name zzz age 33
OK
127.0.0.1:6379> hmget user:02 id name age
1) "02"
2) "zzz"
3) "33"
```
# hdel

```
127.0.0.1:6379> HDEL user:02 age
(integer) 1
127.0.0.1:6379> hmget user:02 id name age
1) "02"
2) "zzz"
3) (nil)
```
# hlen/hexists

```
127.0.0.1:6379> hgetall user:01
1) "id"
2) "01"
3) "name"
4) "hqw"
5) "age"
6) "22"
127.0.0.1:6379> hlen user:01
(integer) 3
127.0.0.1:6379> hexists user:01 name
(integer) 1
127.0.0.1:6379> hexists user:01 sex
(integer) 0
```
# hkeys/hvals

```
127.0.0.1:6379> hkeys user:01
1) "id"
2) "name"
3) "age"
127.0.0.1:6379> hvals user:01
1) "01"
2) "hqw"
3) "22"
```
# hincrby/hincrbyfloat

```
127.0.0.1:6379> hset user:01 score 78.5
(integer) 1
127.0.0.1:6379> hgetall user:01
1) "id"
2) "01"
3) "name"
4) "hqw"
5) "age"
6) "22"
7) "score"
8) "78.5"
127.0.0.1:6379> hincrby user:01 age 2
(integer) 24
127.0.0.1:6379> hincrbyfloat user:01 score 1.5
"80"
```
# hsetnx

```
127.0.0.1:6379> hsetnx user:01 email asdsa@qwq.com
(integer) 1
```
# 应用场景

购物车

![](Hash_001.png)

# Redis hash 命令

下表列出了 redis hash 基本的相关命令：

|  |  |
| --- | --- |
| 序号 | 命令及描述 |
| 1 | [HDEL key field1 [field2]](https://www.runoob.com/redis/hashes-hdel.html)删除一个或多个哈希表字段 |
| 2 | [HEXISTS key field](https://www.runoob.com/redis/hashes-hexists.html)查看哈希表 key 中，指定的字段是否存在。 |
| 3 | [HGET key field](https://www.runoob.com/redis/hashes-hget.html)获取存储在哈希表中指定字段的值。 |
| 4 | [HGETALL key](https://www.runoob.com/redis/hashes-hgetall.html)获取在哈希表中指定 key 的所有字段和值 |
| 5 | [HINCRBY key field increment](https://www.runoob.com/redis/hashes-hincrby.html)为哈希表 key 中的指定字段的整数值加上增量 increment 。 |
| 6 | [HINCRBYFLOAT key field increment](https://www.runoob.com/redis/hashes-hincrbyfloat.html)为哈希表 key 中的指定字段的浮点数值加上增量 increment 。 |
| 7 | [HKEYS key](https://www.runoob.com/redis/hashes-hkeys.html)获取所有哈希表中的字段 |
| 8 | [HLEN key](https://www.runoob.com/redis/hashes-hlen.html)获取哈希表中字段的数量 |
| 9 | [HMGET key field1 [field2]](https://www.runoob.com/redis/hashes-hmget.html)获取所有给定字段的值 |
| 10 | [HMSET key field1 value1 [field2 value2 ]](https://www.runoob.com/redis/hashes-hmset.html)同时将多个 field-value (域-值)对设置到哈希表 key 中。 |
| 11 | [HSET key field value](https://www.runoob.com/redis/hashes-hset.html)将哈希表 key 中的字段 field 的值设为 value 。 |
| 12 | [HSETNX key field value](https://www.runoob.com/redis/hashes-hsetnx.html)只有在字段 field 不存在时，设置哈希表字段的值。 |
| 13 | [HVALS key](https://www.runoob.com/redis/hashes-hvals.html)获取哈希表中所有值。 |
| 14 | [HSCAN key cursor [MATCH pattern] [COUNT count]](https://www.runoob.com/redis/hashes-hscan.html)迭代哈希表中的键值对。 |

