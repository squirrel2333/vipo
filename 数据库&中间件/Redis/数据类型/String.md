最大512MB

Redis 字符串数据类型的相关命令用于管理 redis 字符串值，基本语法如下：

# SET GET

```
redis 127.0.0.1:6379> SET runoobkey redis
OK
redis 127.0.0.1:6379> GET runoobkey
"redis"

# nx不存在的时候创建 或者setnx
127.0.0.1:6379> set k1 v1 nx
OK
127.0.0.1:6379> get k1
"v1"

# xx已存在的时候覆盖 或者setxx
127.0.0.1:6379> set k1 v1x xx
OK
127.0.0.1:6379> get k1
"v1x"

# 设置过期时间（秒）或者setex
127.0.0.1:6379> set k1 v1 ex 10
OK
127.0.0.1:6379> ttl k1
(integer) 5

# 设置过期时间（毫秒）
127.0.0.1:6379> set k1 v1 px 8000
OK
127.0.0.1:6379> ttl k1
(integer) 5

# 设置过期时间 （unix时间戳）
127.0.0.1:6379> set k1 v1 exat 1692757696
OK
127.0.0.1:6379> ttl k1
(integer) 33

# 第一次设置超时时间后，第二次set覆盖会使超时时间变为永不超时。
# 如果想要保持之前的超时时间
127.0.0.1:6379> set k1 v1 ex 30
OK
127.0.0.1:6379> ttl k1
(integer) 26
127.0.0.1:6379> set k1 v11 keepttl
OK
127.0.0.1:6379> ttl k1
(integer) 10
```
# MSET MGET

```
127.0.0.1:6379> mset k1 v1 k2 v2
OK
127.0.0.1:6379> mget k1 k2 k3
1) "v1"
2) "v2"
3) (nil)

# 不存在时设置，要么全部成功，要么全部失败
127.0.0.1:6379> msetnx k1 v1 k4 v4
(integer) 0
127.0.0.1:6379> mget k1 k4
1) "v1"
2) (nil)

# 获取范围
127.0.0.1:6379> set k4 abcdefghi
OK
127.0.0.1:6379> getrange k4 0 -1
"abcdefghi"
127.0.0.1:6379> getrange k4 0 6
"abcdefg"
# 覆盖范围 从下标1开始以字符串替换
127.0.0.1:6379> setrange k4 1 VVVV
(integer) 9
127.0.0.1:6379> get k4
"aVVVVfghi"
```
# 增减

```
# 增加 减少
127.0.0.1:6379> set k1 1
OK
127.0.0.1:6379> incr k1
(integer) 2
127.0.0.1:6379> incr k1
(integer) 3
127.0.0.1:6379> decr k1
(integer) 2
127.0.0.1:6379> incrby k1 5
(integer) 7
127.0.0.1:6379> decrby k1 5
(integer) 2
```
# 字符串长度和内容追加

```
127.0.0.1:6379> set k1 abcd
OK
127.0.0.1:6379> strlen k1
(integer) 4
127.0.0.1:6379> append k1 efg
(integer) 7
```
# getset

```
127.0.0.1:6379> getset k1 123
(nil)
127.0.0.1:6379> get k1
"123"
127.0.0.1:6379> set k1 321 get
"123"
127.0.0.1:6379> get k1
"321"
```
## Redis 字符串命令

下表列出了常用的 redis 字符串命令：

|  |  |
| --- | --- |
| 序号 | 命令及描述 |
| 1 | [SET key value](https://www.runoob.com/redis/strings-set.html)设置指定 key 的值。 |
| 2 | [GET key](https://www.runoob.com/redis/strings-get.html)获取指定 key 的值。 |
| 3 | [GETRANGE key start end](https://www.runoob.com/redis/strings-getrange.html)返回 key 中字符串值的子字符 |
| 4 | [GETSET key value](https://www.runoob.com/redis/strings-getset.html)将给定 key 的值设为 value ，并返回 key 的旧值(old value)。 |
| 5 | [GETBIT key offset](https://www.runoob.com/redis/strings-getbit.html)对 key 所储存的字符串值，获取指定偏移量上的位(bit)。 |
| 6 | [MGET key1 [key2..]](https://www.runoob.com/redis/strings-mget.html)获取所有(一个或多个)给定 key 的值。 |
| 7 | [SETBIT key offset value](https://www.runoob.com/redis/strings-setbit.html)对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。 |
| 8 | [SETEX key seconds value](https://www.runoob.com/redis/strings-setex.html)将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。 |
| 9 | [SETNX key value](https://www.runoob.com/redis/strings-setnx.html)只有在 key 不存在时设置 key 的值。 |
| 10 | [SETRANGE key offset value](https://www.runoob.com/redis/strings-setrange.html)用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。 |
| 11 | [STRLEN key](https://www.runoob.com/redis/strings-strlen.html)返回 key 所储存的字符串值的长度。 |
| 12 | [MSET key value [key value ...]](https://www.runoob.com/redis/strings-mset.html)同时设置一个或多个 key-value 对。 |
| 13 | [MSETNX key value [key value ...]](https://www.runoob.com/redis/strings-msetnx.html)同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。 |
| 14 | [PSETEX key milliseconds value](https://www.runoob.com/redis/strings-psetex.html)这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。 |
| 15 | [INCR key](https://www.runoob.com/redis/strings-incr.html)将 key 中储存的数字值增一。 |
| 16 | [INCRBY key increment](https://www.runoob.com/redis/strings-incrby.html)将 key 所储存的值加上给定的增量值（increment） 。 |
| 17 | [INCRBYFLOAT key increment](https://www.runoob.com/redis/strings-incrbyfloat.html)将 key 所储存的值加上给定的浮点增量值（increment） 。 |
| 18 | [DECR key](https://www.runoob.com/redis/strings-decr.html)将 key 中储存的数字值减一。 |
| 19 | [DECRBY key decrement](https://www.runoob.com/redis/strings-decrby.html)key 所储存的值减去给定的减量值（decrement） 。 |
| 20 | [APPEND key value](https://www.runoob.com/redis/strings-append.html)如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾。 |

