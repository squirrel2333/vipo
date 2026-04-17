* ES往往有最大负载能力，超过这个阈值，性能就会降低或不可用。因此一般都是部署在指定服务器集群中。
* ES集群有一个唯一的名字标识，这个名字默认为elasticsearch。一个节点只能通过指定某个集群的名字，来加入这个集群。
* 集群包含很多服务器，一个节点就是一个服务器。作为集群的一部分，它存储数据，参与集群的索引和搜索功能。

# 配置集群

## 单节点

1. 删除`/Users/hqw/Environment/elasticsearch-8.1.2`下的data目录，和logs目录中的文件
2. `config/elasticsearch.yml`
1. `cluster.name: my-application`
2. `node.name: node-1`
3. `network.host: 127.0.0.1`
4. `http.port: 9200`
5. `cluster.initial\_master\_nodes: ["node-1"]`
3. 将`elasticsearch-8.1.2`复制几份，并修改上面参数
4. check health `<http://localhost:9200/_cluster/health> GET`

查询所有索引 `http://localhost:9200/\_cat/indices?v`

## 多节点

查询节点状态`[http://localhost:9200/\_cat/](http://localhost:9200/_cat/indices?v)nodes`

配置信息：

```
# 加入如下配置

#集群名称

cluster.name: cluster-es

#节点名称，每个节点的名称不能重复

node.name: node-1

#ip 地址，每个节点的地址不能重复
network.host: linux1

#是不是有资格主节点
node.master: true

node.data: true

http.port: 9200

# head 插件需要这打开这两个配置

http.cors.allow-origin: "*"

http.cors.enabled: true

http.max_content_length: 200mb

#es7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举 master

cluster.initial_master_nodes: ["node-1"]

#es7.x 之后新增的配置，节点发现

discovery.seed_hosts: ["linux1:9300","linux2:9300","linux3:9300"]

gateway.recover_after_nodes: 2

network.tcp.keep_alive: true

network.tcp.no_delay: true

transport.tcp.compress: true

#集群内同时启动的数据任务个数，默认是 2 个

cluster.routing.allocation.cluster_concurrent_rebalance: 16

#添加或删除节点及负载均衡时并发恢复的线程个数，默认 4 个

cluster.routing.allocation.node_concurrent_recoveries: 16

#初始化数据恢复时，并发恢复线程的个数，默认 4 个

cluster.routing.allocation.node_initial_primaries_recoveries: 16
```
