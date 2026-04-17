Redis 命令用于在 redis 服务上执行操作。

要在 redis 服务上执行命令需要一个 redis 客户端。Redis 客户端在我们之前下载的的 redis 的安装包中。

```
$ redis-cli
redis 127.0.0.1:6379>
redis 127.0.0.1:6379> PING
```
# 在远程服务器上执行命令

 `redis-cli -h host -p port -a password`

```
$redis-cli -h 127.0.0.1 -p 6379 -a "mypass"
redis 127.0.0.1:6379>
redis 127.0.0.1:6379> PING
```
**有时候会有中文乱码。要在 redis-cli 后面加上 --raw**

# 命令大小写和类型

命令不区分大小写，key区分大小写

帮助命令：`help @类型`

`help @string`

