---
title: 微服务入门
create: 2021-09-09
data: 2021-09-09
---

## 认识微服务

### 单体架构与分布式架构

单体架构：将业务的所有功能集中在一个项目中开发，打成一个包部署。优点是架构简单、部署成本低，但耦合度高（维护困难、升级困难）

分布式架构：根据业务功能对系统做拆分，每个业务功能模块作为独立项目开发，称为一个服务。降低了服务耦合，有利于服务升级和拓展，但服务调用关系错综复杂

分布式架构虽然降低了服务耦合，但是服务拆分时也有很多问题需要思考：

- 服务拆分的粒度如何界定？
- 服务之间如何调用？
- 服务的调用关系如何管理？

人们需要制定一套行之有效的标准来约束分布式架构。

### 微服务

微服务的架构特征：

- 单一职责：微服务拆分粒度更小，每一个服务都对应唯一的业务能力，做到单一职责
- 自治：团队独立、技术独立、数据独立，独立部署和交付
- 面向服务：服务提供统一标准的接口，与语言和技术无关
- 隔离性强：服务调用做好隔离、容错、降级，避免出现级联问题

微服务的上述特性其实是在给分布式架构制定一个标准，进一步降低服务之间的耦合度，提供服务的独立性和灵活性。做到高内聚，低耦合。

因此，可以认为微服务是一种经过良好架构设计的分布式架构方案 。

但方案该怎么落地？选用什么样的技术栈？全球的互联网公司都在积极尝试自己的微服务落地方案。

其中在 Java 领域最引人注目的就是 Spring Cloud 提供的方案了。

### Spring Cloud

Spring Cloud 是目前国内使用最广泛的微服务框架。官网地址：https://spring.io/projects/spring-cloud。

Spring Cloud 集成了各种微服务功能组件，并基于 Spring Boot 实现了这些组件的自动装配，从而提供了良好的开箱即用体验。

其中常见的组件包括：

![SpringCloud组件](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/SpringCloud组件.1y5f5er0mdkw.svg)

另外，Spring Cloud 底层是依赖于 Spring Boot 的，并且有版本的兼容关系，如下：

![SpringBoot与SpringCloud](https://pic.imgdb.cn/item/6133818144eaada739d722b1.jpg)

### 认识微服务小结

- 单体架构：简单方便，高度耦合，扩展性差，适合小型项目。例如：学生管理系统
- 分布式架构：松耦合，扩展性好，但架构复杂，难度大。适合大型互联网项目，例如：京东、淘宝
- 微服务：一种良好的分布式架构方案
  - 优点：拆分粒度更小、服务更独立、耦合度更低
  - 缺点：架构非常复杂，运维、监控、部署难度提高
- Spring Cloud 是微服务架构的一站式解决方案，集成了各种优秀微服务功能组件

## 服务拆分和远程调用

### 服务拆分原则

微服务拆分时的几个原则：

- 不同微服务，不要重复开发相同业务
- 微服务数据独立，不要访问其它微服务的数据库
- 微服务可以将自己的业务暴露为接口，供其它微服务调用

### 服务拆分示例

以 spring-cloud-demo 为例，其结构如下：

```
spring-cloud-demo
 ├── order-service
 └── user-service
```

spring-cloud-demo：父工程，管理依赖

- order-service：订单微服务，负责订单相关业务
- user-service：用户微服务，负责用户相关业务

要求：

- 订单微服务和用户微服务都必须有各自的数据库，相互独立
- 订单服务和用户服务都对外暴露 Restful 的接口
- 订单服务如果需要查询用户信息，只能调用用户服务的 Restful 接口，不能查询用户数据库

初始项目代码：[链接](https://github.com/Lanqilu/HaloSpringCloud/releases/tag/v0.1)

### 实现远程调用案例

#### 案例需求

修改 order-service 中的根据 id 查询订单业务，要求在查询订单的同时，根据订单中包含的 userId 查询出用户信息，一起返回。

因此，我们需要在 order-service 中向 user-service 发起一个 http 的请求，调用 `http://localhost:8081/user/{userId}` 这个接口。

大概的步骤是这样的：

- 注册一个 RestTemplate 的实例到 Spring 容器
- 修改 order-service 服务中的 OrderService 类中的 queryOrderById 方法，根据 Order 对象中的 userId 查询 User
- 将查询的 User 填充到 Order 对象，一起返回

#### 注册 RestTemplate

首先，我们在 order-service 服务中的 OrderApplication 启动类中，注册 RestTemplate 实例：

```java
package cn.itcast.order;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@MapperScan("cn.itcast.order.mapper")
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

#### 实现远程调用

修改 order-service 服务中的 cn.itcast.order.service 包下的 OrderService 类中的 queryOrderById 方法：

```java
@Autowired
private RestTemplate restTemplate;

public Order queryOrderById(Long orderId) {
    // 1.查询订单
    Order order = orderMapper.findById(orderId);
    // 2. 利用 RestTemplate 发起 http 请求，查询用户
    String url = "http://localhost:8081/user/" + order.getUserId();
    User user = restTemplate.getForObject(url, User.class);
    // 3. 封装 user 到 Order
    order.setUser(user);
    // 4.返回
    return order;
}
```

代码：[链接](https://github.com/Lanqilu/HaloSpringCloud/tree/44482f1ec6618da6ee5805fbbdc5c0c2464725bb)

### 提供者与消费者

在服务调用关系中，会有两个不同的角色：

+ 服务提供者：一次业务中，被其它微服务调用的服务。（提供接口给其它微服务）
+ 服务消费者：一次业务中，调用其它微服务的服务。（调用其它微服务提供的接口）

但是，服务提供者与服务消费者的角色并不是绝对的，而是相对于业务而言。如果服务 A 调用了服务 B，而服务B又调用了服务 C，服务 B 的角色是什么？

- 对于 A 调用 B 的业务而言：A 是服务消费者，B 是服务提供者
- 对于 B 调用 C 的业务而言：B 是服务消费者，C 是服务提供者

因此，服务 B 既可以是服务提供者，也可以是服务消费者。

## Eureka 注册中心

以上实例存在的问题：

- order-service 在发起远程调用的时候，该如何得知 user-service 实例的 ip 地址和端口？
- 有多个 user-service 实例地址，order-service 调用时该如何选择？
- order-service 如何得知某个 user-service 实例是否依然健康，是不是已经宕机？

### Eureka 的结构和作用

![Eureka的作用](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Eureka的作用.mzzzu1u538w.svg)

问题1：order-service 如何得知 user-service 实例地址？获取地址信息的流程如下：

- user-service 服务实例启动后，将自己的信息注册到 eureka-server（Eureka 服务端）。这个叫服务注册
- eureka-server 保存服务名称到服务实例地址列表的映射关系
- order-service 根据服务名称，拉取实例地址列表。这个叫服务发现或服务拉取

问题2：order-service 如何从多个 user-service 实例中选择具体的实例？

- order-service 从实例列表中利用负载均衡算法选中一个实例地址
- 向该实例地址发起远程调用

问题3：order-service 如何得知某个 user-service 实例是否依然健康，是不是已经宕机？

- user-service 会每隔一段时间（默认 30 秒）向 eureka-server 发起请求，报告自己状态，称为心跳
- 当超过一定时间没有发送心跳时，eureka-server 会认为微服务实例故障，将该实例从服务列表中剔除
- order-service 拉取服务时，就能将故障实例排除了

在 Eureka 架构中，微服务角色有两类：

+ EurekaServer：服务端，注册中心。记录服务信息、心跳监控
+ EurekaClient：客户端
  + Provider：服务提供者，例如案例中的 user-service。注册自己的信息到 EurekaServer、每隔 30 秒向 EurekaServer 发送心跳
  + Consumer：服务消费者，例如案例中的 order-service 根据服务名称从 EurekaServer 拉取服务列表基于服务列表做负载均衡，选中一个微服务后发起远程调用

> 注意：一个微服务，既可以是服务提供者，又可以是服务消费者，因此 eureka 将服务注册、服务发现等功能统一封装到了eureka-client 端

eureka 服务注册分为以下步骤：

+ 搭建注册中心：搭建 EurekaServer
+ 服务注册：将 user-service、order-service 都注册到 eureka
+ 服务发现：在 order-service 中完成服务拉取，然后通过负载均衡挑选一个服务，实现远程调用

### 搭建 eureka-server

首先大家注册中心服务端：eureka-server，这必须是一个独立的微服务

创建 eureka-server 服务，在 spring-cloud-demo 父工程下，创建 eureka-server Maven 子模块

引入 eureka 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

#### 编写启动类

给 eureka-server 服务编写一个启动类，一定要添加一个 `@EnableEurekaServer` 注解，开启 eureka 的注册中心功能：

```java
package cn.itcast.eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```

#### 编写配置文件

编写一个 application.yml 文件，内容如下：

```yaml
server:
  port: 10086 # 服务端口
spring:
  application:
    name: eureka-server # 服务名称 必须
eureka:
  client:
    service-url: # eureka 的地址信息
      defaultZone: http://127.0.0.1:10086/eureka
```

#### 启动服务

启动微服务，然后在浏览器访问：http://127.0.0.1:10086

看到下面结果应该是成功了：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3fxbktup2b40.png)

代码：[链接](https://github.com/Lanqilu/HaloSpringCloud/tree/6485893c857e8407573b163f91fd7b6b7cfd4cfe)

### Eureka 服务注册

下面，我们将 user-service 注册到 eureka-server 中去

#### 引入依赖

在 user-service 的 pom 文件中，引入下面的 eureka-client 依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

#### 修改配置文件

在 user-service 中，修改 application.yml 文件，添加服务名称、eureka 地址：

```yaml
spring:
  application:
    name: user-service
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
```

用同样的方法可以注册 order-service

#### 启动多个 user-service 实例

为了演示一个服务有多个实例的场景，我们添加一个 SpringBoot 的启动配置，再启动一个 user-service。

首先，复制原来的 user-service 启动配置：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.1oxav2o4lv7k.png)

然后，在弹出的窗口中，填写信息：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.acb3rk5x180.png)

启动两个 user-service 实例后，查看 eureka-server 管理页面：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4tnhuetb1yc0.png)

### Eureka 服务发现

下面，我们将 order-service 的逻辑修改：向 eureka-server 拉取 user-service 的信息，实现服务发现。

#### 引入依赖和修改配置文件

之前说过，服务发现、服务注册统一都封装在 eureka-client 依赖，因此这一步与服务注册时一致。

#### 服务拉取和负载均衡

去 eureka-server 中拉取 user-service 服务的实例列表，并且实现负载均衡。

在 order-service 的 OrderApplication 中，给 RestTemplate 这个 Bean 添加一个 `@LoadBalanced` 注解：

```java
/**
 * 创建 RestTemplate 并注入容器
 */
@Bean
@LoadBalanced
public RestTemplate restTemplate(){
    return new RestTemplate();
}
```

修改 order-service 服务中的 cn.itcast.order.service 包下的 OrderService 类中的queryOrderById 方法。修改访问的 url 路径，用服务名代替 ip、端口：

```java
public Order queryOrderById(Long orderId) {
    // 1.查询订单
    Order order = orderMapper.findById(orderId);
    // 2. 利用 RestTemplate 发起 http 请求，查询用户
    String url = "http://user-service/user/" + order.getUserId();
    User user = restTemplate.getForObject(url, User.class);
    // 3. 封装 user 到 Order
    order.setUser(user);
    // 4.返回
    return order;
}
```

spring 会自动帮助我们从 eureka-server 端，根据 userservice 这个服务名称，获取实例列表，而后完成负载均衡（默认轮询）。

### Eureka 注册中心小结

1. 搭建 EurekaServer
   + 引入 eureka-server 依赖
   + 添加 `@EnableEurekaServer` 注解
   + 在 application.yml 中配置 eureka 地址
2. 服务注册
   + 引入 eureka-client 依赖
   + 在 application.yml 中配置 eureka 地址
3. 服务发现
   + 引入 eureka-client 依赖
   + 在 application.yml 中配置 eureka 地址
   + 给 RestTemplate 添加 `@LoadBalanced` 注解
   + 用服务提供者的服务名称远程调用

## Ribbon 负载均衡

上一节中，我们添加了 @LoadBalanced 注解，即可实现负载均衡功能，这是什么原理呢？

### 负载均衡原理

Spring Cloud 底层其实是利用了一个名为 Ribbon 的组件，来实现负载均衡功能的。

![Ribbon负载均衡流程](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Ribbon负载均衡流程.2mk0jmuwdoq0.svg)

### 源码跟踪

为什么我们只输入了 service 名称就可以访问了呢？之前还要获取 ip 和端口。

显然有人帮我们根据 service 名称，获取到了服务实例的 ip 和端口。它就是 `LoadBalancerInterceptor`，这个类会在对 RestTemplate 的请求进行拦截，然后从 Eureka 根据服务 id 获取服务列表，随后利用负载均衡算法得到真实的服务地址信息，替换服务 id。

我们进行源码跟踪：

#### LoadBalancerIntercepor

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5mxujbposco0.png)

可以看到这里的 intercept 方法，拦截了用户的 HttpRequest 请求，然后做了几件事：

- `request.getURI()`：获取请求 url，本例中就是 http://user-service/user/8
- `originalUri.getHost()`：获取 uri 路径的主机名，其实就是服务 id，`user-service`
- `this.loadBalancer.execute()`：处理服务 id，和用户请求。

这里的 `this.loadBalancer` 是 `LoadBalancerClient` 类型，我们继续跟入。

#### LoadBalancerClient

继续跟入 execute 方法：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.66vjvvg0xn40.png)

代码是这样的：

- `getLoadBalancer(serviceId)`：根据服务 id 获取 ILoadBalancer，而 ILoadBalancer 会拿着服务 id 去 eureka 中获取服务列表并保存起来。
- `getServer(loadBalancer)`：利用内置的负载均衡算法，从服务列表中选择一个。本例中，可以看到获取了8081端口的服务

#### 负载均衡策略 IRule

在刚才的代码中，可以看到获取服务使通过一个 `getServer` 方法来做负载均衡:

我们继续跟入：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3vkjab3blgm0.png)

继续跟踪源码 chooseServer 方法，发现调用父类中 BaseLoadBalancer 一段代码：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4ks2tojsyxe0.png)

我们看看这个 rule 是谁：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2jcbindlmai0.png)

默认是使用轮询，到这里，整个负载均衡的流程我们就清楚了。

#### 负载均衡原理源码分析小结

Spring Cloud Ribbon 的底层采用了一个拦截器，拦截了 RestTemplate 发出的请求，对地址做了修改。基本流程如下：

- 拦截我们的 RestTemplate 请求 http://userservice/user/1
- RibbonLoadBalancerClient 会从请求 url 中获取服务名称，也就是 user-service
- DynamicServerListLoadBalancer 根据 user-service 到 eureka 拉取服务列表
- eureka 返回列表，localhost:8081、localhost:8082
- IRule 利用内置负载均衡规则，从列表中选择一个，例如 localhost:8081
- RibbonLoadBalancerClient 修改请求地址，用 localhost:8081 替代 userservice，得到 http://localhost:8081/user/1，发起真实请求

### 负载均衡策略

负载均衡的规则都定义在 IRule 接口中，而 IRule 有很多不同的实现类：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.61x1c4fc6pg0.png)

常见的不同规则的含义如下：

1. RoundRobinRule：  简单轮询服务列表来选择服务器。它是 Ribbon 默认的负载均衡规则。
2. AvailabilityFilteringRule：对以下两种服务器进行忽略
   + 在默认情况下，这台服务器如果 3 次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续 30 秒，如果再次连接失败，短路的持续时间就会几何级地增加。
   +  并发数过高的服务器。如果一个服务器的并发连接数过高，配置了 AvailabilityFilteringRule 规则的客户端也会将其忽略。并发连接数的上限，可以由客户端的`<clientName>.<clientConfigNameSpace>.ActiveConnectionsLimit `属性进行配置。
3. WeightedResponseTimeRule：为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。
4. ZoneAvoidanceRule：以区域可用的服务器为基础进行服务器的选择。使用 Zone 对服务器进行分类，这个 Zone 可以理解为一个机房、一个机架等。而后再对 Zone 内的多个服务做轮询。
5. BestAvailableRule：忽略那些短路的服务器，并选择并发数较低的服务器。
6. RandomRule：随机选择一个可用的服务器。
7. RetryRule：  重试机制的选择逻辑

默认的实现就是 ZoneAvoidanceRule，是一种轮询方案

#### 自定义负载均衡策略

通过定义 IRule 实现可以修改负载均衡规则，有两种方式：

代码方式：在 order-service 中的 OrderApplication 类（或配置类）中，定义一个新的 IRule：

```java
@Bean
public IRule randomRule(){
    return new RandomRule();
}
```

配置文件方式：在 order-service 的 application.yml 文件中，添加新的配置也可以修改规则：

```yaml
user-service: # 给某个微服务配置负载均衡规则，这里是user-service服务
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则 
```

代码方式针对全局，配置文件方式可以对某个服务进行配置

一般用默认的负载均衡规则，不做修改。

#### 饥饿加载

Ribbon 默认是采用懒加载，即第一次访问时才会去创建 LoadBalanceClient，请求时间会很长。

而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load: 
    enabled: true # 开启饥饿加载
    clients: user-service # 指定对 user-service 这个服务进行加载
```

### Ribbon 负载均衡小结

1. Ribbon 负载均衡规则：
   + 规则接口是 IRule
   + 默认实现是 ZoneAvoidanceRule，根据 zone 选择服务列表，然后轮询
2. 负载均衡自定义方式：
   + 代码方式：配置灵活，但修改时需要重新打包发布
   + 配置方式：直观，方便，无需重新打包发布，但是无法做全局配置
3. 饥饿加载
   + 开启饥饿加载，配置文件
   + 指定饥饿加载的微服务名称，多个 clients 使用 yaml 列表

## Nacos 注册中心

### 认识和安装 Nacos

[Nacos](https://nacos.io/) 是阿里巴巴的产品，现在是 [SpringCloud](https://spring.io/projects/spring-cloud) 中的一个组件。相比 [Eureka](https://github.com/Netflix/eureka) 功能更加丰富，在国内受欢迎程度较高。

Docker 安装：[官网](https://nacos.io/zh-cn/docs/quick-start-docker.html)

### 服务注册到 Nacos

Nacos 是 Spring Cloud Alibaba 的组件，而 Spring Cloud Alibaba 也遵循 Spring Cloud 中定义的服务注册、服务发现规范。因此使用 Nacos 和使用 Eureka 对于微服务来说，并没有太大区别。其主要差异在于：依赖不同、服务地址不同。

#### 引入 Nacos 依赖

在 spring-cloud-demo 父工程的 pom 文件中的 `<dependencyManagement>` 中引入 Spring Cloud Alibaba 的依赖：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

然后在 user-service 和 order-service 中的 pom 文件中引入 nacos-discovery 依赖：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

> 注意：不要忘了注释掉 eureka 的依赖。

#### 配置 Nacos 地址

在 user-service 和 order-service 的 application.yml 中添加 nacos 地址：

```yaml
spring:
  cloud:
    nacos:
      server-addr: n:8848
```

> 这里使用的是远程服务器的地址

#### 启动项目

启动 user-service 和 order-service 浏览器输入 http://halo:8848/nacos/ 账号密码默认都为 nacos，项目成功可以看到服务列表中的服务。  

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.6jgz8xeif3g0.png)

### 服务分级存储模型

一个**服务**可以有多个**实例**，例如我们的 user-service，可以有:

- 127.0.0.1:8081
- 127.0.0.1:8082
- 127.0.0.1:8083

假如这些实例分布于全国各地的不同机房，例如：

- 127.0.0.1:8081，在上海机房
- 127.0.0.1:8082，在上海机房
- 127.0.0.1:8083，在杭州机房

Nacos 就将同一机房内的实例划分为一个**集群**。

也就是说，user-service 是服务，一个服务可以包含多个集群，如杭州、上海，每个集群下可以有多个实例，形成分级模型，如图：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2mxp9c1ugiw0.png)

微服务互相访问时，应该尽可能访问同集群实例，因为本地访问速度更快。当本集群内不可用时，才访问其它集群。例如：杭州机房内的 order-service 应该优先访问同机房的 user-service。

#### 给 user-service 配置集群

修改 user-service 的 application.yml 文件，添加集群配置：

```yaml
spring:
  cloud:
    nacos:
      server-addr: halo:8848
      discovery:
        cluster-name: HZ # 集群名称
```

重启两个 user-service 实例后，我们再次复制一个 user-service 启动配置，添加属性：

```sh
-Dserver.port=8083 -Dspring.cloud.nacos.discovery.cluster-name=SH
```

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.1piv5isgkpb4.png)

我们可以在 Nacos 控制台看到下面结果：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3pz2tkuveru0.png)

#### 同集群优先的负载均衡

默认的 `ZoneAvoidanceRule` 并不能实现根据同集群优先来实现负载均衡。

因此 Nacos 中提供了一个 `NacosRule` 的实现，可以优先从同集群中挑选实例。

首先给 order-service 配置集群信息，方法同上一节

修改 order-service 的 application.yml 文件，添加集群配置：

```yaml
spring:
  cloud:
    nacos:
      server-addr: halo:8848
      discovery:
        cluster-name: HZ # 集群名称
```

修改负载均衡规则。修改 order-service 的 application.yml 文件，修改负载均衡规则：

```yaml
user-service:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule # 负载均衡规则
```

本地集群中采用随机

Nacos Rule 负载均衡策略

1. 优先选择同集群服务实例列表
2. 本地集群找不到提供者，才去其它集群寻找，并且会报警告
3. 确定了可用实例列表后，再采用随机负载均衡挑选实例

### 权重配置

实际部署中会出现这样的场景：服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求。

但默认情况下 Nacos Rule 是同集群内随机挑选，不会考虑机器的性能问题。

因此，Nacos 提供了权重配置来控制访问频率，权重越大则访问频率越高。

在 Nacos 控制台，找到 user-service 的实例列表，点击编辑，即可修改权重：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5bywq83lkh00.png)

> 注意：如果权重修改为 0，则该实例永远不会被访问

### 环境隔离

Nacos 提供了 namespace 来实现环境隔离功能。

- Nacos 中可以有多个 namespace
- namespace 下可以有 group、service 等
- 不同 namespace 之间相互隔离，例如不同 namespace 的服务互相不可见

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.33r23m595e00.png)

#### 创建 namespace

默认情况下，所有 service、data、group 都在同一个 namespace，名为 public：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3ebpxcypny00.png)

我们可以点击页面新增按钮，添加一个 namespace

#### 给微服务配置 namespace

给微服务配置 namespace 只能通过修改配置来实现。

例如，修改 order-service 的 application.yml 文件：

```yaml
spring:
  cloud:
    nacos:
      server-addr: halo:8848
      discovery:
        cluster-name: HZ
        namespace: 45e30304-1b64-4c21-8c83-22309949af10 # 命名空间，填ID
```

重启 order-service 后，访问控制台，可以看到下面的结果：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.7fljcch1djg0.png)

此时访问 order-service，因为 namespace 不同，会导致找不到 user-service，控制台会报错：

```
09-09 22:11:49:886 ERROR 4100 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.lang.IllegalStateException: No instances available for user-service] with root cause
```

## Nacos 与 Eureka 的异同

### Nacos 服务实例类型

Nacos 的服务实例分为两种类型：

- 临时实例：如果实例宕机超过一定时间，会从服务列表剔除，默认的类型。
- 非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。

配置一个服务实例为永久实例：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 设置为非临时实例
```

### Nacos 注册中心原理

Nacos 和 Eureka 整体结构类似，服务注册、服务拉取、心跳等待，但是也存在一些差异：

![Nacos注册中心原理](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Nacos注册中心原理.vsk7w614iqo.svg)

### Nacos 与 Eureka 的共同点

- 都支持服务注册和服务拉取
- 都支持服务提供者心跳方式做健康检测

### Nacos 与 Eureka 的区别

- Nacos 支持服务端主动检测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式
- 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
- Nacos 支持服务列表变更的消息推送模式，服务列表更新更及时
- Nacos 集群默认采用 AP 方式，当集群中存在非临时实例时，采用 CP 模式；Eureka采用 AP 方式

## Nacos 配置管理

Nacos 除了可以做注册中心，也可以做配置管理来使用。

### 统一配置管理

当微服务部署的实例越来越多，达到数十、数百时，逐个修改微服务配置就会让人抓狂，而且很容易出错。我们需要一种统一配置管理方案，可以集中管理所有实例的配置。

Nacos 一方面可以将配置集中管理，另一方可以在配置变更时，及时通知微服务，实现配置的热更新。

#### 在 Nacos 中添加配置文件

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.1k2q3p4hypa8.png)

然后在弹出的表单中，填写配置信息：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.26dw95t2zibk.png)

> 注意：项目的核心配置，需要热更新的配置才有放到 Nacos 管理的必要。基本不会变更的一些配置还是保存在微服务本地比较好。

#### 从微服务拉取配置

微服务要拉取 Nacos 中管理的配置，并且与本地的 application.yml 配置合并，才能完成项目启动。

但如果尚未读取 application.yml，又如何得知 Nacos 地址呢？

因此 Spring 引入了一种新的配置文件：bootstrap.yaml 文件，会在 application.yml 之前被读取，流程如下：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.50gh2h1avrw0.png)

① 引入 nacos-config 依赖

首先，在 user-service 服务中，引入 nacos-config 的客户端依赖：

```xml
<!--nacos配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

② 添加 bootstrap.yaml

然后，在 user-service 中添加一个 bootstrap.yaml 文件，内容如下：

```yaml
spring:
  application:
    name: user-service # 服务名称
  profiles:
    active: dev # 开发环境，这里是 dev 
  cloud:
    nacos:
      server-addr: halo:8848 # Nacos地址
      config:
        file-extension: yaml # 文件后缀名
```

这里会根据 `spring.cloud.nacos.server-addr` 获取 Nacos 地址，再根据 `${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}` 作为文件 id，来读取配置。

本例中，就是去读取 `user-service-dev.yaml`：

③ 读取 Nacos 配置

在 user-service 中的 `UserController` 中添加业务逻辑，读取 `pattern.dateformat` 配置：

```java
@Value("${pattern.dateformat}")
private String dateformat;

@GetMapping("now")
public String now() {
    return LocalDateTime.now().format(DateTimeFormatter.ofPattern(dateformat));
}
```

### 配置热更新

我们最终的目的，是修改 Nacos 中的配置后，微服务中无需重启即可让配置生效，也就是配置热更新。

要实现配置热更新，可以使用两种方式：

#### 方式一

在 `@Value` 注入的变量所在类上添加注解 `@RefreshScope`：

```java
@Slf4j
@RestController
@RequestMapping("/user")
@RefreshScope
public class UserController {
    @Value("${pattern.dateformat}")
    private String dateformat;
    
    // 略
}
```

#### 方式二

使用 `@ConfigurationProperties` 注解代替 `@Value` 注解。

在 user-service 服务中，添加一个类，读取 `patterrn.dateformat` 属性：

```java
package cn.itcast.user.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@Data
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
    private String dateformat;
}
```

在 `UserController` 中使用这个类代替 `@Value`：

```java
@Autowired
private PatternProperties properties;

@GetMapping("now")
public String now() {
    return LocalDateTime.now().format(DateTimeFormatter.ofPattern(properties.getDateformat()));
}
```

### 配置共享

其实微服务启动时，会去 Nacos 读取多个配置文件，例如：

- `[spring.application.name]-[spring.profiles.active].yaml`，例如：user-service-dev.yaml

- `[spring.application.name].yaml`，例如：user-service.yaml

而 `[spring.application.name].yaml` 不包含环境，因此可以被多个环境共享。

下面我们通过案例来测试配置共享

#### 添加一个环境共享配置

在 Nacos 中添加一个 user-service.yaml 文件：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4j1r0ype41k0.png)

#### 在 user-service 中读取共享配置

在 user-service 服务中，修改 `PatternProperties` 类，读取新添加的属性：

```java
@Data
@Component
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
    private String dateformat;
    private String envShareValue;
}
```

在 user-service 服务中，修改 `UserController`，添加一个方法：

```java
@Autowired
private PatternProperties properties;

@GetMapping("prop")
public PatternProperties prop(){
    return properties;
}
```

#### 不同的 profile 下测试

运行两个 UserApplication，使用不同的 profile。修改 UserApplication2 这个启动项，改变其 profile 值：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5fmr356kyr80.png)

这样，UserApplication（8081） 使用的 profile 是 dev，UserApplication2（8082） 使用的 profile 是 test。

启动 UserApplication 和 UserApplication2

可以看出来，不管是 dev，还是 test 环境，都读取到了envSharedValue 这个属性的值。

#### 配置共享的优先级

当 Nacos、服务本地同时出现相同属性时，优先级有高低之分，优先级从高到低依次是：

+ 服务名-[profile].yaml
+ 服务名.yaml
+ 本地配置

## 搭建 Nacos 集群

计划的集群结构：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3v4vof6qfki0.png)

三个 nacos 节点的地址：

| 节点   | ip   | port |
| ------ | ---- | ---- |
| nacos1 | halo | 8845 |
| nacos2 | halo | 8846 |
| nacos3 | halo | 8847 |

搭建集群的基本步骤：

- 搭建数据库，初始化数据库表结构
- 下载 Nacos 安装包
- 配置 Nacos
- 启动 Nacos 集群
- Nginx 反向代理

### 初始化数据库

Nacos 默认数据存储在内嵌数据库 Derby 中，不属于生产可用的数据库。

这里我们以单点的数据库为例来讲解。

首先新建一个数据库，命名为 nacos，而后导入下面的 SQL：

```sql
CREATE TABLE `config_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) DEFAULT NULL,
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(50) DEFAULT NULL COMMENT 'source ip',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  `c_desc` varchar(256) DEFAULT NULL,
  `c_use` varchar(64) DEFAULT NULL,
  `effect` varchar(64) DEFAULT NULL,
  `type` varchar(64) DEFAULT NULL,
  `c_schema` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_aggr   */
/******************************************/
CREATE TABLE `config_info_aggr` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) NOT NULL COMMENT 'group_id',
  `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
  `content` longtext NOT NULL COMMENT '内容',
  `gmt_modified` datetime NOT NULL COMMENT '修改时间',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';


/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_beta   */
/******************************************/
CREATE TABLE `config_info_beta` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(50) DEFAULT NULL COMMENT 'source ip',
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_tag   */
/******************************************/
CREATE TABLE `config_info_tag` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(50) DEFAULT NULL COMMENT 'source ip',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_tags_relation   */
/******************************************/
CREATE TABLE `config_tags_relation` (
  `id` bigint(20) NOT NULL COMMENT 'id',
  `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
  `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `nid` bigint(20) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`nid`),
  UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = group_capacity   */
/******************************************/
CREATE TABLE `group_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_group_id` (`group_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = his_config_info   */
/******************************************/
CREATE TABLE `his_config_info` (
  `id` bigint(64) unsigned NOT NULL,
  `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `data_id` varchar(255) NOT NULL,
  `group_id` varchar(128) NOT NULL,
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL,
  `md5` varchar(32) DEFAULT NULL,
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `src_user` text,
  `src_ip` varchar(50) DEFAULT NULL,
  `op_type` char(10) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`nid`),
  KEY `idx_gmt_create` (`gmt_create`),
  KEY `idx_gmt_modified` (`gmt_modified`),
  KEY `idx_did` (`data_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';


/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = tenant_capacity   */
/******************************************/
CREATE TABLE `tenant_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';


CREATE TABLE `tenant_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `kp` varchar(128) NOT NULL COMMENT 'kp',
  `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
  `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
  `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
  `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
  `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
  `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';

CREATE TABLE `users` (
	`username` varchar(50) NOT NULL PRIMARY KEY,
	`password` varchar(500) NOT NULL,
	`enabled` boolean NOT NULL
);

CREATE TABLE `roles` (
	`username` varchar(50) NOT NULL,
	`role` varchar(50) NOT NULL,
	UNIQUE INDEX `idx_user_role` (`username` ASC, `role` ASC) USING BTREE
);

CREATE TABLE `permissions` (
    `role` varchar(50) NOT NULL,
    `resource` varchar(255) NOT NULL,
    `action` varchar(8) NOT NULL,
    UNIQUE INDEX `uk_role_permission` (`role`,`resource`,`action`) USING BTREE
);

INSERT INTO users (username, password, enabled) VALUES ('nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);

INSERT INTO roles (username, role) VALUES ('nacos', 'ROLE_ADMIN');
```

### 下载 Nacos

Nacos 在 GitHub 上有下载地址：https://github.com/alibaba/nacos/tags，可以选择任意版本下载。

本例中才用 1.4.1 版本

### 配置 Nacos

将这个包解压到任意非中文目录下

- bin：启动脚本
- conf：配置文件

进入 Nacos 的 conf 目录，修改配置文件 cluster.conf.example，重命名为 cluster.conf

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5nlh9ki5w2s0.png)

然后添加内容：

```
127.0.0.1:8845
127.0.0.1.8846
127.0.0.1.8847
```

然后修改 application.properties 文件，添加数据库配置

```
spring.datasource.platform=mysql

db.num=1

db.url.0=jdbc:mysql://halo:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=mogu2018
```

### 启动 Nacos

将 nacos 文件夹复制三份，分别命名为：nacos1、nacos2、nacos3

然后分别修改三个文件夹中的 application.properties，

nacos1:

```properties
server.port=8845
```

nacos2:

```properties
server.port=8846
```

nacos3:

```properties
server.port=8847
```

然后分别启动三个 nacos 节点：

```
startup.cmd
```

### Nginx 反向代理

修改 conf/nginx.conf 文件，配置如下：

```
upstream nacos-cluster {
    server 127.0.0.1:8845;
	server 127.0.0.1:8846;
	server 127.0.0.1:8847;
}

server {
    listen       80;
    server_name  localhost;

    location /nacos {
        proxy_pass http://nacos-cluster;
    }
}
```

而后在浏览器访问：http://localhost/nacos 即可。

代码中 application.yml 文件配置如下：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:80 # Nacos地址
```

- 实际部署时，需要给做反向代理的 Nginx 服务器设置一个域名，这样后续如果有服务器迁移 Nacos 的客户端也无需更改配置。

- Nacos 的各个节点应该部署到多个不同服务器，做好容灾和隔离

## Feign 远程调用

先来看我们以前利用 RestTemplate 发起远程调用的代码：

```java
// 2. 利用 RestTemplate 发起 http 请求，查询用户
String url = "http://user-service/user/" + order.getUserId();
User user = restTemplate.getForObject(url, User.class);
```

存在下面的问题：

+ 代码可读性差，编程体验不统一
+ 参数复杂 URL 难以维护

Feign 是一个声明式的 http 客户端，官方地址：https://github.com/OpenFeign/feign

其作用就是帮助我们优雅的实现 http 请求的发送，解决上面提到的问题。

### Feign 替代 RestTemplate

Feign 的使用步骤如下：

#### 引入依赖

我们在 order-service 服务的 pom 文件中引入 feign 的依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

#### 添加注解

在 order-service 的启动类添加 `@EnableFeignClients` 注解开启 Feign 的功能：

```java
@EnableFeignClients
@MapperScan("cn.itcast.order.mapper")
@SpringBootApplication
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```

#### 编写 Feign 的客户端

在 order-service 中新建一个接口，内容如下：

```java
package cn.itcast.order.clients;

import cn.itcast.order.pojo.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient("user-service")
public interface UserClient {
    @GetMapping("/user/{id}")
    User findById(@PathVariable("id") Long id);
}
```

这个客户端主要是基于 SpringMVC 的注解来声明远程调用的信息，比如：

- 服务名称：user-service
- 请求方式：GET
- 请求路径：/user/{id}
- 请求参数：Long id
- 返回值类型：User

这样，Feign 就可以帮助我们发送 http 请求，无需自己使用 RestTemplate 来发送了。

#### 测试

修改 order-service 中的 `OrderService` 类中的 `queryOrderById` 方法，使用 Feign 客户端代替 RestTemplate：

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;

    @Autowired
    private UserClient userClient;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        // 2. 使用 Feign 进行远程调用
        User user = userClient.findById(order.getUserId());
        // 3. 封装 user 到 Order
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```

#### Feign 替代 RestTemplate 小结

使用 Feign 的步骤：

1. 引入依赖
2. 添加 `@EnableFeignClients` 注解
3. 编写 FeignClient 接口
4. 使用 FeignClient 中定义的方法代替 RestTemplate

### Feign 的自定义配置

Feign 可以支持很多的自定义配置，如下表所示：

| 类型                  | 作用             | 说明                                                        |
| --------------------- | ---------------- | ----------------------------------------------------------- |
| `feign.Logger.Level`  | 修改日志级别     | 包含四种不同的级别：NONE、BASIC、HEADERS、FULL              |
| `feign.codec.Decoder` | 响应结果的解析器 | http 远程调用的结果做解析，例如解析 JSON 字符串为 Java 对象 |
| `feign.codec.Encoder` | 请求参数编码     | 将请求参数编码，便于通过 http 请求发送                      |
| `feign.Contract`      | 支持的注解格式   | 默认是 SpringMVC 的注解                                     |
| `feign.Retryer`       | 失败重试机制     | 请求失败的重试机制，默认是没有，不过会使用Ribbon 的重试     |

一般情况下，默认值就能满足我们使用，如果要自定义时，只需要创建自定义的 `@Bean` 覆盖默认Bean 即可。

下面以日志为例来演示如何自定义配置。

#### 配置文件方式

基于配置文件修改 Feign 的日志级别可以针对单个服务：

```yaml
feign:  
  client:
    config: 
      userservice: # 针对某个微服务的配置
        loggerLevel: FULL # 日志级别 
```

也可以针对所有服务：

```yaml
feign:  
  client:
    config: 
      default: # 这里用 default 就是全局配置，如果是写服务名称，则是针对某个微服务的配置
        loggerLevel: FULL # 日志级别 
```

而日志的级别分为四种：

- NONE：不记录任何日志信息，这是默认值。
- BASIC：仅记录请求的方法，URL 以及响应状态码和执行时间
- HEADERS：在 BASIC 的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。

#### Java 代码方式

也可以基于 Java 代码来修改日志级别，先声明一个类，然后声明一个 `Logger.Level` 的对象：

```java
public class DefaultFeignConfiguration  {
    @Bean
    public Logger.Level feignLogLevel(){
        return Logger.Level.BASIC; // 日志级别为BASIC
    }
}
```

如果要全局生效，将其放到启动类的 `@EnableFeignClients` 这个注解中：

```java
@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration .class) 
```

如果是局部生效，则把它放到对应的 `@FeignClient` 这个注解中：

```java
@FeignClient(value = "user-service", configuration = DefaultFeignConfiguration .class)
```

### Feign 使用优化

Feign 底层发起 http 请求，依赖于其它的框架。其底层客户端实现包括：

+ URLConnection：默认实现，不支持连接池
+ Apache HttpClient ：支持连接池
+ OKHttp：支持连接池

因此提高 Feign 的性能主要手段就是使用连接池代替默认的 URLConnection。

这里我们用 Apache 的 HttpClient 来演示。

#### 引入依赖

在 order-service 的 pom 文件中引入 Apache 的 HttpClient 依赖：

```xml
<!--httpClient的依赖 -->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
</dependency>
```

#### 配置连接池

在 order-service 的 application.yml 中添加配置：

```yaml
feign:
  client:
    config:
      default: # default 全局的配置
        loggerLevel: BASIC # 日志级别，BASIC 就是基本的请求和响应信息
  httpclient:
    enabled: true # 开启 feign 对 HttpClient 的支持
    max-connections: 200 # 最大的连接数
    max-connections-per-route: 50 # 每个路径的最大连接数
```

#### Feign 的优化小结

1. 日志级别尽量用 basic
2. 使用 HttpClient（或 OKHttp ）代替 URLConnection
   + 引入 feign-httpclient 依赖
   + 配置文件开启 httpclient 功能，设置连接池参数

## Feign 的最佳实践

所谓最近实践，就是使用过程中总结的经验，最好的一种使用方式。

仔细观察可以发现，Feign 的客户端与服务提供者的 controller 代码非常相似

Feign客户端：

```java
@GetMapping("/user/{id}")
User findById(@PathVariable("id") Long id);
```

UserController：

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id) {
    return userService.queryById(id);
}
```

有没有一种办法简化这种重复的代码编写呢？

### 继承方式

一样的代码可以通过继承来共享：

1. 定义一个 API 接口，利用定义方法，并基于 SpringMVC 注解做声明。
2. Feign 客户端和 Controller 都集成改接口

优点：简单、实现了代码共享

缺点：

+ 服务提供方、服务消费方紧耦合
+ 参数列表中的注解映射并不会继承，因此 Controller 中必须再次声明方法、参数列表、注解

### 抽取方式

将 Feign 的 Client 抽取为独立模块，并且把接口有关的 POJO、默认的 Feign 配置都放到这个模块中，提供给所有消费者使用。

例如，将 UserClient、User、Feign 的默认配置都抽取到一个 feign-api 包中，所有微服务引用该依赖包，即可直接使用。

### 基于抽取的最佳实践实现

#### 抽取

首先创建一个 module，命名为 feign-api

在 feign-api 中然后引入 feign 的 starter 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

然后，order-service 中编写的 UserClient、User、DefaultFeignConfiguration 都剪切到 feign-api项目中

#### 使用 feign-api

在 order-service 的 pom 文件中中引入 feign-api 的依赖：

```xml
<dependency>
    <groupId>cn.itcast.demo</groupId>
    <artifactId>feign-api</artifactId>
    <version>1.0</version>
</dependency>
```

修改 order-service 中的所有与上述三个组件有关的导包部分，改成导入 feign-api 中的包

#### 解决扫描包问题

order-service 的 `@EnableFeignClients` 注解是在 cn.itcast.order 包下，不在同一个包，无法扫描到 UserClient。

方式一，指定 Feign 应该扫描的包：

```java
@EnableFeignClients(basePackages = "cn.itcast.feign.clients")
```

方式二，指定需要加载的 Client 接口：

```java
@EnableFeignClients(clients = {UserClient.class})
```

## Gateway 服务网关

Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor 等响应式编程和事件流技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。

### 为什么需要网关

Gateway 网关是我们服务的守门神，所有微服务的统一入口。

网关的核心功能特性：

- 请求路由
- 权限控制
- 限流

权限控制：网关作为微服务入口，需要校验用户是是否有请求资格，如果没有则进行拦截。

路由和负载均衡：一切请求都必须先经过 Gateway，但网关不处理业务，而是根据某种规则，把请求转发到某个微服务，这个过程叫做路由。当然路由的目标服务有多个时，还需要做负载均衡。

限流：当请求流量过高时，在网关中按照下流的微服务能够接受的速度来放行请求，避免服务压力过大。

在 Spring Cloud 中网关的实现包括两种：Gateway、Zuul

Zuul 是基于 Servlet 的实现，属于阻塞式编程。而 Spring Cloud Gateway 则是基于 Spring 5 中提供的 WebFlux，属于响应式编程的实现，具备更好的性能。

### Gateway 快速入门

下面，我们就演示下网关的基本路由功能。基本步骤如下：

1. 创建 Maven 工程 gateway，引入网关依赖
2. 编写启动类
3. 编写基础配置和路由规则
4. 启动网关服务进行测试

#### 创建 Gateway 服务并引入依赖

创建 gateway 服务，引入一下依赖：

```xml
<!--网关-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!--nacos服务发现依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

#### 编写启动类

```java
package cn.itcast.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GatewayApplication {

	public static void main(String[] args) {
		SpringApplication.run(GatewayApplication.class, args);
	}
}
```

#### 编写基础配置和路由规则

创建 application.yml 文件，内容如下：

```yaml
server:
  port: 10010 # 网关端口
spring:
  application:
    name: gateway # 服务名称
  cloud:
    nacos:
      server-addr: halo:8848 # nacos地址
    gateway:
      routes: # 网关路由配置
        - id: user-service-gateway # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://user-service # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
```

我们将符合 `Path` 规则的一切请求，都代理到 `uri` 参数指定的地址。

本例中，我们将 `/user/**`开头的请求，代理到 `lb://user-service`，lb 是负载均衡，根据服务名拉取服务列表，实现负载均衡。

#### 启动测试

启动网关，访问 http://localhost:10010/user/1 时，符合`/user/**`规则，请求转发到 uri：http://userservice/user/1，得到了结果

#### 网关路由的流程图

![Gateway网关路由的流程图](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Gateway网关路由的流程图.6onddfdjlr00.svg)

#### Gateway 快速入门小结

网关搭建步骤：

1. 创建项目，引入 nacos 服务发现和 gateway 依赖

2. 配置 application.yml，包括服务基本信息、nacos 地址、路由

路由配置包括：

1. 路由 id：路由的唯一标示

2. 路由目标（uri）：路由的目标地址，http 代表固定地址，lb 代表根据服务名负载均衡

3. 路由断言（predicates）：判断路由的规则，

4. 路由过滤器（filters）：对请求或响应做处理

### 断言工厂

我们在配置文件中写的断言规则只是字符串，这些字符串会被 Predicate Factory 读取并处理，转变为路由判断的条件

例如 `Path=/user/**` 是按照路径匹配，这个规则是由 `org.springframework.cloud.gateway.handler.predicate.PathRoutePredicateFactory` 类来处理的，像这样的断言工厂在 Spring Cloud Gateway 还有十几个：

| 名称       | 说明                            | 示例                                                         |
| ---------- | ------------------------------- | ------------------------------------------------------------ |
| After      | 是某个时间点后的请求            | `-  After=2037-01-20T17:42:47.789-07:00[America/Denver]`     |
| Before     | 是某个时间点之前的请求          | `-  Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]`     |
| Between    | 是某两个时间点之前的请求        | `-  Between=2037-01-20T17:42:47.789-07:00[America/Denver],  2037-01-21T17:42:47.789-07:00[America/Denver]` |
| Cookie     | 请求必须包含某些 cookie         | `- Cookie=chocolate, ch.p`                                   |
| Header     | 请求必须包含某些 header         | `- Header=X-Request-Id, \d+`                                 |
| Host       | 请求必须是访问某个 host（域名） | `-  Host=**.somehost.org,**.anotherhost.org`                 |
| Method     | 请求方式必须是指定方式          | `- Method=GET,POST`                                          |
| Path       | 请求路径必须符合指定规则        | `- Path=/red/{segment},/blue/**`                             |
| Query      | 请求参数必须包含指定参数        | `- Query=name, Jack` 或者 `-  Query=name`                    |
| RemoteAddr | 请求者的 ip 必须是指定范围      | `- RemoteAddr=192.168.1.1/24`                                |
| Weight     | 权重处理                        |                                                              |

详情查阅：[官方文档](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories)

### 过滤器工厂

Gateway Filter 是网关中提供的一种过滤器，可以对进入网关的请求和微服务返回的响应做处理：

![过滤器工厂](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/过滤器工厂.2sw963vwfry0.svg)

#### 路由过滤器的种类

Spring 提供了 31 种不同的路由过滤器工厂。例如：

| 名称                   | 说明                         |
| ---------------------- | ---------------------------- |
| `AddRequestHeader`     | 给当前请求添加一个请求头     |
| `RemoveRequestHeader`  | 移除请求中的一个请求头       |
| `AddResponseHeader`    | 给响应结果中添加一个响应头   |
| `RemoveResponseHeader` | 从响应结果中移除有一个响应头 |
| `RequestRateLimiter`   | 限制请求的流量               |

详情查阅：[官方文档](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gatewayfilter-factories)

#### 请求头过滤器

下面我们以 `AddRequestHeader` 为例来讲解。

需求：给所有进入 user-service 的请求添加一个请求头：Truth=Halo

只需要修改 gateway 服务的 application.yml 文件，添加路由过滤即可：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: user-service 
        uri: lb://userservice 
        predicates: 
        - Path=/user/** 
        filters: # 过滤器
        - AddRequestHeader=Truth, Halo # 添加请求头
```

当前过滤器写在 user-service 路由下，因此仅仅对访问 user-service 的请求有效。

#### 默认过滤器

如果要对所有的路由都生效，则可以将过滤器工厂写到 default 下。格式如下：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: user-service 
        uri: lb://userservice 
        predicates: 
        - Path=/user/**
      default-filters: # 默认过滤项
      - AddRequestHeader=Truth, Halo
```

#### 过滤器工厂总结

过滤器的作用是什么？

+ 对路由的请求或响应做加工处理，比如添加请求头
+ 配置在路由下的过滤器只对当前路由的请求生效

defaultFilters 的作用是什么？

+ 对所有路由都生效的过滤器

### 全局过滤器

上一节学习的过滤器，网关提供了 31 种，但每一种过滤器的作用都是固定的。如果我们希望拦截请求，做自己的业务逻辑则没办法实现。

#### 全局过滤器作用

全局过滤器的作用也是处理一切进入网关的请求和微服务响应，与 GatewayFilter 的作用一样。

两者的区别在于： 

+ GatewayFilter 通过配置定义，处理逻辑是固定的；
+ GlobalFilter 的逻辑需要自己写代码实现。

定义方式是实现 GlobalFilter 接口。

```java
public interface GlobalFilter {
    /**
     *  处理当前请求，有必要的话通过{@link GatewayFilterChain}将请求交给下一个过滤器处理
     *
     * @param exchange 请求上下文，里面可以获取Request、Response等信息
     * @param chain 用来把请求委托给下一个过滤器 
     * @return {@code Mono<Void>} 返回标示当前过滤器业务结束
     */
    Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
}
```

在 filter 中编写自定义逻辑，可以实现下列功能：

- 登录状态判断
- 权限校验
- 请求限流等

#### 自定义全局过滤器

需求：定义全局过滤器，拦截请求，判断请求的参数是否满足下面条件：

- 参数中是否有 authorization，

- authorization 参数值是否为 admin

如果同时满足则放行，否则拦截

实现，在 gateway 中定义一个过滤器：

```java
@Order(-1)
@Component
public class AuthorizeFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 1. 获取请求参数
        ServerHttpRequest request = exchange.getRequest();
        MultiValueMap<String, String> queryParams = request.getQueryParams();
        // 2. 获取参数中的 authorization
        String auth = queryParams.getFirst("authorization");
        // 3. 判断参数值是否等于 admin
        if ("admin".equals(auth)) {
            // 放行
            return chain.filter(exchange);
        }
        // 拦截
        // 设置状态码
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
        // 拦截请求
        return exchange.getResponse().setComplete();
    }
}
```

`@Order(-1)` 用于指定过滤器顺序，值越小优先级越高

重启网关，访问 http://localhost:10010/user/1?authorization=admin

#### 过滤器执行顺序

请求进入网关会碰到三类过滤器：当前路由的过滤器、DefaultFilter、GlobalFilter

请求路由后，会将当前路由过滤器和 DefaultFilter、GlobalFilter，合并到一个过滤器链（集合）中，排序后依次执行每个过滤器：

![过滤器执行顺序](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/过滤器执行顺序.4mfcqien0h60.svg)

排序的规则是什么呢？

- 首先，每一个过滤器都必须指定一个 int 类型的 order 值，order 值越小，优先级越高，执行顺序越靠前。
- GlobalFilter 通过实现 Ordered 接口，或者添加 `@Order` 注解来指定 order 值，由我们自己指定。路由过滤器和 defaultFilter 的 order 由 Spring 指定，默认是按照声明顺序从 1 递增。
- 当过滤器的 order 值一样时，会按照 defaultFilter、路由过滤器、GlobalFilter 的顺序执行。

详细内容，可以查看源码：

+ `org.springframework.cloud.gateway.route.RouteDefinitionRouteLocator#getFilters()` 方法是先加载 defaultFilters，然后再加载某个 route 的 filters，然后合并。
+ `org.springframework.cloud.gateway.handler.FilteringWebHandler#handle()` 方法会加载全局过滤器，与前面的过滤器合并后根据order排序，组织过滤器链

### 跨域问题

#### 什么是跨域问题

跨域：域名不一致就是跨域，主要包括：

- 域名不同： www.taobao.com 和 www.taobao.org 和 www.jd.com 和 miaosha.jd.com

- 域名相同，端口不同：localhost:8080 和 localhost8081

跨域问题：浏览器禁止请求的发起者与服务端发生跨域 Ajax 请求，请求被浏览器拦截的问题

CORS 解决方案，参考资料：https://www.ruanyifeng.com/blog/2016/04/cors.html

#### 解决跨域问题

在 gateway 服务的 application.yml 文件中，添加下面的配置：

```yaml
spring:
  cloud:
    gateway:
      # .
      globalcors: # 全局的跨域处理
        add-to-simple-url-handler-mapping: true # 解决options请求被拦截问题
        corsConfigurations:
          '[/**]':
            allowedOrigins: # 允许哪些网站的跨域请求 
              - "http://localhost:8090"
            allowedMethods: # 允许的跨域ajax的请求方式
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowedHeaders: "*" # 允许在请求中携带的头信息
            allowCredentials: true # 是否允许携带cookie
            maxAge: 360000 # 这次跨域检测的有效期
```









