---
title: Seata入门
create: 2021-09-13
data: 2021-09-13
update: 2021-09-15
---

## 分布式事务理论基础

事务的 ACID 原则：

+ A 原子性： 事务中的所有操作，要么全部成功，要么全部失败
+ C 一致性：要保证数据库内部完整性约束、声明性约束
+ I 隔离性：对同一资源操作的事务不能同时发生
+ D 持久性：对数据库做的一切修改将永久保存，不管是否出现故障

分布式服务的事务问题：在分布式系统下，一个业务跨越多个服务或数据源，每个服务都是一个分支事务，要保证所有分支事务最终状态一致，这样的事务就是分布式事务。

### CAP 定理

1998 年，加州大学的计算机科学家 Eric Brewer 提出，分布式系统有三个指标：

+ Consistency（一致性）：用户访问分布式系统中的任意节点，得到的数据必须一致
+ Availability（可用性）：用户访问集群中的任意健康节点，必须能得到响应，而不是超时或拒绝
+ Partition tolerance （分区容错性）：
  + Partition（分区）：因为网络故障或其它原因导致分布式系统中的部分节点与其它节点失去连接，形成独立分区。
  + Tolerance（容错）：在集群出现分区时，整个系统也要持续对外提供服务

Eric Brewer 认为，分布式系统无法同时满足这三个指标。这个结论就叫做 CAP 定理。

分布式系统节点通过网络连接，一定会出现分区问题（P）当分区出现时，系统的一致性（C）和可用性（A）就无法同时满足

思考：ElasticSearch 集群是 CP 还是 AP？

ES 集群出现分区时，故障节点会被剔除集群，数据分片会重新分配到其它节点，保证数据一致。因此是低可用性，高一致性，属于 CP。

### BASE 理论

BASE 理论是对 CAP 的一种解决思路，包含三个思想：

+ Basically Available （基本可用）：分布式系统在出现故障时，允许损失部分可用性，即保证核心可用。
+ Soft State（软状态）：在一定时间内，允许出现中间状态，比如临时的不一致状态。
+ Eventually Consistent（最终一致性）：虽然无法保证强一致性，但是在软状态结束后，最终达到数据一致。

### 分布式系统理论基础小结

而分布式事务最大的问题是各个子事务的一致性问题，因此可以借鉴 CAP 定理和 BASE 理论：

+ AP 模式：各子事务分别执行和提交，允许出现结果不一致，然后采用弥补措施恢复数据即可，实现最终一致。
+ CP 模式：各个子事务执行后互相等待，同时提交，同时回滚，达成强一致。但事务等待过程中，处于弱可用状态。

解决分布式事务，各个子系统之间必须能感知到彼此的事务状态，才能保证状态一致，因此需要一个事务协调者来协调每一个事务的参与者（子系统事务）。

这里的子系统事务，称为分支事务；有关联的各个分支事务在一起称为全局事务

解决分布式事务的思想和模型：

+ 全局事务：整个分布式事务
+ 分支事务：分布式事务中包含的每个子系统的事务
+ 最终一致思想：各分支事务分别执行并提交，如果有不一致的情况，再想办法恢复数据
+ 强一致思想：各分支事务执行完业务不要提交，等待彼此结果。而后统一提交或回滚

## 初识 Seata

Seata 是 2019 年 1 月份蚂蚁金服和阿里巴巴共同开源的分布式事务解决方案。致力于提供高性能和简单易用的分布式事务服务，为用户打造一站式的分布式解决方案。

官网地址：http://seata.io/，其中的文档、博客中提供了大量的使用说明、源码分析。

### Seata 架构

Seata 事务管理中有三个重要的角色：

+ TC（Transaction Coordinator）：事务协调者：维护全局和分支事务的状态，协调全局事务提交或回滚。
+ TM（Transaction Manager）：事务管理器：定义全局事务的范围、开始全局事务、提交或回滚全局事务。
+ RM（Resource Manager）：资源管理器：管理分支事务处理的资源，与 TC 交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

![Seata架构](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Seata架构.i269crb2d34.svg)

Seata 提供了四种不同的分布式事务解决方案：

+ XA 模式：强一致性分阶段事务模式，牺牲了一定的可用性，无业务侵入
+ TCC 模式：最终一致的分阶段事务模式，有业务侵入
+ AT 模式：最终一致的分阶段事务模式，无业务侵入，也是 Seata 的默认模式
+ SAGA模式：长事务模式，有业务侵入

### 部署 TC 服务

#### 下载解压配置

首先我们要下载 seata-server 包，地址在 [http](http://seata.io/zh-cn/blog/download.html)[://seata.io/zh-cn/blog/download](http://seata.io/zh-cn/blog/download.html)[.](http://seata.io/zh-cn/blog/download.html)[html](http://seata.io/zh-cn/blog/download.html) 

下载完成后解压，修改配置 conf 目录下的 registry.conf 文件：

```properties
registry {
  # tc服务的注册中心类，这里选择nacos，也可以是eureka、zookeeper等
  type = "nacos"

  nacos {
    # seata tc 服务注册到 nacos的服务名称，可以自定义
    application = "seata-tc-server"
    serverAddr = "nacos:8848"
    group = "DEFAULT_GROUP"
    namespace = ""
    cluster = "HZ"
    username = "nacos"
    password = "nacos"
  }
}

config {
  # 读取tc服务端的配置文件的方式，这里是从nacos配置中心读取，这样如果tc是集群，可以共享配置
  type = "nacos"
  # 配置nacos地址等信息
  nacos {
    serverAddr = "nacos:8848"
    namespace = ""
    group = "SEATA_GROUP"
    username = "nacos"
    password = "nacos"
    dataId = "seataServer.properties"
  }
}
```

#### 在 Nacos 添加配置

特别注意，为了让 TC 服务的集群可以共享配置，我们选择了 Nacos 作为统一配置中心。因此服务端配置文件 seataServer.properties 文件需要在 Nacos 中配好。

配置内容如下：

```properties
# 数据存储方式，db代表数据库
store.mode=db
store.db.datasource=druid
store.db.dbType=mysql
store.db.driverClassName=com.mysql.jdbc.Driver
store.db.url=jdbc:mysql://mysql:3306/seata?useUnicode=true&rewriteBatchedStatements=true
store.db.user=root
store.db.password=mogu2018
store.db.minConn=5
store.db.maxConn=30
store.db.globalTable=global_table
store.db.branchTable=branch_table
store.db.queryLimit=100
store.db.lockTable=lock_table
store.db.maxWait=5000
# 事务、日志等配置
server.recovery.committingRetryPeriod=1000
server.recovery.asynCommittingRetryPeriod=1000
server.recovery.rollbackingRetryPeriod=1000
server.recovery.timeoutRetryPeriod=1000
server.maxCommitRetryTimeout=-1
server.maxRollbackRetryTimeout=-1
server.rollbackRetryTimeoutUnlockEnable=false
server.undo.logSaveDays=7
server.undo.logDeletePeriod=86400000

# 客户端与服务端传输方式
transport.serialization=seata
transport.compressor=none
# 关闭metrics功能，提高性能
metrics.enabled=false
metrics.registryType=compact
metrics.exporterList=prometheus
metrics.exporterPrometheusPort=9898
```

> 其中的数据库地址、用户名、密码都需要修改成你自己的数据库信息。可能需要删除注释和空行

#### 创建数据库表

特别注意：TC 服务在管理分布式事务时，需要记录事务相关数据到数据库中，你需要提前创建好这些表。

这些表主要记录全局事务、分支事务、全局锁信息：

```sql
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- 分支事务表
-- ----------------------------
DROP TABLE IF EXISTS `branch_table`;
CREATE TABLE `branch_table`  (
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `transaction_id` bigint(20) NULL DEFAULT NULL,
  `resource_group_id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `resource_id` varchar(256) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `branch_type` varchar(8) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `status` tinyint(4) NULL DEFAULT NULL,
  `client_id` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `application_data` varchar(2000) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `gmt_create` datetime(6) NULL DEFAULT NULL,
  `gmt_modified` datetime(6) NULL DEFAULT NULL,
  PRIMARY KEY (`branch_id`) USING BTREE,
  INDEX `idx_xid`(`xid`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- 全局事务表
-- ----------------------------
DROP TABLE IF EXISTS `global_table`;
CREATE TABLE `global_table`  (
  `xid` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `transaction_id` bigint(20) NULL DEFAULT NULL,
  `status` tinyint(4) NOT NULL,
  `application_id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `transaction_service_group` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `transaction_name` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `timeout` int(11) NULL DEFAULT NULL,
  `begin_time` bigint(20) NULL DEFAULT NULL,
  `application_data` varchar(2000) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `gmt_create` datetime NULL DEFAULT NULL,
  `gmt_modified` datetime NULL DEFAULT NULL,
  PRIMARY KEY (`xid`) USING BTREE,
  INDEX `idx_gmt_modified_status`(`gmt_modified`, `status`) USING BTREE,
  INDEX `idx_transaction_id`(`transaction_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

SET FOREIGN_KEY_CHECKS = 1;
```

#### 启动 TC 服务

进入 bin 目录，运行其中的 seata-server.bat 即可，启动成功后，seata-server 应该已经注册到 nacos 注册中心了。

打开浏览器，访问 nacos 地址：http://localhost:8848，然后进入服务列表页面，可以看到 seata-tc-server 的信息

#### 微服务集成 Seata

首先，引入 Seata 相关依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    <exclusions>
        <!--版本较低，1.3.0，因此排除-->
        <exclusion>
            <artifactId>seata-spring-boot-starter</artifactId>
            <groupId>io.seata</groupId>
        </exclusion>
    </exclusions>
</dependency>
<!--seata starter 采用1.4.2版本-->
<dependency>
    <groupId>io.seata</groupId>
    <artifactId>seata-spring-boot-starter</artifactId>
    <version>${seata.version}</version>
</dependency>
```

然后，配置 application.yml，让微服务通过注册中心找到 seata-tc-server

```yaml
seata:
  registry: # TC服务注册中心的配置，微服务根据这些信息去注册中心获取 tc 服务地址
    # 参考 tc 服务自己的 registry.conf 中的配置，
    # 包括：地址、namespace、group、application-name 、cluster
    type: nacos
    nacos: # tc
      server-addr: nacos:8848
      namespace: "45e30304-1b64-4c21-8c83-22309949af10"
      group: DEFAULT_GROUP
      application: seata-tc-server # TC 服务在 nacos 中的服务名称
      username: nacos
      password: nacos
  tx-service-group: seata-demo # 事务组，根据这个获取 TC 服务的 cluster 名称
  service:
    vgroup-mapping: # 事务组与 TC 服务 cluster 的映射关系
      seata-demo: HZ
```

## 使用 Seata

### XA 模式

#### XA 规范

XA 规范 是 X/Open 组织定义的分布式事务处理（DTP，Distributed Transaction Processing）标准，XA 规范 描述了全局的 TM 与局部的 RM 之间的接口，几乎所有主流的数据库都对 XA 规范 提供了支持。

![XA原理](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/XA原理.62tx9ng7qqc0.svg)

#### Seata 的 XA 模式

Seata 的 XA 模式做了一些调整，但大体相似：

RM 一阶段的工作：

+ 注册分支事务到 TC
+ 执行分支业务 SQL 但不提交
+ 报告执行状态到 TC

TC 二阶段的工作：

+ TC 检测各分支事务执行状态
+ 如果都成功，通知所有 RM 提交事务
+ 如果有失败，通知所有 RM 回滚事务

RM 二阶段的工作：

+ 接收 TC 指令，提交或回滚事务

![Seata的XA模式](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Seata的XA模式.5kzn5v50ro40.svg)

XA 模式的优点：

+ 事务的强一致性，满足 ACID 原则。
+ 常用数据库都支持，实现简单，并且没有代码侵入

XA 模式的缺点：

+ 因为一阶段需要锁定数据库资源，等待二阶段结束才释放，性能较差
+ 依赖关系型数据库实现事务

#### Seata 实现 XA 模式

Seata 的 starter 已经完成了 XA 模式的自动装配，实现非常简单，步骤如下：

修改 application.yml 文件（每个参与事务的微服务），开启 XA 模式：

```yaml
seata:
  data-source-proxy-mode: XA # 开启数据源代理的XA模式
```

给发起全局事务的入口方法添加 `@GlobalTransactional` 注解，本例中是`SeataOrderServiceImpl` 中的 `create` 方法：

```java
@Override
@Transactional
@GlobalTransactional
public Long create(SeataOrder order) {
    // 创建订单
    orderMapper.insert(order);
    try {
        // 扣用户余额
        accountClient.deduct(order.getUserId(), order.getMoney());
        // 扣库存
        storageClient.deduct(order.getCommodityCode(), order.getCount());

    } catch (FeignException e) {
        log.error("下单失败，原因:{}", e.contentUTF8(), e);
        throw new RuntimeException(e.contentUTF8(), e);
    }
    return order.getId();
}
```

重启服务并测试

### AT 模式

#### AT 模式介绍

AT 模式同样是分阶段提交的事务模型，不过缺弥补了 XA 模型中资源锁定周期过长的缺陷。

![AT模式](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/AT模式.7bkzptrwvm40.svg)

阶段一 RM 的工作：

+ 注册分支事务
+ 记录 undo-log（数据快照）
+ 执行业务 SQL 并提交
+ 报告事务状态

阶段二提交时 RM 的工作：

+ 删除 undo-log 即可

阶段二回滚时 RM 的工作：

+ 根据 undo-log 恢复数据到更新前

简述 AT 模式与 XA 模式最大的区别是什么？

+ XA 模式一阶段不提交事务，锁定资源；AT 模式一阶段直接提交，不锁定资源。
+ XA 模式依赖数据库机制实现回滚；AT 模式利用数据快照实现数据回滚。
+ XA 模式强一致；AT 模式最终一致

#### AT 模式的脏写问题

因为事务之间没有隔离，存在脏写问题

![AT模式的脏写问题](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/AT模式的脏写问题.2wajoxbl9sa0.svg)

解决方法：

全局锁：由 TC 记录当前正在操作某行数据的事务，该事务持有全局锁，具备执行权。

![AT模式的写隔离](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/AT模式的写隔离.5nlh9kqblx00.svg)

对于非 Seata 管理的业务

![AT模式对于非Seata管理的全局事务](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/AT模式对于非Seata管理的全局事务.4f39u8p9n5i0.svg)

AT 模式的优点：

+ 一阶段完成直接提交事务，释放数据库资源，性能比较好
+ 利用全局锁实现读写隔离
+ 没有代码侵入，框架自动完成回滚和提交

AT 模式的缺点：

+ 两阶段之间属于软状态，属于最终一致
+ 框架的快照功能会影响性能，但比 XA 模式要好很多

#### Seata 实现 AT 模式

AT 模式中的快照生成、回滚等动作都是由框架自动完成，没有任何代码侵入，因此实现非常简单。

1. 导入 SQL 文件：[seata-at.sql](https://github.com/Lanqilu/HaloSpringCloud/blob/master/seata-demo/db/seata-at.sql)，其中 lock_table 导入到 TC 服务关联的数据库，undo_log 表导入到微服务关联的数据库

2. 修改 application.yml 文件，将事务模式修改为 AT 模式即可

   ```yaml
   seata:
     data-source-proxy-mode: AT # 开启数据源代理的AT模式
   ```

3. 服务添加 `@GlobalTransactional` 注解

4. 重启服务并测试

### TCC 模式

#### TCC 模式介绍

TCC 模式与 AT 模式非常相似，每阶段都是独立事务，不同的是 TCC 通过人工编码来实现数据恢复。需要实现三个方法：

+ Try：资源的检测和预留；
+ Confirm：完成资源操作业务；要求 Try 成功 Confirm 一定要能成功。
+ Cancel：预留资源释放，可以理解为 Try 的反向操作。

举例，一个扣减用户余额的业务。假设账户 A 原来余额是 100，需要余额扣减 30 元。

+ 阶段一（ Try ）：检查余额是否充足，如果充足则冻结金额增加 30 元，可用余额扣除 30
+ 阶段二：假如要提交（Confirm），则冻结金额扣减 30
+ 阶段二：如果要回滚（Cancel），则冻结金额扣减 30，可用余额增加 30

![TCC模式](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/TCC模式.5947loh7hrc0.svg)

TCC 模式的每个阶段是做什么的？

+ Try：资源检查和预留
+ Confirm：业务执行和提交
+ Cancel：预留资源的释放

TCC 的优点是什么？

+ 一阶段完成直接提交事务，释放数据库资源，性能好
+ 相比 AT 模型，无需生成快照，无需使用全局锁，性能最强
+ 不依赖数据库事务，而是依赖补偿操作，可以用于非事务型数据库

TCC 的缺点是什么？

+ 有代码侵入，需要人为编写 Try、Confirm 和 Cancel 接口，太麻烦
+ 软状态，事务是最终一致

需要考虑 Confirm 和 Cancel 的失败情况，做好幂等处理

#### 实现 TCC 模式

改造 account-service 服务，利用 TCC 实现分布式事务

需求如下：

+ 修改 account-service，编写 try、confirm、cancel 逻辑
+ try 业务：添加冻结金额，扣减可用金额
+ confirm 业务：删除冻结金额
+ cancel 业务：删除冻结金额，恢复可用金额
+ 保证 confirm、cancel 接口的幂等性（重复调用效果相同）
+ 允许空回滚
+ 拒绝业务悬挂

TCC 的空回滚和业务悬挂

+ 当某分支事务的 try 阶段阻塞时，可能导致全局事务超时而触发二阶段的 cancel 操作。在未执行 try 操作时先执行了 cancel 操作，这时 cancel 不能做回滚，就是空回滚。
+ 对于已经空回滚的业务，如果以后继续执行 try，就永远不可能 confirm 或 cancel，这就是业务悬挂。应当阻止执行空回滚后的 try 操作，避免悬挂。

为了实现空回滚、防止业务悬挂，以及幂等性要求。我们必须在数据库记录冻结金额的同时，记录当前事务 id 和执行状态，为此我们设计了一张表：

```sql
CREATE TABLE `account_freeze_tbl` (
  `xid` varchar(128) NOT NULL,
  `user_id` varchar(255) DEFAULT NULL COMMENT '用户id',
  `freeze_money` int(11) unsigned DEFAULT '0' COMMENT '冻结金额',
  `state` int(1) DEFAULT NULL COMMENT '事务状态，0:try，1:confirm，2:cancel',
  PRIMARY KEY (`xid`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ROW_FORMAT=COMPACT;
```

+ Try 业务：记录冻结金额和事务状态到 account_freeze 表，扣减 account 表可用金额
+ Confirm 业务：根据 xid 删除 account_freeze 表的冻结记录
+ Cancel 业务：修改 account_freeze 表，冻结金额为 0，state 为 2；修改 account 表，恢复可用金额
+ 如何判断是否空回滚：Cancel 业务中，根据 xid 查询 account_freeze，如果为 null 则说明 Try 还没做，需要空回滚
+ 如何避免业务悬挂：Try 业务中，根据 xid 查询 account_freeze ，如果已经存在则证明Cancel 已经执行，拒绝执行 Try 业务

声明 TCC 接口：Try、Confirm、Cancel 方法都需要在接口中基于注解来声明，语法如下

```java
@LocalTCCpublic interface TCCService {
    /**
    * Try逻辑，@TwoPhaseBusinessAction中的name属性要与当前方法名一致，用于指定Try逻辑对应的方法
    */
    @TwoPhaseBusinessAction(name = "prepare", commitMethod = "confirm", rollbackMethod = "cancel")
    void prepare(@BusinessActionContextParameter(paramName = "param") String param);
    /**
    * 二阶段confirm确认方法、可以另命名，但要保证与commitMethod一致 
    *
    * @param context 上下文,可以传递try方法的参数
    * @return boolean 执行是否成功
    */
    boolean confirm (BusinessActionContext context);
    /**
    * 二阶段回滚方法，要保证与rollbackMethod一致
    */
    boolean cancel (BusinessActionContext context);
}
```

seata-demo 数据库中引入 [account_freeze_tbl.sql](https://github.com/Lanqilu/HaloSpringCloud/blob/master/seata-demo/db/account_freeze_tbl.sql) 数据表，创建对应实体类和Mapper

```java
@Data
@TableName("account_freeze_tbl")
public class AccountFreeze {
    @TableId(type = IdType.INPUT)
    private String xid;
    private String userId;
    private Integer freezeMoney;
    private Integer state;

    public static abstract class State {
        public final static int TRY = 0;
        public final static int CONFIRM = 1;
        public final static int CANCEL = 2;
    }
}
```

创建接口，使用 `@TwoPhaseBusinessAction` 注解

```java
@LocalTCC
public interface AccountTCCService {
    /**
     * 从用户账户中扣款
     */
    @TwoPhaseBusinessAction(name = "deduct", commitMethod = "confirm", rollbackMethod = "cancel")
    void deduct(@BusinessActionContextParameter(paramName = "userId") String userId,
                @BusinessActionContextParameter(paramName = "money") int money);
    boolean confirm (BusinessActionContext context);
    boolean cancel (BusinessActionContext context);
}
```

实现接口和业务

```java
@Service
@Slf4j
public class AccountTCCServiceImpl implements AccountTCCService {
    @Autowired
    private AccountMapper accountMapper;
    @Autowired
    private AccountFreezeMapper freezeMapper;

    @Override
    @Transactional
    public void deduct(String userId, int money) {
        // 获取事务id
        String xid = RootContext.getXID();
        // 判断 freeze 是否有冻结记录
        AccountFreeze oldFreeze = freezeMapper.selectById(xid);
        if (oldFreeze != null) {
            // 已经处理过一次 CANCEL，无需重复处理
            return;
        }
        // 1. 扣减可以余额
        accountMapper.deduct(userId, money);
        // 2. 记录冻结余额，事务状态
        AccountFreeze freeze = new AccountFreeze();
        freeze.setUserId(userId);
        freeze.setFreezeMoney(money);
        freeze.setState(AccountFreeze.State.TRY);
        freeze.setXid(xid);
        freezeMapper.insert(freeze);
    }

    @Override
    public boolean confirm(BusinessActionContext context) {
        // 获取事务id
        String xid = context.getXid();
        // 根据 id 删除冻结记录
        return freezeMapper.deleteById(xid) == 1;
    }

    @Override
    public boolean cancel(BusinessActionContext context) {
        // 查询冻结记录
        String xid = context.getXid();
        AccountFreeze freeze = freezeMapper.selectById(xid);
        // 空回滚判断
        if (freeze == null) {
            // 需要空回滚
            freeze.setUserId(context.getActionContext("userId").toString());
            freeze.setFreezeMoney(0);
            freeze.setState(AccountFreeze.State.CANCEL);
            freeze.setXid(xid);
            freezeMapper.insert(freeze);
            return true;
        }

        // 幂等判断
        if (freeze.getState() == AccountFreeze.State.CANCEL) {
            // 已经处理过了
            return true;
        }

        // 恢复可用余额
        accountMapper.refund(freeze.getUserId(), freeze.getFreezeMoney());
        // 将冻结余额清零
        freeze.setFreezeMoney(0);
        freeze.setState(AccountFreeze.State.CANCEL);
        return freezeMapper.updateById(freeze) == 1;
    }
}
```

修改 Controller 中使用的 Service

```java
@Autowired
private AccountTCCService accountService;
```

### SAGA 模式

Saga 模式是 Seata 提供的长事务解决方案。也分为两个阶段：

+ 一阶段：直接提交本地事务
+ 二阶段：成功则什么都不做；失败则通过编写补偿业务来回滚

Saga 模式优点：

+ 事务参与者可以基于事件驱动实现异步调用，吞吐高
+ 一阶段直接提交事务，无锁，性能好
+ 不用编写TCC中的三个阶段，实现简单

缺点：

+ 软状态持续时间不确定，时效性差
+ 没有锁，没有事务隔离，会有脏写

### 四种模式对比

| 对比内容  |  XA    | AT    | TCC   | SAGA  |
| ---- | ---- | ---- | ---- | ------ |
| 一致性 | 强一致  | 弱一致 | 弱一致 | 最终一致 |
| 隔离性 | 完全隔离 | 基于全局锁隔离 | 基于资源预留隔离  | 无隔离 |
| 代码侵入 | 无 | 无 | 有，要编写三个接口 | 有，要编写状态机和补偿业务 |
| 性能 | 差 | 好 | 非常好 | 非常好 |
| 场景 | 对一致性、隔离性有高要求的业务 | 基于关系型数据库的大多数分布式事务场景都可以 | 对性能要求较高的事务。有非关系型数据库要参与的事务。 | 业务流程长、业务流程多。参与者包含其它公司或遗留系统服务，无法提供 TCC 模式要求的三个接口 |





















