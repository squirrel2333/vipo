
Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。

集合对象的编码可以是 intset 或者 hashtable。

Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。

# SADD

```
127.0.0.1:6379> sadd set1 1 1 1 2 2 3 4 5
(integer) 5
redis 127.0.0.1:6379> SADD runoobkey mysql
(integer) 1
redis 127.0.0.1:6379> SADD runoobkey mysql
(integer) 0
```
# SMEMBERS/SISMEMBER

```
127.0.0.1:6379> smembers set1
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"

127.0.0.1:6379> sismember set1 6
(integer) 0
127.0.0.1:6379> sismember set1 5
(integer) 1
```
# SREM

```
127.0.0.1:6379> srem set1 y
(integer) 0
127.0.0.1:6379> srem set1 1
(integer) 1
127.0.0.1:6379> smembers set1
1) "2"
2) "3"
3) "4"
4) "5"
```
# SCARD

```
127.0.0.1:6379> scard set1
(integer) 4
```
# SRANDMEMBER

```
127.0.0.1:6379> srandmember set1
"3"
127.0.0.1:6379> srandmember set1 2
1) "3"
2) "5"
127.0.0.1:6379> srandmember set1 2
1) "4"
2) "5"
```
# SPOP

```
127.0.0.1:6379> spop set1 1
1) "3"
127.0.0.1:6379> spop set1 2
1) "2"
2) "5"
127.0.0.1:6379> smembers set1
1) "4"
```
# SMOVE

```
127.0.0.1:6379> sadd set2 a b c
(integer) 3
127.0.0.1:6379> sadd set1 1 2 3 4 5
(integer) 4
127.0.0.1:6379> smove set1 set2 4
(integer) 1
127.0.0.1:6379> smembers set2
1) "a"
2) "b"
3) "c"
4) "4"
```
# 集合运算

```
127.0.0.1:6379> sadd set1 a b c 1 2
(integer) 5
127.0.0.1:6379> sadd set2 1 2 3 a x
(integer) 5

# 差集 属于set1但不属于set2
127.0.0.1:6379> sdiff set1 set2
1) "b"
2) "c"

# 并集
127.0.0.1:6379> sunion set1 set2
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
6) "3"
7) "x"

# 交集
127.0.0.1:6379> sinter set1 set2
1) "a"
2) "1"
3) "2"
# 不返回交集的结果集，只返回交集的个数
# 2个集合 set1 set2的交集
127.0.0.1:6379> sintercard 2 set1 set2
(integer) 3
127.0.0.1:6379> sintercard 2 set1 set2 limit 1
(integer) 1
127.0.0.1:6379> sintercard 2 set1 set2 limit 2
(integer) 2
127.0.0.1:6379> sintercard 2 set1 set2 limit 3
(integer) 3
127.0.0.1:6379> sintercard 2 set1 set2 limit 4
(integer) 3
```
# 应用场景

微信抽奖

![](Set_001.png)

朋友圈点赞

![](Set_002.png)

可能认识的人

SDIFF user1 user2 求差集

# Redis 集合命令

下表列出了 Redis 集合基本命令：

|  |  |
| --- | --- |
| 序号 | 命令及描述 |
| 1 | [SADD key member1 [member2]](https://www.runoob.com/redis/sets-sadd.html)向集合添加一个或多个成员 |
| 2 | [SCARD key](https://www.runoob.com/redis/sets-scard.html)获取集合的成员数 |
| 3 | [SDIFF key1 [key2]](https://www.runoob.com/redis/sets-sdiff.html)返回第一个集合与其他集合之间的差异。 |
| 4 | [SDIFFSTORE destination key1 [key2]](https://www.runoob.com/redis/sets-sdiffstore.html)返回给定所有集合的差集并存储在 destination 中 |
| 5 | [SINTER key1 [key2]](https://www.runoob.com/redis/sets-sinter.html)返回给定所有集合的交集 |
| 6 | [SINTERSTORE destination key1 [key2]](https://www.runoob.com/redis/sets-sinterstore.html)返回给定所有集合的交集并存储在 destination 中 |
| 7 | [SISMEMBER key member](https://www.runoob.com/redis/sets-sismember.html)判断 member 元素是否是集合 key 的成员 |
| 8 | [SMEMBERS key](https://www.runoob.com/redis/sets-smembers.html)返回集合中的所有成员 |
| 9 | [SMOVE source destination member](https://www.runoob.com/redis/sets-smove.html)将 member 元素从 source 集合移动到 destination 集合 |
| 10 | [SPOP key](https://www.runoob.com/redis/sets-spop.html)移除并返回集合中的一个随机元素 |
| 11 | [SRANDMEMBER key [count]](https://www.runoob.com/redis/sets-srandmember.html)返回集合中一个或多个随机数 |
| 12 | [SREM key member1 [member2]](https://www.runoob.com/redis/sets-srem.html)移除集合中一个或多个成员 |
| 13 | [SUNION key1 [key2]](https://www.runoob.com/redis/sets-sunion.html)返回所有给定集合的并集 |
| 14 | [SUNIONSTORE destination key1 [key2]](https://www.runoob.com/redis/sets-sunionstore.html)所有给定集合的并集存储在 destination 集合中 |
| 15 | [SSCAN key cursor [MATCH pattern] [COUNT count]](https://www.runoob.com/redis/sets-sscan.html)迭代集合中的元素 |

