---
title: 搭建最小springcloud
date: 2019-07-30 17:17:13
tags: [springcloud, simple]
---

快速搭建springcloud三件客
<!-- more -->

## erueka

- 点击 [https://start.spring.io/](https://start.spring.io/)  
   1. 项目配置
![springcloud](springcloud.jpg)
   2. eureka 项目目录结构
![eureka](eureka-project-structure.jpg)
   3. eureka 控制台
![springcloudeureka](springcloudeureka.png)

- EurekaApplication.java

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {

  public static void main(String[] args) {
    SpringApplication.run(EurekaApplication.class, args);
  }

}
```

- application.yml 配置

```yml
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

- 测试springcloud eureka是否安装成功
访问：[http://localhost:8761/](http://localhost:8761/)

## getway

- 创建 gateway 项目
![gateway](gateway.jpg)
![gateway-project](gateway-project-structure.jpg)

- GatewayApplication.java

```java
@SpringBootApplication
public class GatewayApplication {

  public static void main(String[] args)
  {
    SpringApplication.run(GatewayApplication.class, args);
  }

}
```

- application.yml 配置

```yml
server:
  port: 8080
spring:
  application:
    name: gateway
  cloud:
    gateway:
      routes:
      - id: route_one
#        uri: lb://fsh-house
        uri: https://start.spring.io/
        predicates:
        - Path=/spring-cloud
        filters:
        - name: Retry
          args:
            retries: 3
            statuses: BAD_GATEWAY
# 配置服务发现
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

## config

- 创建一个 config 项目
![config](config.jpg)
![config-structure](config-structure.jpg)

- application.yml 配置

```yml
# gitlab
server:
  port: 8888
spring:
  application:
    name: config
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/ofengx/config.git

```

## boot

- 创建一个简单 boot 项目
![boot](boot.jpg)
![boot-structure](boot-structure.jpg)

## 验证

- 项目启动顺序
>
> eureka -> config -> boot
> 验证：访问 [http://localhost:8083](http://localhost:8083) 打开 HAL Browser
>
