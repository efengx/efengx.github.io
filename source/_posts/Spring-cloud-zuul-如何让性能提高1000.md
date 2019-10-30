---
title: Spring cloud zuul 如何让性能提高1000%
date: 2019-03-13 16:10:25
tags: ['spring cloud', zul]
---

zuul作为springcloud中的核心路由组件，如何更大的压榨性能一直是一个非常关键的问题。
<!-- more -->

## zuul 参数调优

### spring-boot-tomcat 参数优化

```yaml
server:
  tomcat:
    accept-count: 1000
    max-threads: 1000
    max-connections: 2000
```
