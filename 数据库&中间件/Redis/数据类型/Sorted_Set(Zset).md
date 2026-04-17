
Redis 有序集合和集合一样也是 string 类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。

有序集合的成员是唯一的,但分数(score)却可以重复。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。 集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。

# zadd/zrange

```

127.0.0.1:6379> zadd zset1 60 v1 70 v2 80 v3 90 v4 100 v5
(integer) 5

```
# zrange/zrevrange

```

# 从小到大
127.0.0.1:6379> zrange zset1 0 -1
1) "v1"
2) "v2"
3) "v3"
4) "v4"
5) "v5"

127.0.0.1:6379> zrange zset1 0 -1 withscores
 1) "v1"
 2) "60"
 3) "v2"
 4) "70"
 5) "v3"
 6) "80"
 7) "v4"
 8) "90"
 9) "v5"
10) "100"

# 反转 从大到小
127.0.0.1:6379> zrevrange zset1 0 -1
1) "v5"
2) "v4"
3) "v3"
4) "v2"
5) "v1"
```
# zrangebyscore

```
# 按照分数筛选 [60,90]
127.0.0.1:6379> zrangebyscore zset1 60 90
1) "v1"
2) "v2"
3) "v3"
4) "v4"
127.0.0.1:6379> zrangebyscore zset1 60 90 withscores
1) "v1"
2) "60"
3) "v2"
4) "70"
5) "v3"
6) "80"
7) "v4"
8) "90"

# (60,90]
127.0.0.1:6379> zrangebyscore zset1 (60 90
1) "v2"
2) "v3"
3) "v4"
# 没有右括号
127.0.0.1:6379> zrangebyscore zset1 60 90)
(error) ERR min or max is not a float
# [60, 90)
127.0.0.1:6379> zrangebyscore zset1 60 (90
1) "v1"
2) "v2"
3) "v3"

# 从下标为1开始，显示三个
127.0.0.1:6379> zrangebyscore zset1 60 100 limit 1 3
1) "v2"
2) "v3"
3) "v4"
```
# zscore

```
127.0.0.1:6379> zscore zset1 v1
"60"
```
# zcard

```
127.0.0.1:6379> zcard zset1
(integer) 5
```
# zrem

```
127.0.0.1:6379> zrem zset1 v5
(integer) 1
127.0.0.1:6379> zrange zset1 0 -1 withscores
1) "v1"
2) "60"
3) "v2"
4) "70"
5) "v3"
6) "80"
7) "v4"
8) "90"
```
# zincrby

```
127.0.0.1:6379> zincrby zset1 8 v1
"68"
```
# zcount

```
127.0.0.1:6379> zcount zset1 60 90
(integer) 4
```
# zmpop

```
# 从1个zset1中 弹出 最小的 1条数据
127.0.0.1:6379> zmpop 1 zset1 min count 1
1) "zset1"
2) 1) 1) "v1"
      2) "68"

# 从1个zset中弹出最大的两条数据
127.0.0.1:6379> zmpop 1 zset1 max count 2
1) "zset1"
2) 1) 1) "v4"
      2) "90"
   2) 1) "v3"
      2) "80"
```
# zrank/zrevrank

```
# 从小到大正序获取元素的下标
127.0.0.1:6379> zrank zset1 v1
(integer) 0
127.0.0.1:6379> zrank zset1 v2
(integer) 1
127.0.0.1:6379> zrank zset1 v3
(integer) 2

# 从大到小逆序获取
127.0.0.1:6379> zrevrank zset1 v3
(integer) 1
```
# 应用场景

商品销售排序

![](Sorted_Set(Zset)_001.png)

# Redis 有序集合命令

下表列出了 redis 有序集合的基本命令:

|  |  |
| --- | --- |
| 序号 | 命令及描述 |
| 1 | [ZADD key score1 member1 [score2 member2]](https://www.runoob.com/redis/sorted-sets-zadd.html)向有序集合添加一个或多个成员，或者更新已存在成员的分数 |
| 2 | [ZCARD key](https://www.runoob.com/redis/sorted-sets-zcard.html)获取有序集合的成员数 |
| 3 | [ZCOUNT key min max](https://www.runoob.com/redis/sorted-sets-zcount.html)计算在有序集合中指定区间分数的成员数 |
| 4 | [ZINCRBY key increment member](https://www.runoob.com/redis/sorted-sets-zincrby.html)有序集合中对指定成员的分数加上增量 increment |
| 5 | [ZINTERSTORE destination numkeys key [key ...]](https://www.runoob.com/redis/sorted-sets-zinterstore.html)计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 destination 中 |
| 6 | [ZLEXCOUNT key min max](https://www.runoob.com/redis/sorted-sets-zlexcount.html)在有序集合中计算指定字典区间内成员数量 |
| 7 | [ZRANGE key start stop [WITHSCORES]](https://www.runoob.com/redis/sorted-sets-zrange.html)通过索引区间返回有序集合指定区间内的成员 |
| 8 | [ZRANGEBYLEX key min max [LIMIT offset count]](https://www.runoob.com/redis/sorted-sets-zrangebylex.html)通过字典区间返回有序集合的成员 |
| 9 | [ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT]](https://www.runoob.com/redis/sorted-sets-zrangebyscore.html)通过分数返回有序集合指定区间内的成员 |
| 10 | [ZRANK key member](https://www.runoob.com/redis/sorted-sets-zrank.html)返回有序集合中指定成员的索引 |
| 11 | [ZREM key member [member ...]](https://www.runoob.com/redis/sorted-sets-zrem.html)移除有序集合中的一个或多个成员 |
| 12 | [ZREMRANGEBYLEX key min max](https://www.runoob.com/redis/sorted-sets-zremrangebylex.html)移除有序集合中给定的字典区间的所有成员 |
| 13 | [ZREMRANGEBYRANK key start stop](https://www.runoob.com/redis/sorted-sets-zremrangebyrank.html)移除有序集合中给定的排名区间的所有成员 |
| 14 | [ZREMRANGEBYSCORE key min max](https://www.runoob.com/redis/sorted-sets-zremrangebyscore.html)移除有序集合中给定的分数区间的所有成员 |
| 15 | [ZREVRANGE key start stop [WITHSCORES]](https://www.runoob.com/redis/sorted-sets-zrevrange.html)返回有序集中指定区间内的成员，通过索引，分数从高到低 |
| 16 | [ZREVRANGEBYSCORE key max min [WITHSCORES]](https://www.runoob.com/redis/sorted-sets-zrevrangebyscore.html)返回有序集中指定分数区间内的成员，分数从高到低排序 |
| 17 | [ZREVRANK key member](https://www.runoob.com/redis/sorted-sets-zrevrank.html)返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
| 18 | [ZSCORE key member](https://www.runoob.com/redis/sorted-sets-zscore.html)返回有序集中，成员的分数值 |
| 19 | [ZUNIONSTORE destination numkeys key [key ...]](https://www.runoob.com/redis/sorted-sets-zunionstore.html)计算给定的一个或多个有序集的并集，并存储在新的 key 中 |
| 20 | [ZSCAN key cursor [MATCH pattern] [COUNT count]](https://www.runoob.com/redis/sorted-sets-zscan.html)迭代有序集合中的元素（包括元素成员和元素分值） |

