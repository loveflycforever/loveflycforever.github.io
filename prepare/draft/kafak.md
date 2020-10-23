Kafka 相关概念：

Broker：Kafka 集群中包含的服务器。
Producer：消息生产者。
Consumer：消息消费者。
Consumer Group：每个 Consumer 都属于一个 Consumer Group，每条消息只能被 Consumer Group 中的一个 Consumer 消费，但可以被多个 Consumer Group 消费。
Topic：消息的类别。每条消息都属于某个 Topic，不同的 Topic 之间是相互独立的，即 Kafka 是面向 Topic 的。
Partition：每个 Topic 分为多个 Partition，Partition 是 Kafka 分配的单位。Kafka 物理上的概念，相当于一个目录，目录下的日志文件构成这个 Partition。
Replica：Partition 的副本，保障 Partition 的高可用。
Leader：Replica 中的一个角色， Producer 和 Consumer 只跟 Leader 交互。
Follower：Replica 中的一个角色，从 Leader 中复制数据。
Controller：Kafka 集群中的其中一个服务器，用来进行 Leader Election 以及各种 Failover。
Zookeeper：Kafka 通过 Zookeeper 来存储集群的 Meta 信息。