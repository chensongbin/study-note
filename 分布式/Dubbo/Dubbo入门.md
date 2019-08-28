# 环境搭建：

一、注册中心: zookeeper

二、监控：<https://github.com/apache/dubbo-admin>

java -jar dubbo-admin-0.1.jar



# demo:

场景：订单服务、用户服务

订单服务调用用户服务拿到所有收获地址



实现步骤：

1. 分模块：

（1）公共模块

（2）订单模块

（3）用户模块

2. 将服务提供者注册到注册中心
   - 引入dubbo依赖
   - 引入操作zookeeper的客户端
3. 让服务消费者去注册中心订阅服务提供





# SpringBoot 整合Dubbo

<https://github.com/apache/dubbo-spring-boot-project>

1. 导入dubbo-starter，在application.properties配置属性，使用@Service暴露服务，使用@Refrence

2. 保留xml文件，导入dubbo-starter，使用@ImportResource导入dubbo的配置文件

    





# 配置文件

一、优先级

从高到低：

- 虚拟机参数
- application.properties
- dubb.properties



二、常见配置

- check 默认为true，消费者启动时检查提供者是否可以正常调用

- timeout 远程调用超时时间，超时返回

- retries 远程调用失败，重试次数（不包括第一次调用）

  > 重试次数配置策略：
  >
  > 1）对于幂等调用设置重试次数（查询）
  >
  > 2）非幂等的不设置重试次数（新增）



三、注意

@EnableDubbo 与 





# 高可用

zookeeper宕机之后，服务还可以用，因为消费者还缓存者服务提供方的地址



服务降级：

某个服务器跑了很多服务，为了保证核心服务的正常运作，牺牲非必要的服务

设置返回策略：

（1）直接返回空 （2）容错，调用失败返回空

这样就能减少非必要服务对服务器资源的占用





# 集群容错

**集群模式：**

- Failover Cluster（默认配置）

  调用失败时重试其他服务器，通常用于读操作

- Failfast Cluster

  快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

- Failsafe Cluster

  失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

- Failback Cluster

  失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

- Forking Cluster

  并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 `forks="2"` 来设置最大并行数。

- Broadcast Cluster

  广播调用所有提供者，逐个调用，任意一台报错则报错 ，通常用于通知所有提供者更新缓存或日志等本地资源信息。

**配置：**

```xml
<dubbo:service cluster="failsafe" />
或者
<dubbo:reference cluster="failsafe" />
```



# 负载均衡

Dubbo 提供了4种负载均衡实现：

基于权重随机算法的 Random

基于最少活跃调用数算法的 LeastActive

基于 hash 一致性的 ConsistentHash

基于加权轮询算法的 RoundRobin 

```xml
<dubbo:service interface="..." loadbalance="roundrobin" />
或者
<dubbo:reference interface="..." loadbalance="roundrobin" />
或者
<dubbo:service interface="...">
    <dubbo:method name="..." loadbalance="roundrobin"/>
</dubbo:service>
或者
<dubbo:reference interface="...">
    <dubbo:method name="..." loadbalance="roundrobin"/>
</dubbo:reference>
```





# 问题：

- Dubbo admin 中统计功能无法使用