# 安装部署

1. [链接](https://kafka.apache.org)
2. 配置信息：kafka/config/server.properties
1. broker.id broker的全局编号
2. delete.topic.enable topic删除功能
3. listeners 端口信息
4. log.dir kafka运行日志存放路径
5. log.retention.hours log保存时间
6. zookeeper.connect zookeeper集群地址
3. 启动zookeeper
4. 单节点启动kafka
1. 去每个环境中启动
2. bin/kafka-server-start.sh config/server.properties 阻塞进程
3. bin/kafka-server-start.sh -daemon config/server.properties 守护进程
4. 群起脚本

```
#!/bin/bash

case $1 in
"start"){
	for i in hadoop102 hadoop103 hadoop104
  do
  	echo "******$i*******"
  	ssh $i "/opt/module/kafka/bin/kafka-server-start.sh -damon /opt/module/kafka/conig/server.properties"
  done
};;

"stop"){
	for i in hadoop102 hadoop103 hadoop104
  do
  		echo ""
      ssh $i "/opt/module/kafka/bin/kafka-server-stopt.sh"
  done
}};;
esac
```
# Kafka命令行

示例中2181是zookeeper端口 9092是kafka端口 hadoop是服务器名

1. **查看当前服务器所有topic**

`$ bin/kafka-topics.sh --zookeeper hadoop102.2181 --list`

zookeeper中存放着元数据信息，topic数据都存放在这里

2. **创建topic**

`$ bin/kafka-topics.sh --zookeeper hadoop102.2181 --create --replication-factor 2 --partitions 2 --topic firstTopic`

会在每个服务器中的logs/下创建副本，pod-0中firstTopic-0, pod-1中firstTopic-1,pod-2中firstTopic-0和firstTopic-1 每个分区号总共有两个副本

3. **删除数据**

`$ bin/kafka-topics.sh --delete --zookeeper hadoop102.2181 --topic firstTopic`

4. **主题详情**

`$ bin/kafka-topics.sh --describe --topic firstTopic --zookeeper hadoop102:2181`

5. **启动生产者**

`$ bin/kafka-console-producer.sh --topic firstTopic --broker-list hadoop102:9092`

6. **启动消费者**

`$ bin/kafka-console-consumer.sh --topic fitstTopic --zookeeper hadoop102:2181` 会提示过时

发送消息后，再让消费者上线，此时要拿到之前的消息加上`--from-beginning`

`$ bin/kafka-console-consumer.sh --topic fitstTopic --bootstrap-server hadoop102:9092`

会在/logs中创建offset的 系统topic 以记录偏移量（默认50个partition，在每个节点中轮询的创建）

