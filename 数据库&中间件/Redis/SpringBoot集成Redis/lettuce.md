Springboot2.0之后默认使用lettuce连接Redis服务器。因为Jedis客户端连接服务器时，每个线程都要拿自己创建的实例连接Redis，不仅开销大需要反复创建关闭Jedis连接，而且也是线程不安全的，一个线程通过Jedis实例更改Redis服务器数据之后会影响另一个线程

lettuce底层使用Netty，当有多个线程需要连接时，保证只创建一个Lettuce连接，所有线程共用这个连接，减少了lettece连接断开的开销，并且线程安全

# 依赖

```
<!--lettuce-->
<dependency>
  <groupId>io.lettuce</groupId>
  <artifactId>lettuce-core</artifactId>
  <version>6.2.1.RELEASE</version>
</dependency>
```
# 业务类

```
@Slf4j
public class LettuceDemo
{
    public static void main(String[] args)
    {
        //使用构建器 RedisURI.builder
        RedisURI uri = RedisURI.builder()
            .redis("192.168.111.181")
            .withPort(6379)
            .withAuthentication("default","111111")
            .build();
        //创建连接客户端
        RedisClient client = RedisClient.create(uri);
        StatefulRedisConnection conn = client.connect();
        //操作命令api
        RedisCommands<String,String> commands = conn.sync();

        //keys
        List<String> list = commands.keys("*");
        for(String s : list) {
            log.info("key:{}",s);
        }
        //String
        commands.set("k1","1111");
        String s1 = commands.get("k1");
        System.out.println("String s ==="+s1);

        //list
        commands.lpush("myList2", "v1","v2","v3");
        List<String> list2 = commands.lrange("myList2", 0, -1);
        for(String s : list2) {
            System.out.println("list ssss==="+s);
        }
        //set
        commands.sadd("mySet2", "v1","v2","v3");
        Set<String> set = commands.smembers("mySet2");
        for(String s : set) {
            System.out.println("set ssss==="+s);
        }
        //hash
        Map<String,String> map = new HashMap<>();
        map.put("k1","138xxxxxxxx");
        map.put("k2","atguigu");
        map.put("k3","zzyybs@126.com");

        commands.hmset("myHash2", map);
        Map<String,String> retMap = commands.hgetall("myHash2");
        for(String k : retMap.keySet()) {
            System.out.println("hash  k="+k+" , v=="+retMap.get(k));
        }

        //zset
        commands.zadd("myZset2", 100.0,"s1",110.0,"s2",90.0,"s3");
        List<String> list3 = commands.zrange("myZset2",0,10);
        for(String s : list3) {
            System.out.println("zset ssss==="+s);
        }

        //sort
        SortArgs sortArgs = new SortArgs();
        sortArgs.alpha();
        sortArgs.desc();

        List<String> list4 = commands.sort("myList2",sortArgs);
        for(String s : list4) {
            System.out.println("sort ssss==="+s);
        }

        //关闭
        conn.close();
        client.shutdown();
    }
}
```
