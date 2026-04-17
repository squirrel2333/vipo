# 依赖

```
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>4.3.1</version>
        </dependency>
```
# 连接

```
        Jedis jedis = new Jedis("81.68.97.243", 6379);
        jedis.auth("Hqw3.1415926");
        System.out.println(jedis.ping());
```
# 操作

```
        jedis.set("k1", "jedis");
        jedis.get("k1");
```
