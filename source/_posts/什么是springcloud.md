---
title: 什么是springcloud
date: 2019-07-30 11:21:21
categories: MicroService
tags: ['spring cloud','SOA','微服务','架构']
---

通过整个架构的演变过程来理解微服务架构springcloud
<!-- more -->

## 架构发展历程

>
> 单体架构 -> SOA架构 -> 微服务架构
>
### 单体架构

![monolithicApplication](monolithicApplication.jpg)

- 这种将所有功能都部署在一个web容器中运行的系统就叫做单体架构（也叫：巨石型应用）。

### SOA架构（面向服务的体系结构）

![soa](soa.gif)

- 面向服务的体系结构不是关于如何模块化应用程序，而是关于如何通过集成分布式，单独维护和部署的软件组件来组合应用程序。它通过技术和标准实现，使组件更容易通过网络进行通信和协作，尤其是IP网络。

### 微服务架构(spring cloud)

![microservices](microservices.jpg)

- 微服务的好处有：服务独立、扩展性好、可靠性强，但同时，也面临一些新的问题，比如运维复杂性，分布式复杂性、监控复杂性等等

### 微服务与SOA区别

![soa-vs-microservices](soa-vs-microservices.png)

---

## spring cloud

### 定位

![modern](modern.jpg)

- 快速开始和快速启动
- 协调任何事情
- 快速建立连接
![modern2](modern2.jpg)
- 协调任何事情：简化的分布式系统

### 核心组件

#### SpringCloudNetflix

- Eureka
- eureka主要组件：Eureka系统本身由一个写和读集群组成。写集群是一个有状态子系统，它处理客户端注册并维护内部服务注册表。注册表内容以最终一致的方式在所有写入服务器节点之间复制。写集群的注册表内容由读集群读取，最终由Eureka客户端使用。由于读取群集实际上是缓存层，因此可以根据流量的大小轻松地快速扩展和缩小。写集群应该预先调整，其容量足以处理偷看/繁忙的小时流量。虽然可以动态扩展和缩小，但需要进行更多协调。向上扩展将需要流量重新平衡，这最终会发生，但不会立即发生。向下缩小将强制关闭节点的客户端重新注册。
![eureka1](eureka1.png)
- eureka客户端：通过注册，心跳和更新来注册自己。注册包括可发现的标识符和服务状态，以及可选的自由格式元数据。负责处理这些操作的Eureka 2.0服务器构成写集群。
![eureka2](eureka2.png)
- 注册表发现：Eureka客户可以订阅Eureka服务器上的兴趣集。订阅成功后，服务器会将对订阅兴趣的所有更改推送到客户端。负责处理这些操作的Eureka服务器构成了读集群。
![eureka3](eureka3.png)
- 从CAP定理的角度来看，Eureka写集群是一个AP系统（高可用性和分区容错）。此选择由基于云的发现服务的主要要求驱动。在云中，特别是对于大型部署，故障始终发生。这可能是Eureka服务器本身，注册客户端或网络分区的失败。在所有这些情况下，Eureka仍然可以通过提供注册表信息并单独接受来自每个可用节点的新注册来获得。由于Eureka选择了可用性，因此这种情况下的数据在这些节点之间并不一致。由于此模型导致注册表数据始终存在某种程度的陈旧性，因此应通过适当的客户端负载平衡和故障转移机制进行补充。

- Hystrix
![hystrix](hystrix-command-flow-chart.png)

1. 构造一个HystrixCommand或HystrixObservableCommand对象
2. 执行命令
3. 缓存响应吗?
4. 电路是否打开？
5. 线程池/队列/信号量是否已满？
6. HystrixObservableCommand.construct()或HystrixCommand.run()
7. 计算电路健康状况
8. 获得后备
9. 返回成功的回复

- Zuul

#### SpringCloudGateway

![gateway](gateway.png)

- 路由：将此视为我们希望特定请求路由到的目的地。它包含目标URI，必须满足的条件 - 或者在技术术语方面，Predicates和一个或多个过滤器。
- 谓词：这实际上是一个匹配的条件。即一种“if”条件..如果请求有某些东西 - 例如path = blah或请求头包含foo-bar等。在技术术语中，它是Java 8 Function Predicate
- 过滤器：这些是Spring Framework WebFilter的实例。您可以在这里应用修改请求或响应的魔力。框架提供了很多开箱即用的WebFilter。但当然，我们正在谈论Spring Framework。所以，休息好人！您始终可以使用自己的逻辑添加自己的过滤器:)

#### SpringCloudConfig

![config](config1.png)
