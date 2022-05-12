---
title: 微服务学习指南
data: 2021-09-09
---




[视频链接](https://www.bilibili.com/video/BV18E411x7eT)

[资料](https://pan.baidu.com/s/1Kg7UUpO3wwALX6x28cWA7A#list/path=%2F%E5%B0%9A%E7%A1%85%E8%B0%B7Java%E5%AD%A6%E7%A7%91%E5%85%A8%E5%A5%97%E6%95%99%E7%A8%8B%EF%BC%88%E6%80%BB207.77GB%EF%BC%89%2F2.%E5%B0%9A%E7%A1%85%E8%B0%B7%E5%85%A8%E5%A5%97JAVA%E6%95%99%E7%A8%8B--%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A0%B8%E5%BF%83%EF%BC%8846.39GB%EF%BC%89%2F%E5%B0%9A%E7%A1%85%E8%B0%B7SpringCloud%EF%BC%88Alibaba%EF%BC%89&parentPath=%2F) 提取码：8op3

---

微服务：

+ 服务注册与发现：Eureka 的替代产品 Zookeeper、Consul、Nacos（推荐）
+ 服务调用：Ribbon 的替代产品 LoadBalancer；Feign 的替代产品 OpenFeign
+ 服务熔断
+ 负载均衡
+ 服务降级：Hystrix 的替代产品 Sentinel
+ 服务消息队列
+ 配置中心管理：Config 的替代产品 Nacos
+ 服务网关：Zuul 的替代产品 Gateway
+ 服务监控
+ 全链路追踪
+ 自动化构建部署
+ 服务定时任务调度操作

## Spring Cloud

Spring Cloud 是分布式微服务架构的一站式解决方案，是多种微服务架构落地技术的集合体，俗称微服务全家桶。

Spring Cloud 集成相关优质项目：

+ Spring Cloud Config：配置管理工具包，让你可以把配置放到远程服务器，集中化管理集群配置，目前支持本地存储、Git 以及 Subversion。
+ Spring Cloud Bus：事件、消息总线，用于在集群（例如，配置变化事件）中传播状态变化，可与 Spring Cloud Config 联合实现热部署。
+ Eureka：云端服务发现，一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。（过时）
+ [Nacos](https://nacos.io/zh-cn/)：支持基于 DNS 和基于 RPC 的服务发现（可以作为 Spring Cloud 的注册中心）、动态配置服务（可以做配置中心）、动态 DNS 服务。
+ Hystrix：熔断器，容错管理工具，旨在通过熔断机制控制服务和第三方库的节点，从而对延迟和故障提供更强大的容错能力。
+ Zuul：Zuul 是在云平台上提供动态路由，监控，弹性，安全等边缘服务的框架。Zuul 相当于是设备和 Netflix 流应用的 Web 网站后端所有请求的前门。
+ Archaius：配置管理 API，包含一系列配置管理 API，提供动态类型化属性、线程安全配置操作、轮询框架、回调机制等功能。
+ Consul：封装了Consul 操作，consul 是一个服务发现与配置工具，与 Docker 容器可以无缝集成。
+ Spring Cloud for Cloud Foundry：通过 Oauth2 协议绑定服务到 CloudFoundry，CloudFoundry 是 VMware 推出的开源 PaaS 云平台。
+ Spring Cloud Sleuth：日志收集工具包，封装了Dapper 和 log-based 追踪以及 Zipkin 和 HTrace 操作，为 SpringCloud 应用实现了一种分布式追踪解决方案。
+ Spring Cloud Data Flow：大数据操作工具，作为 Spring XD 的替代产品，它是一个混合计算模型，结合了流数据与批量数据的处理方式。
+ Spring Cloud Security：基于 spring security 的安全工具包，为你的应用程序添加安全控制。
+ Spring Cloud Zookeeper：操作 Zookeeper 的工具包，用于使用 zookeeper 方式的服务发现和配置管理。
+ Spring Cloud Stream：数据流操作开发包，封装了与 Redis、Rabbit、Kafka 等发送接收消息。
+ Spring Cloud CLI：基于 Spring Boot CLI，可以让你以命令行方式快速建立云组件。
+ Ribbon：提供云端负载均衡，有多种负载均衡策略可供选择，可配合服务发现和断路器使用。
+ Turbine：Turbine 是聚合服务器发送事件流数据的一个工具，用来监控集群下 hystrix 的 metrics 情况。
+ Feign：Feign 是一种声明式、模板化的 HTTP 客户端。
+ Spring Cloud Task：提供云端计划任务管理、任务调度。
+ Spring Cloud Connectors：便于云端应用程序在各种 PaaS 平台连接到后端，如：数据库和消息代理服务。
+ Spring Cloud Cluster：提供 Leadership 选举，如：Zookeeper，Redis，Hazelcast，Consul 等常见状态模式的抽象和实现。
+ Spring Cloud Starters：Spring Boot 式的启动项目，为 Spring Cloud 提供开箱即用的依赖管理。

## 版本选择

![SpringBoot与SpringCloud](https://pic.imgdb.cn/item/6133818144eaada739d722b1.jpg)

[官网地址](https://spring.io/projects/spring-cloud)

学习时使用的版本：

+ Spring Cloud：Hoxton.SR1
+ Spring Boot：2.2.2.RELEASE
+ Spring Cloud Alibaba：2.1.0.RELEASE
+ Java：Java 8
+ Maven：3.5+
+ MySQL：5.7+
