
去重复统计功能的基数（去掉重复的个数）估计算法

![](hyperloglog_001.png)

存在误差

底层是string类型

# pfadd/pfcount/pfmerge/

```
127.0.0.1:6379> pfadd hll1 1 3 5 7 9
(integer) 1
127.0.0.1:6379> pfadd hll2 1 2 4 4 4 5 9 10
(integer) 1
127.0.0.1:6379> pfcount hll1
(integer) 5
127.0.0.1:6379> pfcount hll2
(integer) 6
127.0.0.1:6379> pfmerge hllresult hll1 hll2
OK
127.0.0.1:6379> pfcount hllresult
(integer) 8
```
# 应用场景

![](hyperloglog_002.png)

