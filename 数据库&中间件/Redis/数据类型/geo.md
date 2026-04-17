
![](geo_001.png)

底层是zset类型

# geoadd

```
127.0.0.1:6379> geoadd city 116.403963 39.915119 "Tian an men" 116.403414 39.924091 "Gu Gong" 116.024067 40.362639 "Chang Cheng"
(integer) 3
127.0.0.1:6379> type city
zset
127.0.0.1:6379> zrange city 0 -1
1) "Tian an men"
2) "Gu Gong"
3) "Chang Cheng"
```
# geopos

```
127.0.0.1:6379> geopos city "Tian an men"
1) 1) "116.40396326780319214"
   2) "39.91511970338637383"
```
# geohash

```
127.0.0.1:6379> geohash city "Tian an men" "Gu Gong" "Chang Cheng"
1) "wx4g0f6f2v0"
2) "wx4g0gfqsj0"
3) "wx4t85y1kt0"
```
# geodist

```
127.0.0.1:6379> geodist city "Tian an men" "Chang Cheng"
"59338.9814"
127.0.0.1:6379> geodist city "Tian an men" "Chang Cheng" m
"59338.9814"
127.0.0.1:6379> geodist city "Tian an men" "Chang Cheng" km
"59.3390"
```
# georadius

以给定的经纬度为中心，返回键包含位置元素中，与中心距离不超过给定最大距离的所有位置元素

* withcoord: 返回元素的同时，将距离一起返回
* withcoord：经纬度一起返回
* withhash：52为有符号整数，表示元素经过原始geohash编码的有序集合分值
* count：记录数
* desc/asc: 降序/升序

```
127.0.0.1:6379> georadius city 116.418017 39.914402 10 km withcoord withdist withhash count 10 desc
1) 1) "Tian an men"
   2) "1.2016"
   3) (integer) 4069885555089531
   4) 1) "116.40396326780319214"
      2) "39.91511970338637383"
2) 1) "Gu Gong"
   2) "1.6470"
   3) (integer) 4069885568908290
   4) 1) "116.40341609716415405"
      2) "39.92409008156928252"
```
# georadiusbymember

```
127.0.0.1:6379> georadiusbymember city "Gu Gong" 10 km
1) "Tian an men"
2) "Gu Gong"
```
