分为两种类型，生产者拦截器，消费者拦截器





# 生产者拦截器

生产者拦截器允许你在发送消息前以及消息提交成功后植入你的拦截器逻辑;



1. 实现接口

```java
org.apache.kafka.clients.producer.ProducerInterceptor{
  // 该方法会在消息发送之前被调用。如果你想在发送之前对消息“美美容”，这个方法是你唯一 的机会。
  onSend()
	
    //该方法会在消息成功提交或发送失败之后被调用。onAcknowledgement的调用要早于callback的调用。值得注意的是，这个方法和 onSend不是在同一个线程中被调用的，因此如果你在这两个方法中调用了某个共享可变对象，一定要保 证线程安全。还有一点很重要，这个方法处在Producer发送的主路径中，所以最好别放一些太重的逻辑进去，否则你会发现你的Producer TPS直线下降。
    onAcknowledgement()
}

```



2. 注册自定义拦截器

```java
Properties props = new Properties();
List<String> interceptors = new ArrayList<>(); interceptors.add("com.yourcompany.kafkaproject.interceptors.AddTimestampInterceptor"); // 拦截器1 
interceptors.add("com.yourcompany.kafkaproject.interceptors.UpdateCounterInterceptor"); // 拦截器2 
props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG, interceptors);
```



# 消费者拦截器

消费者拦截器支持在消费消息前以及提交位移后编写特定逻辑



实现org.apache.kafka.clients.consumer.ConsumerInterceptor接口，这里面也有两个核心方法。 

1. onConsume:该方法在消息返回给Consumer程序之前调用。也就是说在开始正式处理消息之前，拦截器会先拦一道，搞一些事情，之后再返回给你。 

2. onCommit:Consumer在提交位移之后调用该方法。通常你可以在该方法中做一些记账类的动作，比如打日志等。 