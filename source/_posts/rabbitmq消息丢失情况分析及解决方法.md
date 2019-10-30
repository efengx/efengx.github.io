---
title: rabbitmq消息丢失情况分析及解决方法
date: 2019-10-17 14:07:22
tags: [rabbitmq,'高可用']
description: "这是是详情内容"
---

消息中间件是软件设计中非常好用的一个组件，如何确保消息中间件的可靠性一直是一个很大的问题。
<!-- more -->

## 消息丢失场景

![rabbitmq2](rabbitmq2.png)  
以上图片为rabbitmq消息推送的流程图，通过该图我们可以判断出消息有以下几种可能会存在丢失场景：

## 1. 生产者生产消息到RabbitMQ Server消息丢失

- 外界环境问题导致：发生网络丢包、网络故障等造成RabbitMQ Server端收不到消息，因为生产环境的网络是很复杂的，网络抖动，丢包现象很常见。
- 代码层面、配置层面、考虑不全导致消息丢失。  
  场景1: 生产者使用Confirm模式投递消息，RabbitMQ Server接收失败后会发送nack消息通知生产者，如果此时生产者监听消息失败或没做任何事情，该消息将丢失。  
  场景2: 生产者发送消息到exchange后，发送的路由和queue没有绑定，此时消息将会丢失。

解决方法：发送方确认机制（publisher confirm）
生产者通过调用channel.confirmSelect方法将信道设置为confirm模式，一旦信道进入confirm模式，所有该信道上面发布的消息都会被指派一个唯一的ID(从1开始)，一旦消息被投递道所有匹配的队列之后，RabbitMQ就会发送一个确认（Basic.Ack）给生产者（包含消息的唯一deliveryTag和multiple参数），这就使得生产者知晓消息已经正确到达了目的地了。

confirm模式的三种实现方式：

```java
// 1. 串行confirm模式：producer每发送一条消息后，调用waitForConfirms()方法，等待broker端confirm，如果服务器端返回false或者超时时间内未返回，客户端进行消息重传。

for (int i = 0; i < 50; i++) {
    channel.basicPublish(
        exchange, routingKey,
        mandatory, immediate,
        messageProperties,
        message.getContent()
    );
    if (channel.waitForConfirms) {
        System.out.println("发送成功");
    } else {
        // 在这里进行重新发送
        System.out.println("发送失败");
    }
}


// 2. 批量confirm模式：producer每发送一批消息后，调用waitForConfirms()方法，等待broker端confirm
for (int i = 0; i < 50; i++>) {
    channel.basicPublish(
        exchange, routingKey,
        mandatory, immediate,
        messageProperties,
        message.getContent()
    );
}
if (channel.waitForConfirms()) {
    System.out.println("发送成功")
} else {
    System.out.println("发送失败")
}


// 3. 异步confirm模式：提供一个回调方法，broker confirm了一条或者多条消息后producer端会回调这个方法。
Channel channel = channelManager.getPublisherChannel(namespaceName);
// 监听类
ProxiedConfirmListener confirmListener = new ProxiedConfirmListener();
confirmListener.setChannelManager(channelManager);
confirmListener.setChannel(channel);
confirmListener.setNamespace(namespaceName);
confirmListener.addSuccessCallbacks(successCallbacks);
channel.addConfirmListener(confirmListener);
// 开启confirm模式
channel.confirmSelect();
AMQP.BasicProperties messageProperties = null;
if (message.getProperty() instanceof AMQP.BasicProperties) {
    messageProperties = (AMQP.BasicProperties)message.getProperty();
}
confirmListener.toConfirm(channel.getNextPublishSeqNo(), rawMsg);
for (int i = 0; i < 50; i++) {
    channel.basicPublish(
        exchange, routingKey,
        mandatory, immediate,
        messageProperties,
        message.getContent()
    );
}
// 异步模式需要监听类，监听server端的通知消息，异步的好处是性能回大幅提升（非柱塞式）
// ...略过继承类代码，以下是需要实现的接口：
package com.rabbitmq.client;
import java.io.IOException;

public interface ConfirmListener {
    // handleAck RabbitMQ消息接收成功后的实现方法
    // 例如：发送端投递消息前，需要把消息先存起来，比如用KV存储，接收到ack后删除
    void handleAck(long deliveryTag, boolean multiple) throws IOException;

    // handleNack RabbitMQ 消息接收失败的通知方法，用户可以在这里重新投递消息
    void handleNack(long deliveryTag, boolean multiple) throws IOException;
}
```

![confirm](confirm.png)  
图片说明：channel1连续发送1,2,3条消息到RabbitMQ-Server, RabbitMQ-Server通知返回一条通知，里面包含回传给生产者的确认消息中的deliveryTag包含了确认消息的序号，此处还有一个参数multiple=true,表示到这个序号之前的所有消息都已经得到了处理。这样就减少了客户端和服务器端的通信次数，提升了效率。
（备注：使用说明方式存储消息？在每秒几千甚至上万的消息投递出去，消息的ack速度必须要快。放在内存中有可能内存溢出。采用KV存储是最合适的方法，但是确保KV存储的高可用，又是一个比较复杂的问题。这个问题就不在本文讨论范围，后续分析以下使用radis或Mongodb时的性能表现和可靠性）

## 2. RabbitMQ Server中存在的消息丢失或可靠性不足

- 消息未完全持久化，当机器重启后，消息会全部丢失，甚至Queue也不见了。
- 单节点模式问题：如果某个节点挂了，消息就不能用了，业务可能瘫痪，只能等待。
- 普通集群模式：某个节点挂了，该节点上的消息不能用，有影响的业务瘫痪，只能等节点恢复重启可用（建立在消息持久化）

RabbitMQ集群模式比较特殊，所有节点仅存放消息结构和元数据（可以理解为索引），具体的队列信息仅存在某一个节点上面。（这是为了提高性能，如果每次把所有内容同步到所有节点将产生额外的系统性能开销）。下图简单描述了这个过程：  
![rabbitmq](rabbitmq.jpg)  
解决该问题的方法：使用镜像模式(镜像模式比较复杂，后续单独发文说明)

## 3. RabbitMQ Server到消费者消息丢失

消费端接收到相关消息之后，消费端还没来得及处理消息，消费端机器就宕机了，此时消息如果处理不当会有丢失风险。（通过消费端ack机制预防）
