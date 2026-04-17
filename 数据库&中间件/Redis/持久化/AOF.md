
* AOF（append only file）
* 存储文件：appendonly.aof
* 以日志的形式来记录每个操作，将Redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，重启就根据日志文件内容将指令从前到后执行一次以完成数据恢复工作
* 默认情况，redis没有开启AOF，开启需要设置配置：appendonly yes

# 工作流程和写回策略

![](AOF_001.png)

写回策略配置项：appendfsync

## Always

同步写回，每个写命令执行完立刻同步的将日志写回磁盘

## everysec（default）

命令执行完先写到缓冲区，每秒写回磁盘一次

## no

操作系统控制写回，每个写命令执行完，只是先把日志写到AOF文件的内存缓冲区，由操作系统决定何时将缓冲区内容写回磁盘

## 总结

![](AOF_002.png)

# 配置文件

## 保存路径

redis6 配置了 dir /myredis， 则存在 /myredis/dump.rdb /myredis/appendonly.aof

redis7 配置 dir/myredis, 则 /myredis/dump.rdb /myredis/[配置项appenddirname]/[配置项appendfilename]

## 保存名称

redis6只有一个

redis7存在三个：

* appendonly.aof.1.base.rdb :子进程通过重写产生，只有一个
* appendonly.aof.1.incr.aof : 在aofrw开始执行时创建，可能存在多个
* appendonly.aof.manifest: 历史aof由incr 和 base变化而来，每次aofrw成功完成时，本次之前的base和incr都变为history，history类型的aof会被自动删除。为了管理这些aof，引入manifest文件来跟踪管理aof。

# 正常恢复

数据会在redis server重启时恢复，aof开启会使用aof文件恢复（即使dump文件也存在）。有新的写入操作时，会改变incr文件（文件内增加，或重写生成2.incr文件）

# 异常恢复

everysec写回策略时，高并发情况一秒内宕机，造成aof文件错误，redis无法重启。

![](AOF_003.png)

`redis-check-aof --fix /myredis/appendonlydir/appendonly.aof.1.incr.aof`

# 重写机制

由于不断将命令记录到AOF文件中，AOF文件会越来越大，文件越大占用空间越大，aof恢复时间越长

为此，redis新增重写机制，当文件大小超过峰值时，redis会创建重写子进程，自动启动aof文件内容压缩，只保留可以恢复数据的最小指令集

或者可以手动使用命令`bgrewriteaof`触发

![](AOF_004.png)表示aof文件大小是上次rewrite后大小的一倍，且文件大于64M时触发

set k1 v1; set k2 v2 ..... => set k1 v1 k2 v2...

当文件达到64mb时，会将incr命令进行压缩，放到2.base里，2.incr变为空的

![](AOF_005.png)

如果使用手动触发，则不用到达上限，直接重写

# 优势

1. AOF更加持久，可以有不同的同步策略。默认每秒写入策略性能也很好。使用后台线程执行，没有fsync时，主线程全力写入，即使失败也只会丢失一秒写入
2. aof日志是仅附加日志，不会出现寻道问题，也不会出现断电损坏。即使日志写了一半的命令结尾，check-aof工具也能恢复
3. 当aof变得太大时，redis会在后台自动重写aof。重写完全安全，因为当redis继续附件到旧文件时，会使用创建当前数据集所需要的最少操作集生成一个全新的文件，一旦第二个文件准备就绪，redis就会切换两者并开始附加到最新的一个
4. aof易于理解和解析操作日志。例如不小心使用flushall刷新了内容，只要在此期间没有执行日志重写，依然可以通过停止服务器、删除最新的命令并重启redis来恢复数据

# 缺点

1. aof文件通常比相同数据集的等效RDB文件大
2. 理论上比RDB慢
