

# Kafka持久化消息

对已提交的消息保证不丢失

什么是已提交的消息？

当Kafka的若干个Broker成功地接收到一条消息并写入到日志文件后，它们会告诉生产者程序这条消息已成功提交

那为什么是若干个Broker呢?

这取决于你对“已提交”的定义。你可以选择只要有一个Broker成功保存该消息就算是已提交，也可以是令所有Broker都成功保存该消息才算是已提交。



# 消息丢失case

## Producer丢失消息

发消息有两个API

1. 发送之后，没有callback

```java
producer.send(msg)
```

2. 需要注册callback，从callback判断是否提交成功

```java
producer.send(msg, callback)
```

*注意：对于第一种API，会出现消息丢失*

## Consumer丢失消息

### case1

消费时候，消费者会维护一个offset，表示消费到的位置

使用两种方式：

1. 先更新offset，再消费消息（会出现消息丢失）
2. 先消费消息，再更新offset（会出现重复消费）

### case2

消费者读取消息，使用多线程技术，异步处理消息，然后更新offset

此时如果某个线程执行失败，那么他负责的那条消息就丢失了

**solution：如果是多线程异步处理消费消息，Consumer程序不要开启自动提交位移，而是要应用程序手动提交位移**



# Best Practices

1. 不要使用producer.send(msg)，而要使用producer.send(msg, callback)。
2. 设置acks = all。acks是Producer的一个参数，代表了你对“已提交”消息的定义。如果设置成all，则表明所有副本Broker都要接收到消息，该消息才算是“已提交”。这是最高等级的“已提交”定义。 
3. 设置retries为一个较大的值。这里的retries同样是Producer的参数，对应前面提到的Producer自动重试。当出现网络的瞬时抖动时，消息发送可能会失败，此时配置了retries > 0的Producer能够自动重试消 息发送，避免消息丢失。 
4. 设置unclean.leader.election.enable = false。这是Broker端的参数，它控制的是哪些Broker有资格竞选分区的Leader。如果一个Broker落后原先的Leader太多，那么它一旦成为新的Leader，必然会造成消息 的丢失。故一般都要将该参数设置成false，即不允许这种情况的发生。 
5. 设置replication.factor >= 3。这也是Broker端的参数。其实这里想表述的是，最好将消息多保存几份，毕竟目前防止消息丢失的主要机制就是冗余。 
6. 设置min.insync.replicas > 1。这依然是Broker端参数，控制的是消息至少要被写入到多少个副本才算 是“已提交”。设置成大于1可以提升消息持久性。在实际环境中千万不要使用默认值1。 
7. 确保replication.factor > min.insync.replicas。如果两者相等，那么只要有一个副本挂机，整个分区就无法正常工作了。我们不仅要改善消息的持久性，防止数据丢失，还要在不降低可用性的基础上完成。推荐设置成replication.factor = min.insync.replicas + 1。 
8. 确保消息消费完成再提交。Consumer端有个参数enable.auto.commit，最好把它设置成false，并采用手动提交位移的方式。比如case2，这对于单Consumer多线程处理的场景而言是至关重要的。 