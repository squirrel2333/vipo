
由0 和1状态表现的二级制位的bit数组

![](bitmap_001.png)

底层是string类型

# setbit/getbit

偏移量从零开始

```
127.0.0.1:6379> setbit k1 1 1
(integer) 0
127.0.0.1:6379> setbit k1 0 1
(integer) 0
127.0.0.1:6379> setbit k1 2 1
(integer) 1
127.0.0.1:6379> getbit k1 1
(integer) 1
127.0.0.1:6379> getbit k1 0
(integer) 1
127.0.0.1:6379> getbit k1 2
(integer) 1
127.0.0.1:6379> setbit k1 3 2
(error) ERR bit is not an integer or out of range
```
# strlen/bitcount

```
# strlen表示字符串长度占用几个字节，超过8位后，按照8位一组再扩容一个字节
127.0.0.1:6379> setbit k1 0 1
(integer) 0
127.0.0.1:6379> strlen k1
(integer) 1
127.0.0.1:6379> setbit k1 7 1
(integer) 0
127.0.0.1:6379> strlen k1
(integer) 1
127.0.0.1:6379> setbit k1 8 1
(integer) 0
127.0.0.1:6379> strlen k1
(integer) 2

# 键里含有多少个1
127.0.0.1:6379> setbit uid:login123 1 1
(integer) 0
127.0.0.1:6379> setbit uid:login123 2 1
(integer) 0
127.0.0.1:6379> setbit uid:login123 3 1
(integer) 0
127.0.0.1:6379> bitcount uid:login123
(integer) 3
```
# bitop

```
# 用户id和用户名的映射
127.0.0.1:6379> hset uid:map 0 uid-asdwqxcoa
(integer) 0
127.0.0.1:6379> hset uid:map 1 uid-tsassljol
(integer) 1
127.0.0.1:6379> hgetall uid:map
1) "0"
2) "uid-asdwqxcoa"
3) "1"
4) "uid-tsassljol"

# 1号表，统计各个用户签到情况
127.0.0.1:6379> setbit 20230101 0 1
(integer) 0
127.0.0.1:6379> setbit 20230101 1 1
(integer) 0
127.0.0.1:6379> setbit 20230101 2 1
(integer) 0
127.0.0.1:6379> setbit 20230101 3 1
(integer) 0

# 1月2号表，只有0 和 3 两个用户签到
127.0.0.1:6379> setbit 20230102 0 1
(integer) 0
127.0.0.1:6379> setbit 20230102 3 1
(integer) 0

127.0.0.1:6379> bitcount 20230101
(integer) 4
127.0.0.1:6379> bitcount 20230102
(integer) 2

# 统计连续两天都签到的用户
127.0.0.1:6379> bitop and k3 20230101 20230102
(integer) 1
# 有多少个连续签到的
127.0.0.1:6379> bitcount k3
(integer) 2
# 具体是哪几个用户
127.0.0.1:6379> getbit k3 0
(integer) 1
127.0.0.1:6379> getbit k3 1
(integer) 0
127.0.0.1:6379> getbit k3 2
(integer) 0
127.0.0.1:6379> getbit k3 3
(integer) 1
```
# 统计一个月打卡的天数

```
127.0.0.1:6379> setbit sign:u1:202306 1 1
(integer) 0
127.0.0.1:6379> setbit sign:u1:202306 2 1
(integer) 0
127.0.0.1:6379> setbit sign:u1:202306 3 1
(integer) 0
127.0.0.1:6379> setbit sign:u1:202306 6 1
(integer) 0
127.0.0.1:6379> setbit sign:u1:202306 22 1
(integer) 0
127.0.0.1:6379> setbit sign:u1:202306 29 1
(integer) 0
127.0.0.1:6379> bitcount sign:u1:202306 0 30
(integer) 6
```
# 应用场景

![](bitmap_002.png)

比起mysql节省大量的空间和操作

