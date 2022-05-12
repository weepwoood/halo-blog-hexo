---
title: Sentinel入门
create: 2021-09-13
data: 2021-09-13
update: 2021-09-13
---

## 初识 Sentinel

### 雪崩问题及解决方案

#### 雪崩问题

微服务中，服务间调用关系错综复杂，一个微服务往往依赖于多个其它微服务。

如果服务提供者 I 发生了故障，当前的应用的部分业务因为依赖于服务 I，因此也会被阻塞。此时，其它不依赖于服务 I 的业务似乎不受影响。

但是，依赖服务 I 的业务请求被阻塞，用户不会得到响应，则 tomcat 的这个线程不会释放，于是越来越多的用户请求到来，越来越多的线程会阻塞

服务器支持的线程和并发数有限，请求一直阻塞，会导致服务器资源耗尽，从而导致所有其它服务都不可用，那么当前服务也就不可用了。

那么，依赖于当前服务的其它服务随着时间的推移，最终也都会变的不可用，形成级联失败，雪崩就发生了

微服务调用链路中的某个服务故障，引起整个链路中的所有微服务都不可用，这就是雪崩。

#### 雪崩问题解决方式

解决雪崩问题的常见方式有四种：

+ 超时处理：设定超时时间，请求超过一定时间没有响应就返回错误信息，不会无休止等待。如果请求速度大于释放速度最后仍会产生雪崩，只能缓解问题，不能解决问题。

+ 仓壁模式：仓壁模式来源于船舱的设计：船舱都会被隔板分离为多个独立空间，当船体破损时，只会导致部分空间进入，将故障控制在一定范围内，避免整个船体都被淹没。限定每个业务能使用的线程数，避免耗尽整个 tomcat 的资源，因此也叫线程隔离。

+ 断路器模式：由断路器统计业务执行的异常比例，如果超出阈值则会熔断该业务，拦截访问该业务的一切请求。

  断路器会统计访问某个服务的请求数量，异常比例，当发现访问服务 D 的请求异常比例过高时，认为服务 D 有导致雪崩的风险，会拦截访问服务 D 的一切请求，形成熔断。

+ 流量控制：限制业务访问的 QPS（每秒请求数量），避免服务因流量的突增而故障。

#### 雪崩问题总结

什么是雪崩问题？

微服务之间相互调用，因为调用链中的一个服务故障，引起整个链路都无法访问的情况。

雪崩问题解决方式：

+ **限流**是对服务的保护，避免因瞬间高并发流量而导致服务故障，进而避免雪崩。是一种**预防**措施。
+ **超时处理、线程隔离、降级熔断**是在部分服务故障时，将故障控制在一定范围，避免雪崩。是一种**补救**措施。

### 服务保护技术对比

在 Spring Cloud 当中支持多种服务保护技术：

- [Netfix Hystrix](https://github.com/Netflix/Hystrix)
- [Sentinel](https://github.com/alibaba/Sentinel)
- [Resilience4J](https://github.com/resilience4j/resilience4j)

早期比较流行的是 Hystrix 框架，但目前国内实用最广泛的还是阿里巴巴的 Sentinel 框架，这里我们做下对比：

|                | Sentinel                                       | Hystrix                       |
| -------------- | ---------------------------------------------- | ----------------------------- |
| 隔离策略       | 信号量隔离                                     | 线程池隔离/信号量隔离         |
| 熔断降级策略   | 基于慢调用比例或异常比例                       | 基于失败比率                  |
| 实时指标实现   | 滑动窗口                                       | 滑动窗口（基于 RxJava）       |
| 规则配置       | 支持多种数据源                                 | 支持多种数据源                |
| 扩展性         | 多个扩展点                                     | 插件的形式                    |
| 基于注解的支持 | 支持                                           | 支持                          |
| 限流           | 基于 QPS，支持基于调用关系的限流               | 有限的支持                    |
| 流量整形       | 支持慢启动、匀速排队模式                       | 不支持                        |
| 系统自适应保护 | 支持                                           | 不支持                        |
| 控制台         | 开箱即用，可配置规则、查看秒级监控、机器发现等 | 不完善                        |
| 常见框架的适配 | Servlet、Spring Cloud、Dubbo、gRPC  等         | Servlet、Spring Cloud Netflix |

### Sentinel 介绍和安装

#### Sentinel 简介

Sentinel 是阿里巴巴开源的一款微服务流量控制组件。官网地址：https://sentinelguard.io/zh-cn/index.html

Sentinel 具有以下特征:

+ 丰富的应用场景：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
+ 完备的实时监控：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
+ 广泛的开源生态：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
+ 完善的 SPI 扩展点：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

#### 安装 Sentinel

Sentinel 官方提供了UI控制台，方便我们对系统做限流设置。大家可以在 [GitHub](https://github.com/alibaba/Sentinel/releases) 下载。

将 jar 包放到任意非中文目录，执行命令：

```bash
java -jar sentinel-dashboard-1.8.1.jar
```

如果要修改 Sentinel 的默认端口、账户、密码，可以通过下列配置：

| 配置项                             | 默认值   | 说明       |
| ---------------------------------- | -------- | ---------- |
| `server.port`                      | 8080     | 服务端口   |
| `sentinel.dashboard.auth.username` | sentinel | 默认用户名 |
| `sentinel.dashboard.auth.password` | sentinel | 默认密码   |

例如，修改端口：

```sh
java -Dserver.port=8090 -jar sentinel-dashboard-1.8.1.jar
```

访问 http://localhost:8080 页面，就可以看到 Sentinel 的控制台了：

需要输入账号和密码，默认都是：sentinel

登录后，发现一片空白，什么都没有，这是因为我们还没有与微服务整合。

### 微服务整合 Sentinel

我们在 order-service 中整合 sentinel，并连接 sentinel 的控制台，步骤如下：

引入 sentinel 依赖

```xml
<!--sentinel-->
<dependency>
    <groupId>com.alibaba.cloud</groupId> 
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

配置控制台，修改 application.yaml 文件，添加下面内容：

```yaml
spring:
  cloud: 
    sentinel:
      transport:
        dashboard: sentinel:8092
```

访问 order-service 的任意端点

打开浏览器，访问 http://localhost:8088/order/101，这样才能触发 sentinel 的监控。

然后再访问 sentinel 的控制台，查看效果

## 流量控制

雪崩问题虽然有四种方案，但是限流是避免服务因突发的流量而发生故障，是对微服务雪崩问题的预防。我们先学习这种模式。

### 簇点链路

簇点链路：就是项目内的调用链路，链路中被监控的每个接口就是一个资源。

当请求进入微服务时，首先会访问 `DispatcherServlet`，然后进入 Controller、Service、Mapper，这样的一个调用链就叫做簇点链路。簇点链路中被监控的每一个接口就是一个**资源**。

默认情况下 sentinel 会监控 SpringMVC 的每一个端点（Endpoint，也就是 controller 中的方法），因此 Spring MVC 的每一个端点（Endpoint）就是调用链路中的一个资源。

例如，我们刚才访问的 order-service 中的 `OrderController` 中的端点：`/order/{orderId}`

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2bkadnwmts7w.png)

流控、熔断等都是针对簇点链路中的资源来设置的，因此我们可以点击对应资源后面的按钮来设置规则：

- 流控：流量控制
- 降级：降级熔断
- 热点：热点参数限流，是限流的一种
- 授权：请求的权限控制

### 快速入门

点击资源 `/order/{orderId}` 后面的流控按钮，就可以弹出表单。

表单中可以填写限流规则，如下：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.70ekiexb0i00.png)

其含义是限制 `/order/{orderId}` 这个资源的单机 QPS 为 1，即每秒只允许 1 次请求，超出的请求会被拦截并报错。

需求：给 `/order/{orderId}` 这个资源设置流控规则，QPS 不能超过  5，然后利用 JMeter  测试。

### 流控模式

在添加限流规则时，点击高级选项，可以选择三种流控模式：

- 直接：统计当前资源的请求，触发阈值时对当前资源直接限流，也是默认的模式
- 关联：统计与当前资源相关的另一个资源，触发阈值时，对当前资源限流
- 链路：统计从指定链路访问到本资源的请求，触发阈值时，对指定链路限流

快速入门测试的就是直接模式。

#### 关联模式

关联模式：统计与当前资源相关的另一个资源，触发阈值时，对当前资源限流

使用场景：比如用户支付时需要修改订单状态，同时用户要查询订单。查询和修改操作会争抢数据库锁，产生竞争。业务需求是有限支付和更新订单的业务，因此当修改订单业务触发阈值时，需要对查询订单业务限流。

配置规则：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2fazj95c9jgg.png)

语法说明：当 /write 资源访问量触发阈值时，就会对 /read 资源限流，避免影响 /write 资源。

需求说明：

- 在 `OrderController` 新建两个端点：/order/query 和 /order/update，无需实现业务

- 配置流控规则，当 /order/update 资源被访问的 QPS 超过 5 时，对 /order/query 请求限流

定义 /order/query 端点，模拟订单查询

```java
@GetMapping("/query")
public String queryOrder() {
    return "查询订单成功";
}
```

定义 /order/update 端点，模拟订单更新

```java
@GetMapping("/update")
public String updateOrder() {
    return "更新订单成功";
}
```

重启服务，查看 sentinel 控制台的簇点链路

配置流控规则，对哪个端点限流，就点击哪个端点后面的按钮。我们是对订单查询 /order/query 限流，因此点击它后面的按钮，在表单中填写流控规则：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.8syzbd74gco.png)

在 JMeter 测试

满足下面条件可以使用关联模式：

+ 两个有竞争关系的资源
+ 一个优先级较高，一个优先级较低

#### 链路模式

链路模式：只针对从指定链路访问到本资源的请求做统计，判断是否超过阈值。

配置示例：

例如有两条请求链路：

- /test1 → /common

- /test2 → /common

如果只希望统计从 /test2 进入到 /common 的请求，则可以这样配置：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3pgkf0xop5s0.png)

案例需求：有查询订单和创建订单业务，两者都需要查询商品。针对从查询订单进入到查询商品的请求统计，并设置限流。

步骤：

1. 在 `OrderService` 中添加一个 `queryGoods` 方法，不用实现业务

2. 在 `OrderController` 中，改造 /order/query 端点，调用 `OrderService` 中的 `queryGoods` 方法

3. 在 `OrderController` 中添加一个 /order/save 的端点，调用 `OrderService` 的 `queryGoods` 方法

4. 给 `queryGoods` 设置限流规则，从 /order/query 进入 `queryGoods` 的方法限制QPS必须小于2

添加查询商品方法，在 order-service 服务中，给 `OrderService` 类添加一个 `queryGoods` 方法：

```java
public void queryGoods(){
    System.err.println("查询商品");
}
```

查询订单时，查询商品，在 order-service 的 `OrderController` 中，修改 /order/query 端点的业务逻辑：

```java
@GetMapping("/query")
public String queryOrder() {
    // 查询商品
    orderService.queryGoods();
    // 查询订单
    System.out.println("查询订单");
    return "查询订单成功";
}
```

新增订单，查询商品，在 order-service 的 `OrderController` 中，添加 /order/save 端点，模拟新增订单：

```java
@GetMapping("/save")
public String saveOrder() {
    // 查询商品
    orderService.queryGoods();
    // 查询订单
    System.err.println("新增订单");
    return "新增订单成功";
}
```

给查询商品添加资源标记，默认情况下，`OrderService` 中的方法是不被 Sentinel 监控的，需要我们自己通过注解来标记要监控的方法。给 `OrderService` 的 `queryGoods` 方法添加 `@SentinelResource` 注解：

```java
@SentinelResource("goods")
public void queryGoods(){
    System.err.println("查询商品");
}
```

链路模式中，是对不同来源的两个链路做监控。但是 Sentinel 默认会给进入 Spring MVC 的所有请求设置同一个 root 资源，会导致链路模式失效。我们需要关闭这种对 Spring MVC 的资源聚合，修改 order-service 服务的 application.yml 文件：

```yaml
spring:
  cloud:
    sentinel:
      web-context-unify: false # 关闭context整合
```

重启服务，访问 /order/query 和 /order/save，可以查看到 Sentinel 的簇点链路规则中，出现了新的资源：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.1ed4rdp6vmm8.png)

添加流控规则，点击 goods 资源后面的流控按钮，在弹出的表单中填写下面信息：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.sax6d8udqkg.png)

只统计从 /order/query 进入 /goods 的资源，QPS 阈值为 2，超出则被限流。最后使用 JMeter 测试。

#### 流控模式总结

+ 直接：对当前资源限流
+ 关联：高优先级资源触发阈值，对低优先级资源限流。
+ 链路：阈值统计时，只统计从指定资源进入当前资源的请求，是对请求来源的限流

### 流控效果

在流控的高级选项中，还有一个流控效果选项：

流控效果是指请求达到流控阈值时应该采取的措施，包括三种：

- 快速失败：达到阈值后，新的请求会被立即拒绝并抛出 `FlowException` 异常。是默认的处理方式。

- Warm Up：预热模式，对超出阈值的请求同样是拒绝并抛出异常。但这种模式阈值会动态变化，从一个较小值逐渐增加到最大阈值。

- 排队等待：让所有的请求按照先后次序排队执行，两个请求的间隔不能小于指定时长

#### Warm Up

阈值一般是一个微服务能承担的最大 QPS，但是一个服务刚刚启动时，一切资源尚未初始化（冷启动），如果直接将 QPS 跑到最大值，可能导致服务瞬间宕机。

Warm Up 也叫预热模式，是应对服务冷启动的一种方案。请求阈值初始值是 `maxThreshold / coldFactor`，持续指定时长后，逐渐提高到 `maxThreshold` 值。而 `coldFactor` 的默认值是 3 。

例如，我设置 QPS 的 `maxThreshold` 为 10，预热时间为 5 秒，那么初始阈值就是 10 / 3 ，也就是 3，然后在 5 秒后逐渐增长到 10。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.578rh9uf2hs0.png)

案例需求：给 /order/{orderId} 这个资源设置限流，最大 QPS 为 10，利用 Warm Up 效果，预热时长为 5 秒

#### 排队等待

当请求超过 QPS 阈值时，快速失败和 Warm Up 会拒绝新的请求并抛出异常。

而排队等待则是让所有请求进入一个队列中，然后按照阈值允许的时间间隔依次执行。后来的请求必须等待前面执行完成，如果请求预期的等待时间超出最大时长，则会被拒绝。

工作原理

例如：QPS = 5，意味着每 200ms 处理一个队列中的请求；timeout = 2000，意味着预期等待时长超过 2000ms 的请求会被拒绝并抛出异常。

那什么叫做预期等待时长呢？

比如现在一下子来了 12 个请求，因为每 200ms 执行一个请求，那么：

- 第 6 个请求的**预期等待时长** =  200 * (6 - 1) = 1000ms
- 第 12 个请求的预期等待时长 = 200 * (12-1) = 2200ms

现在，第 1 秒同时接收到 10 个请求，但第 2 秒只有 1 个请求，此时 QPS 的曲线这样的：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.41n82oeep1g0.png)

如果使用队列模式做流控，所有进入的请求都要排队，以固定的 200ms 的间隔执行，QPS 会变的很平滑

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.6aqz6pt0zqo0.png)

平滑的 QPS 曲线，对于服务器来说是更友好的。

案例需求：给 /order/{orderId} 这个资源设置限流，最大 QPS 为 10，利用排队的流控效果，超时时长设置为 5s

#### 流控效果总结

- 快速失败：QPS 超过阈值时，拒绝新的请求

- Warm Up： QPS 超过阈值时，拒绝新的请求；QPS 阈值是逐渐提升的，可以避免冷启动时高并发导致服务宕机。

- 排队等待：请求会进入队列，按照阈值允许的时间间隔依次执行请求；如果请求预期等待时长大于超时时间，直接拒绝

### 热点参数限流

之前的限流是统计访问某个资源的所有请求，判断是否超过 QPS 阈值。而热点参数限流是分别统计参数值相同的请求，判断是否超过 QPS 阈值。

#### 全局参数限流

例如，一个根据 id 查询商品的接口，访问 /goods/{id} 的请求中，id 参数值会有变化，热点参数限流会根据**参数值**分别统计 QPS，统计结果：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.44ql4uo7cfi0.png)

当 id=1 的请求触发阈值被限流时，id 值不为 1 的请求不受影响。

配置示例：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.qkaqtkxdxio.png)

代表的含义是：对 hot 这个资源的 0 号参数（第一个参数）做统计，每 1 秒相同参数值的请求数不能超过 5

#### 热点参数限流

刚才的配置中，对查询商品这个接口的所有商品一视同仁，QPS 都限定为 5。

而在实际开发中，可能部分商品是热点商品，例如秒杀商品，我们希望这部分商品的 QPS 限制与其它商品不一样，高一些。那就需要配置热点参数限流的高级选项了：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.6bhk98olbio0.png)

结合上一个配置，这里的含义是对 0 号的 long 类型参数限流，每 1 秒相同参数的 QPS 不能超过 5，有两个例外：

+ 如果参数值是 100，则每 1 秒允许的 QPS 为 10
+ 如果参数值是 101，则每 1 秒允许的 QPS 为 15

#### 示范案例

案例需求：给 /order/{orderId} 这个资源添加热点参数限流，规则如下：

+ 默认的热点参数规则是每 1 秒请求量不超过 2
+ 给 102 这个参数设置例外：每 1 秒请求量不超过 4
+ 给 103 这个参数设置例外：每 1 秒请求量不超过 10

注意事项：热点参数限流对默认的 Spring MVC 资源无效，需要利用 `@SentinelResource` 注解标记资源

标记资源，给 order-service 中的 `OrderController` 中的 /order/{orderId} 资源添加注解： 

```java
@SentinelResource("hot")
@GetMapping("{orderId}")
public Order queryOrderByUserId(@PathVariable("orderId") Long orderId) {
    // 根据 id 查询订单并返回
    return orderService.queryOrderById(orderId);
}
```

热点参数限流规则，访问该接口，可以看到我们标记的 hot 资源出现了，这里不要点击 hot 后面的按钮，页面有Bug，没有高级选项，点击左侧菜单中热点规则菜单：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.7dbpozbwj1s0.png)

点击新增，填写表单：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.62qkqxkatd40.png)

## 隔离和降级

限流是一种预防措施，虽然限流可以尽量避免因高并发而引起的服务故障，但服务还会因为其它原因而故障。

而要将这些故障控制在一定范围，避免雪崩，就要靠线程隔离（舱壁模式）和熔断降级手段了。

线程隔离之前讲到过：调用者在调用服务提供者时，给每个调用的请求分配独立线程池，出现故障时，最多消耗这个线程池内资源，避免把调用者的所有资源耗尽。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4hs9dg5qave0.png)

熔断降级：是在调用方这边加入断路器，统计对服务提供者的调用，如果调用的失败比例过高，则熔断该业务，不允许访问该服务的提供者了。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4m9qos5euys0.png)

可以看到，不管是线程隔离还是熔断降级，都是对客户端（调用方）的保护。需要在**调用方** 发起远程调用时做线程隔离、或者服务熔断。

而我们的微服务远程调用都是基于 Feign 来完成的，因此我们需要将 Feign 与 Sentinel 整合，在 Feign 里面实现线程隔离和服务熔断。

### Feign Client 整合 Sentinel

Spring Cloud 中，微服务调用都是通过 Feign 来实现的，因此做客户端保护必须整合 Feign 和 Sentinel。

#### 修改 Feign 配置开启 Sentinel 功能

修改 `OrderService` 的 application.yml 文件，开启 Feign 的 Sentinel 功能：

```yaml
feign:
  sentinel:
    enabled: true # 开启feign对sentinel的支持
```

#### 编写失败降级逻辑

业务失败后，不能直接报错，而应该返回用户一个友好提示或者默认结果，这个就是失败降级逻辑。

给 Feign Client 编写失败后的降级逻辑，有两种实现方式：

+  `FallbackClass`，无法对远程调用的异常做处理
+ `FallbackFactory`，可以对远程调用的异常做处理（我们选择这种）

步骤一：在 feign-api 项目中定义类，实现 `FallbackFactory`：

```java
package cn.itcast.feign.clients.fallback;

import cn.itcast.feign.clients.UserClient;
import cn.itcast.feign.pojo.User;
import feign.hystrix.FallbackFactory;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class UserClientFallbackFactory implements FallbackFactory<UserClient> {
    @Override
    public UserClient create(Throwable throwable) {
        return new UserClient() {
            @Override
            public User findById(Long id) {
                log.error("查询用户异常", throwable);
                return new User();
            }
        };
    }
}
```

步骤二：在 feign-api 项目中的 `DefaultFeignConfiguration` 类中将 `UserClientFallbackFactory` 注册为一个 Bean：

```java
@Bean
public UserClientFallbackFactory userClientFallbackFactory(){
    return new UserClientFallbackFactory();
}
```

**步骤三**：在 feign-api 项目中的 UserClient 接口中使用 `UserClientFallbackFactory`：

```java
@FeignClient(value = "userservice", fallbackFactory = UserClientFallbackFactory.class)
public interface UserClient {

    @GetMapping("/user/{id}")
    User findById(@PathVariable("id") Long id);
}
```

#### Feign Client 整合 Sentinel 总结

Sentinel支持的雪崩解决方案：

- 线程隔离（仓壁模式）
- 降级熔断

Feign 整合 Sentinel 的步骤：

- 在application.yml中配置：`feign.sentienl.enable=true`
- 给 FeignClient 编写 `FallbackFactory` 并注册为Bean
- 将 `FallbackFactory` 配置到 FeignClient

### 线程隔离（舱壁模式）

#### 线程隔离的实现方式

线程隔离有两种方式实现：

- 线程池隔离

- 信号量隔离（Sentinel 默认采用）

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.7cx1j6s3zxk0.png)

线程池隔离：给每个服务调用业务分配一个线程池，利用线程池本身实现隔离效果

+ 优点：支持主动超时，支持异步调用
+ 缺点：线程的额外开销比较大
+ 场景：低扇出（Fanout）

信号量隔离：不创建线程池，而是计数器模式，记录业务使用的线程数量，达到信号量上限时，禁止新的请求。

+ 优点：轻量级，无额外开销
+ 缺点：不支持主动超时，不支持异步调用
+ 场景：高频调用，高扇出

#### Sentinel 的线程隔离

在添加限流规则时，可以选择两种阈值类型：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.xp3c06ky0cg.png)

- QPS：就是每秒的请求数，在快速入门中已经演示过

- 线程数：是该资源能使用用的 tomcat 线程数的最大值。也就是通过限制线程数量，实现**线程隔离**（舱壁模式）。

案例需求：给 order-service 服务中的 UserClient 的查询用户接口设置流控规则，线程数不能超过 2。然后利用 JMeter 测试。

### 熔断降级

熔断降级是解决雪崩问题的重要手段。其思路是由断路器统计服务调用的异常比例、慢请求比例，如果超出阈值则会熔断该服务。即拦截访问该服务的一切请求；而当服务恢复时，断路器会放行访问该服务的请求。

断路器控制熔断和放行是通过状态机来完成的：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.6y5xecv9u000.png)

状态机包括三个状态：

- closed：关闭状态，断路器放行所有请求，并开始统计异常比例、慢请求比例。超过阈值则切换到 open 状态
- open：打开状态，服务调用被**熔断**，访问被熔断服务的请求会被拒绝，快速失败，直接走降级逻辑。open 状态 5 秒后会进入 half-open 状态
- half-open：半开状态，放行一次请求，根据执行结果来判断接下来的操作。
  - 请求成功：则切换到 closed 状态
  - 请求失败：则切换到 open 状态

断路器熔断策略有三种：慢调用、异常比例、异常数

#### 慢调用比例

慢调用比例：业务的响应时长（RT）大于指定时长的请求认定为慢调用请求。在指定时间内，如果请求数量超过设定的最小数量，慢调用比例大于设定的阈值，则触发熔断。例如：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2cyb37owkqtc.png)

解读：RT 超过 500ms 的调用是慢调用，统计最近 10000ms 内的请求，如果请求量超过 10 次，并且慢调用比例不低于 0.5，则触发熔断，熔断时长为 5 秒。然后进入 half-open 状态，放行一次请求做测试。

案例需求：给 UserClient 的查询用户接口设置降级规则，慢调用的 RT 阈值为 50ms ，统计时间为 1 秒，最小请求数量为 5，失败阈值比例为 0.4，熔断时长为 5

设置慢调用，修改 user-service 中的 /user/{id} 这个接口的业务。通过休眠模拟一个延迟时间：

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id) throws InterruptedException {
    if (id == 1) {
        // id 为 1 时触发慢调用
        Thread.sleep(60);
    }
    return userService.queryById(id);
}
```

设置熔断规则，下面，给 feign 接口设置降级规则：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.cnbag9i3h2w.png)

超过 50ms 的请求都会被认为是慢请求

#### 异常比例、异常数

异常比例或异常数：统计指定时间内的调用，如果调用次数超过指定请求数，并且出现异常的比例达到设定的比例阈值（或超过指定异常数），则触发熔断。

例如，一个异常比例设置：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.62u0t9cfx4c0.png)

解读：统计最近 1000ms 内的请求，如果请求量超过 10 次，并且异常比例不低于 0.4，则触发熔断。

一个异常数设置：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5l93gd0ccps0.png)

解读：统计最近 1000ms 内的请求，如果请求量超过 10 次，并且异常比例不低于 2 次，则触发熔断。

案例需求：给 UserClient 的查询用户接口设置降级规则，统计时间为 1 秒，最小请求数量为 5，失败阈值比例为 0.4，熔断时长为 5s

设置异常请求，首先，修改 user-service 中的 /user/{id} 这个接口的业务。手动抛出异常，以触发异常比例的熔断：

```java
@GetMapping("/{id}")
public User queryById(@PathVariable("id") Long id) throws InterruptedException {
    if (id == 1) {
        // id 为 1 时触发慢调用
        Thread.sleep(100);
    } else if (id == 2) {
        throw new RuntimeException("故意出错，触发熔断");
    }
    return userService.queryById(id);
}
```

设置熔断规则，下面给 feign 接口设置降级规则：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.ovuaoitoy0w.png)

在 5 次请求中，只要异常比例超过 0.4，也就是有 2 次以上的异常，就会触发熔断。

## 授权规则

默认情况下，发生限流、降级、授权拦截时，都会抛出异常到调用方。异常结果都是 flow limmiting（限流）。这样不够友好，无法得知是限流还是降级还是授权拦截。

### 基本规则

授权规则可以对调用方的来源做控制，有白名单和黑名单两种方式。

- 白名单：来源（origin）在白名单内的调用者允许访问

- 黑名单：来源（origin）在黑名单内的调用者不允许访问

点击左侧菜单的授权，可以看到授权规则：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.1dwl90cub8zk.png)

- 资源名：就是受保护的资源，例如 /order/{orderId}

- 流控应用：是来源者的名单，
  - 如果是勾选白名单，则名单中的来源被许可访问。
  - 如果是勾选黑名单，则名单中的来源被禁止访问。

我们允许请求从 gateway 到 order-service，不允许浏览器访问 order-service，那么白名单中就要填写网关的来源名称（origin）。

### 如何获取 origin

Sentinel 是通过 `RequestOriginParser` 这个接口的 `parseOrigin` 来获取请求的来源的。

```java
public interface RequestOriginParser {
    /**
     * 从请求request对象中获取origin，获取方式自定义
     */
    String parseOrigin(HttpServletRequest request);
}
```

这个方法的作用就是从 request 对象中，获取请求者的 origin 值并返回。

默认情况下，sentinel 不管请求者从哪里来，返回值永远是 default，也就是说一切请求的来源都被认为是一样的值 default。

因此，我们需要自定义这个接口的实现，让不同的请求，返回不同的 origin。

例如 order-service 服务中，我们定义一个 `RequestOriginParser` 的实现类：

```java
@Component
public class HeaderOriginParser implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest request) {
        // 1.获取请求头
        String origin = request.getHeader("origin");
        // 2.非空判断
        if (StringUtils.isEmpty(origin)) {
            origin = "blank";
        }
        return origin;
    }
}
```

我们会尝试从 request-header 中获取 origin 值。

### 给网关添加请求头

既然获取请求 origin 的方式是从 reques-header 中获取 origin 值，我们必须让所有从 gateway 路由到微服务的请求都带上origin头。

这个需要利用之前学习的一个 GatewayFilter 来实现，`AddRequestHeaderGatewayFilter`。

修改 gateway 服务中的 application.yml，添加一个 defaultFilter：

```yaml
spring:
  cloud:
    gateway:
      default-filters:
        - AddRequestHeader=origin,gateway
      routes:
       # ...略
```

这样，从 gateway 路由的所有请求都会带上 origin 头，值为 gateway。而从其它地方到达微服务的请求则没有这个头。

### 配置授权规则

接下来，我们添加一个授权规则，放行 origin 值为 gateway 的请求。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.422hi4klpiq0.png)

### 自定义异常结果

默认情况下，发生限流、降级、授权拦截时，都会抛出异常到调用方。异常结果都是 flow limmiting（限流）。这样不够友好，无法得知是限流还是降级还是授权拦截。

#### 异常类型

而如果要自定义异常时的返回结果，需要实现 `BlockExceptionHandler` 接口：

```java
public interface BlockExceptionHandler {
    /**
     * 处理请求被限流、降级、授权拦截时抛出的异常：BlockException
     */
    void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception;
}
```

这个方法有三个参数：

- `HttpServletRequest request`：request 对象
- `HttpServletResponse response`：response 对象
- `BlockException e`：被 sentinel 拦截时抛出的异常

这里的 `BlockException` 包含多个不同的子类：

| 异常                   | 说明               |
| ---------------------- | ------------------ |
| `FlowException`        | 限流异常           |
| `ParamFlowException`   | 热点参数限流的异常 |
| `DegradeException`     | 降级异常           |
| `AuthorityException`   | 授权规则异常       |
| `SystemBlockException` | 系统规则异常       |

#### 自定义异常处理

```java
@Component
public class SentinelExceptionHandler implements BlockExceptionHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception {
        String msg = "未知异常";
        int status = 429;

        if (e instanceof FlowException) {
            msg = "请求被限流了";
        } else if (e instanceof ParamFlowException) {
            msg = "请求被热点参数限流";
        } else if (e instanceof DegradeException) {
            msg = "请求被降级了";
        } else if (e instanceof AuthorityException) {
            msg = "没有权限访问";
            status = 401;
        }

        response.setContentType("application/json;charset=utf-8");
        response.setStatus(status);
        response.getWriter().println("{\"msg\": " + msg + ", \"status\": " + status + "}");
    }
}
```

重启测试，在不同场景下，会返回不同的异常消息。

## 规则持久化

现在，sentinel 的所有规则都是内存存储，重启后所有规则都会丢失。在生产环境下，我们必须确保这些规则的持久化，避免丢失。

### 规则管理模式

规则是否能持久化，取决于规则管理模式，Sentinel 支持三种规则管理模式：

- 原始模式：Sentinel 的默认模式，将规则保存在内存，重启服务会丢失。
- pull 模式
- push 模式

#### pull 模式

pull 模式：控制台将配置的规则推送到 Sentinel 客户端，而客户端会将配置规则保存在本地文件或数据库中。以后会定时去本地文件或数据库中查询，更新本地规则。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5260ibjzz300.png)

存在时效性问题，客户端之间规则不统一

#### push 模式

push 模式：控制台将配置规则推送到远程配置中心，例如 Nacos。Sentinel 客户端监听 Nacos，获取配置变更的推送消息，完成本地配置更新。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3xqps4wiyim0.png)

### 实现 push 模式

#### 修改 order-service 服务

引入依赖，在 order-service 中引入 sentinel 监听 nacos 的依赖：

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

配置 nacos 地址，在 order-service 中的 application.yml 文件配置 nacos 地址及监听的配置信息：

```yaml
spring:
  cloud:
    sentinel:
      datasource:
        flow:
          nacos:
            server-addr: halo:8848 # nacos地址
            dataId: orderservice-flow-rules
            groupId: SENTINEL_GROUP
            rule-type: flow # 还可以是：degrade、authority、param-flow
```

#### 修改 sentinel-dashboard 源码

修改完成后，重新打包运行

```sh
java -jar -Dnacos.addr=halo:8848 -DServer.port=8090 sentinel-dashboard.jar
```

