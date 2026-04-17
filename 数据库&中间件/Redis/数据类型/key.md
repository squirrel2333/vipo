Redis 键命令用于管理 redis 的键。

`redis 127.0.0.1:6379> COMMAND KEY\_NAME`

```
# Create/Delete
redis 127.0.0.1:6379> SET runoobkey redis
OK
redis 127.0.0.1:6379> DEL runoobkey
(integer) 1

# Exits
127.0.0.1:6379> exists key1
(integer) 1
127.0.0.1:6379> keys *
1) "key1"

# Type
127.0.0.1:6379> type key1
string
127.0.0.1:6379> lpush list 1 2 3
(integer) 3
127.0.0.1:6379> type list
list

# 非阻塞删除，删除操作在后续异步中
127.0.0.1:6379> unlink key1
(integer) 1

# 设置/查看key过期时间 -1表示永不过期，-2表示已经过期
127.0.0.1:6379> expire list 10000
(integer) 1
127.0.0.1:6379> ttl list
(integer) 9995

# 默认为0号库[0-15]，可以在配置文件中设置默认和库的范围
127.0.0.1:6379> keys *
1) "list"
# 将key移动到库
127.0.0.1:6379> move list 2
(integer) 1
127.0.0.1:6379> keys *
(empty array)
# 切换到库
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]> keys *
1) "list"

# 库中key数量
127.0.0.1:6379[2]> dbsize
(integer) 1

# 清空当前库
flushdb

# 通杀全部库
flushall
```
# Redis kesys命令

下表给出了与 Redis 键相关的基本命令：

|  |  |
| --- | --- |
| 序号 | 命令及描述 |
| 1 | [DEL key](https://www.runoob.com/redis/keys-del.html)该命令用于在 key 存在时删除 key。 |
| 2 | [DUMP key](https://www.runoob.com/redis/keys-dump.html)序列化给定 key ，并返回被序列化的值。 |
| 3 | [EXISTS key](https://www.runoob.com/redis/keys-exists.html)检查给定 key 是否存在。 |
| 4 | [EXPIRE key](https://www.runoob.com/redis/keys-expire.html) seconds为给定 key 设置过期时间，以秒计。 |
| 5 | [EXPIREAT key timestamp](https://www.runoob.com/redis/keys-expireat.html)EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。 |
| 6 | [PEXPIRE key milliseconds](https://www.runoob.com/redis/keys-pexpire.html)设置 key 的过期时间以毫秒计。 |
| 7 | [PEXPIREAT key milliseconds-timestamp](https://www.runoob.com/redis/keys-pexpireat.html)设置 key 过期时间的时间戳(unix timestamp) 以毫秒计 |
| 8 | [KEYS pattern](https://www.runoob.com/redis/keys-keys.html)查找所有符合给定模式( pattern)的 key 。 |
| 9 | [MOVE key db](https://www.runoob.com/redis/keys-move.html)将当前数据库的 key 移动到给定的数据库 db 当中。 |
| 10 | [PERSIST key](https://www.runoob.com/redis/keys-persist.html)移除 key 的过期时间，key 将持久保持。 |
| 11 | [PTTL key](https://www.runoob.com/redis/keys-pttl.html)以毫秒为单位返回 key 的剩余的过期时间。 |
| 12 | [TTL key](https://www.runoob.com/redis/keys-ttl.html)以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。 |
| 13 | [RANDOMKEY](https://www.runoob.com/redis/keys-randomkey.html)从当前数据库中随机返回一个 key 。 |
| 14 | [RENAME key newkey](https://www.runoob.com/redis/keys-rename.html)修改 key 的名称 |
| 15 | [RENAMENX key newkey](https://www.runoob.com/redis/keys-renamenx.html)仅当 newkey 不存在时，将 key 改名为 newkey 。 |
| 16 | [SCAN cursor [MATCH pattern] [COUNT count]](https://www.runoob.com/redis/keys-scan.html)迭代数据库中的数据库键。 |
| 17 | [TYPE key](https://www.runoob.com/redis/keys-type.html)返回 key 所储存的值的类型。 |

