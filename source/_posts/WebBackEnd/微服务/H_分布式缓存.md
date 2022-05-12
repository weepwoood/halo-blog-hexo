---
title: 分布式缓存
create: 2021-09-15
data: 2021-09-15
update: 2021-09-16
---

## 单点 Redis 的问题

+ 数据丢失问题：Redis 是内存存储，服务重启可能会丢失数据 → 实现 Redis 数据持久化
+ 并发能力问题：单节点 Redis 并发能力虽然不错，但也无法满足如双十一这样的高并发场景 → 搭建主从集群，实现读写分离
+ 故障恢复问题：如果 Redis 宕机，则服务不可用，需要一种自动的故障恢复手段 → 利用 Redis 哨兵，实现健康检测和自动恢复
+ 存储能力问题：Redis 基于内存，单节点能存储的数据量难以满足海量数据需求 → 搭建分片集群，利用插槽机制实现动态扩容

## Redis 持久化

### RDB 持久化

RDB 全称 Redis Database Backup file（Redis 数据备份文件），也被叫做 Redis 数据快照。简单来说就是把内存中的所有数据都记录到磁盘中。当 Redis 实例故障重启后，从磁盘读取快照文件，恢复数据。

快照文件称为 RDB 文件，默认是保存在当前运行目录。

```
# redis-cil
> save   # 由 Redis 主进程来执行 RDB，会阻塞所有命令
> bgsave # 开启子进程执行 RDB, 避免主进程受到
```

Redis 停机时会执行一次 RDB

Redis 内部有触发 RDB 的机制，可以在 redis.conf 文件中找到，格式如下：

```
# 900秒内，如果至少有1个key被修改，则执行 bgsave ， 如果是 save "" 则表示禁用RDB
save 900 1  
save 300 10  
save 60 10000 
```

RDB 的其它配置也可以在 redis.conf 文件中设置：

```
# 是否压缩 ,建议不开启，压缩也会消耗cpu，磁盘的话不值钱
rdbcompression yes

# RDB 文件名称
dbfilename dump.rdb  

# 文件保存的路径目录
dir ./ 
```

bgsave 开始时会 fork 主进程得到子进程，子进程共享主进程的内存数据。完成 fork 后读取内存数据并写入 RDB 文件。

fork 采用的是 copy-on-write 技术：

+ 当主进程执行读操作时，访问共享内存；
+ 当主进程执行写操作时，则会拷贝一份数据，执行写操作。

![RDBfork](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/RDBfork.6lsdm1x9i7w0.svg)

RDB 方式 bgsave 的基本流程：

+ fork 主进程得到一个子进程，共享内存空间
+ 子进程读取内存数据并写入新的 RDB 文件
+ 用新 RDB 文件替换旧的 RDB 文件。

RDB 会在什么时候执行？save 60 1000代表什么含义？

+ 默认是服务停止时。
+ 代表 60 秒内至少执行 1000 次修改则触发 RDB

RDB的缺点？

+ RDB 执行间隔时间长，两次 RDB 之间写入数据有丢失的风险
+ fork 子进程、压缩、写出 RDB 文件都比较耗时

### AOF 持久化

AOF 全称为 Append Only File（追加文件）。Redis 处理的每一个写命令都会记录在 AOF 文件，可以看做是命令日志文件。

AOF 默认是关闭的，需要修改 redis.conf 配置文件来开启 AOF：

```
# 是否开启AOF功能，默认是no
appendonly yes
# AOF文件的名称
appendfilename "appendonly.aof"
```

AOF 的命令记录的频率也可以通过 redis.conf 文件来配：

```
# 表示每执行一次写命令，立即记录到AOF文件
appendfsync always 
# 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendfsync everysec 
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no
```

| 配置项   | 刷盘时机     | 优点                   | 缺点                         |
| -------- | ------------ | ---------------------- | ---------------------------- |
| always   | 同步刷盘     | 可靠性高，几乎不丢数据 | 性能影响大                   |
| everysec | 每秒刷盘     | 性能适中               | 最多丢失 1 秒数据            |
| no       | 操作系统控制 | 性能最好               | 可靠性较差，可能丢失大量数据 |

因为是记录命令，AOF 文件会比 RDB 文件大的多。而且 AOF 会记录对同一个 key 的多次写操作，但只有最后一次写操作才有意义。通过执行 `bgrewriteaof` 命令，可以让 AOF 文件执行重写功能，用最少的命令达到相同效果。

 Redis 也会在触发阈值时自动去重写 AOF 文件。阈值也可以在 redis.conf 中配置：

```
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写
auto-aof-rewrite-min-size 64mb 
```

### Redis 持久化小结

RDB 和 AOF 各有自己的优缺点，如果对数据安全性要求较高，在实际开发中往往会**结合**两者来使用。

|                | RDB                                          | AOF                                                          |
| -------------- | -------------------------------------------- | ------------------------------------------------------------ |
| 持久化方式     | 定时对整个内存做快照                         | 记录每一次执行的命令                                         |
| 数据完整性     | 不完整，两次备份之间会丢失                   | 相对完整，取决于刷盘策略                                     |
| 文件大小       | 会有压缩，文件体积小                         | 记录命令，文件体积很大                                       |
| 宕机恢复速度   | 很快                                         | 慢                                                           |
| 数据恢复优先级 | 低，因为数据完整性不如 AOF                   | 高，因为数据完整性更高                                       |
| 系统资源占用   | 高，大量 CPU 和内存消耗                      | 低，主要是磁盘 IO 资源，但 AOF 重写时会占用大量 CPU 和内存资源 |
| 使用场景       | 可以容忍数分钟的数据丢失，追求更快的启动速度 | 对数据安全性要求较高常见                                     |

## Redis 主从集群

### 搭建主从架构

单节点 Redis 的并发能力是有上限的，要进一步提高 Redis 的并发能力，就需要搭建主从集群，实现读写分离。

![redis主从架构](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/redis主从架构.1yy9qyf3d7z4.svg)

共包含三个节点，一个主节点，两个从节点。

这里我们会在同一台虚拟机中开启 3 个 Redis 实例，模拟主从集群，信息如下：

|  IP  | PORT |  角色  |
| :--: | :--: | :----: |
| halo | 7001 | master |
| halo | 7002 | slave  |
| halo | 7003 | slave  |

### 准备实例和配置

要在同一台虚拟机开启 3 个实例，必须准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。

创建三个文件夹，名字分别叫 7001、7002、7003

修改 redis-6.2.4/redis.conf 文件，将其中的持久化模式改为默认的 RDB 模式，AOF 保持关闭状态。

```properties
# 绑定地址，默认是127.0.0.1，会导致只能在本地访问。修改为0.0.0.0则可以在任意IP访问
bind 0.0.0.0
# 数据库数量，设置为1
databases 1
# 开启RDB
# save ""
save 3600 1
save 300 100
save 60 10000

# 关闭AOF
appendonly no
```

拷贝配置文件到每个实例目录，然后将 redis-6.2.4/redis.conf 文件拷贝到三个目录中：

```sh
# 方式一：逐个拷贝
cp redis-6.2.4/redis.conf 7001
cp redis-6.2.4/redis.conf 7002
cp redis-6.2.4/redis.conf 7003
# 方式二：管道组合命令，一键拷贝
echo 7001 7002 7003 | xargs -t -n 1 cp redis-6.2.4/redis.conf
```

修改每个文件夹内的配置文件，将端口分别修改为 7001、7002、7003，将 rdb 文件保存位置都修改为自己所在目录：

```sh
sed -i -e 's/6379/7001/g' -e 's/dir .\//dir \/develop\/redis\/7001\//g' 7001/redis.conf
sed -i -e 's/6379/7002/g' -e 's/dir .\//dir \/develop\/redis\/7002\//g' 7002/redis.conf
sed -i -e 's/6379/7003/g' -e 's/dir .\//dir \/develop\/redis\/7003\//g' 7003/redis.conf
```

修改每个实例的声明 IP，虚拟机本身有多个 IP，为了避免将来混乱，我们需要在 redis.conf 文件中指定每一个实例的绑定 ip 信息，格式如下：

```properties
# redis实例的声明 IP
replica-announce-ip 81.70.151.52
```

每个目录都要改，我们一键完成修改：

```sh
# 逐一执行
sed -i '1a replica-announce-ip 81.70.151.52' 7001/redis.conf
sed -i '1a replica-announce-ip 81.70.151.52' 7002/redis.conf
sed -i '1a replica-announce-ip 81.70.151.52' 7003/redis.conf

# 或者一键修改
printf '%s\n' 7001 7002 7003 | xargs -I{} -t sed -i '1a replica-announce-ip 81.70.151.52' {}/redis.conf
```

为了方便查看日志，我们打开 3 个 ssh 窗口，分别启动 3 个 redis 实例，启动命令：

```sh
redis-server 7001/redis.conf
redis-server 7002/redis.conf
redis-server 7003/redis.conf
```

如果要一键停止，可以运行下面命令：

```sh
printf '%s\n' 7001 7002 7003 | xargs -I{} -t redis-cli -p {} shutdown
```

### 开启主从关系

现在三个实例还没有任何关系，要配置主从可以使用 replicaof 或者 slaveof（5.0 以前）命令。

有临时和永久两种模式：

- 修改配置文件（永久生效），在 redis.conf 中添加一行配置：```slaveof <masterip> <masterport>```

- 使用 redis-cli 客户端连接到 redis 服务，执行 slaveof 命令（重启后失效）：`slaveof <masterip> <masterport>`

注意：在 5.0 以后新增命令 replicaof，与 slaveof 效果一致。

通过 redis-cli 命令连接 7002，执行下面命令：

```sh
# 连接 7002
redis-cli -p 7002
# 执行slaveof
slaveof 81.70.151.52 7001
```

通过 redis-cli 命令连接 7003，执行下面命令：

```sh
# 连接 7003
redis-cli -p 7003
# 执行slaveof
slaveof 81.70.151.52 7001
```

然后连接 7001 节点，查看集群状态：

```sh
# 连接 7001
redis-cli -p 7001
# 查看状态
info replication
```

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.163o7jdikeg0.png)

执行下列操作以测试：

- 利用 redis-cli 连接 7001，执行 `set num 123`

- 利用 redis-cli 连接 7002，执行 `get num`，再执行 `set num 666`

- 利用 redis-cli 连接 7003，执行 `get num`，再执行 `set num 888`

可以发现，只有在 7001 这个 master 节点上可以执行写操作，7002 和 7003 这两个 slave 节点只能执行读操作。

### 主从数据同步原理

主从第一次同步是全量同步：

![主从数据同步原理](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/主从数据同步原理.3j3fw2lkmda0.svg)

master 如何判断 slave 是不是第一次来同步数据？这里会用到两个很重要的概：

+ Replication Id：简称 replid，是数据集的标记，id 一致则说明是同一数据集。每一个master 都有唯一的 replid，slave 则会继承 master 节点的 replid
+ offset：偏移量，随着记录在 `repl_baklog` 中的数据增多而逐渐增大。slave 完成同步时也会记录当前同步的 offset。如果 slave 的 offset 小于 master 的 offset，说明 slave 数据落后于 master，需要更新。

因此 slave 做数据同步，必须向 master 声明自己的 replication id 和 offset，master 才可以判断到底需要同步哪些数据

简述全量同步的流程：

+ slave 节点请求增量同步
+ master 节点判断 replid，发现不一致，拒绝增量同步
+ master 将完整内存数据生成 RDB，发送 RDB 到 slave
+ slave 清空本地数据，加载 master 的 RDB
+ master 将 RDB 期间的命令记录在 `repl_baklog`，并持续将 log 中的命令发送给 slave
+ slave 执行接收到的命令，保持与 master 之间的同步

主从第一次同步是全量同步，但如果 slave 重启后同步，则执行增量同步

> `repl_baklog` 大小有上限，写满后会覆盖最早的数据。如果 slave 断开时间过久，导致尚未备份的数据被覆盖，则无法基于 log 做增量同步，只能再次全量同步。

可以从以下几个方面来优化 Redis 主从就集群：

+ 在 master 中配置 `repl-diskless-sync yes` 启用无磁盘复制，避免全量同步时的磁盘 IO。
+ Redis 单节点上的内存占用不要太大，减少 RDB 导致的过多磁盘 IO
+ 适当提高 `repl_baklog` 的大小，发现 slave 宕机时尽快实现故障恢复，尽可能避免全量同步
+ 限制一个 master 上的 slave 节点数量，如果实在是太多 slave，则可以采用主-从-从链式结构，减少 master 压力

### Redis 主从集群小结

简述全量同步和增量同步区别？

+ 全量同步：master 将完整内存数据生成 RDB，发送 RDB 到 slave。后续命令则记录在 `repl_baklog`，逐个发送给 slave。
+ 增量同步：slave 提交自己的 offset 到 master，master 获取 `repl_baklog` 中从 offset 之后的命令给 slave

什么时候执行全量同步？

+ slave 节点第一次连接 master 节点时
+ slave 节点断开时间太久，`repl_baklog` 中的 offset 已经被覆盖时

什么时候执行增量同步？

+ slave 节点断开又恢复，并且在 `repl_baklog` 中能找到 offset 时

## Redis 哨兵

### 哨兵的作用和原理

slave 节点宕机恢复后可以找 master 节点同步数据，那 master 节点宕机怎么办？

Redis 提供了哨兵（Sentinel）机制来实现主从集群的自动故障恢复。哨兵的结构和作用如下：

![Redis哨兵](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Redis哨兵.4jsq9xke4h60.svg)

#### 哨兵的作用

+ 监控：Sentinel 会不断检查 master 和 slave 是否按预期工作
+ 自动故障恢复：如果 master 故障，Sentinel 会将一个 slave 提升为 master。当故障实例恢复后也以新的 master 为主
+ 通知：Sentinel 充当 Redis 客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给 Redis 的客户端

#### 服务状态监控

Sentinel 基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送 ping 命令：

+ 主观下线：如果某 Sentinel 节点发现某实例未在规定时间响应，则认为该实例主观下线。
+ 客观下线：若超过指定数量（quorum）的 Sentinel 都认为该实例主观下线，则该实例客观下线。quorum 值最好超过 Sentinel 实例数量的一半。

#### 选举新的 master

一旦发现 master 故障，Sentinel 需要在 salve 中选择一个作为新的 master，选择依据是这样的：

+ 首先会判断 slave 节点与 master 节点断开时间长短，如果超过指定值（`down-after-milliseconds * 10`）则会排除该 slave 节点
+ 然后判断 slave 节点的 slave-priority 值，越小优先级越高，如果是 0 则永不参与选举
+ 如果 slave-priority 一样，则判断 slave 节点的 offset 值，越大说明数据越新，优先级越高
+ 最后是判断 slave 节点的运行 id 大小，越小优先级越高。

#### 如何实现故障转移

当选中了其中一个 slave 为新的 master 后（例如 slave1），故障的转移的步骤如下：

+ Sentinel 给备选的 slave1 节点发送 slaveof no one 命令，让该节点成为 master
+ Sentinel 给所有其它 slave 发送 slaveof ip port 命令，让这些 slave 成为新 master 的从节点，开始从新的 master 上同步数据。
+ 最后，Sentinel 将故障节点标记为 slave，当故障节点恢复后会自动成为新的 master 的 slave 节点

#### 哨兵的作用和原理小结

Sentinel 的三个作用是什么？

+ 监控
+ 故障转移
+ 通知

Sentinel 如何判断一个 redis 实例是否健康？

+ 每隔 1 秒发送一次 ping 命令，如果超过一定时间没有相向则认为是主观下线
+ 如果大多数 sentinel 都认为实例主观下线，则判定服务下线

故障转移步骤有哪些？

+ 首先选定一个 slave 作为新的 master，执行 slaveof no one
+ 然后让所有节点都执行 slaveof 新 master
+ 修改故障节点配置，添加 slaveof 新 master

### 搭建哨兵集群

三个 Sentinel 实例信息如下：

| 节点 |    IP     | PORT  |
| ---- | :-------: | :---: |
| s1   | public-ip | 27001 |
| s2   | public-ip | 27002 |
| s3   | public-ip | 27003 |

#### 准备实例和配置

要在同一台虚拟机开启3个实例，必须准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。

我们创建三个文件夹，名字分别叫s1、s2、s3：

```
# 进入/sentinel 目录
cd /sentinel
# 创建目录
mkdir s1 s2 s3
```

然后我们在 s1 目录创建一个 sentinel.conf 文件，添加下面的内容：

```properties
port 27001
sentinel announce-ip 81.70.151.52
sentinel monitor mymaster 81.70.151.52 7001 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "/develop/redis/sentinel/s1"
```

解读：

- `port 27001`：是当前 sentinel 实例的端口
- `sentinel monitor mymaster 81.70.151.52 7001 2`：指定主节点信息
  - `mymaster`：主节点名称，自定义，任意写
  - `public-ip 7001`：主节点的 ip 和端口
  - `2`：选举 master 时的 quorum 值

然后将 s1/sentinel.conf 文件拷贝到 s2、s3 两个目录中（在 /sentinel 目录执行下列命令）：

```sh
# 方式一：逐个拷贝
cp s1/sentinel.conf s2
cp s1/sentinel.conf s3
# 方式二：管道组合命令，一键拷贝
echo s2 s3 | xargs -t -n 1 cp s1/sentinel.conf
```

修改 s2、s3 两个文件夹内的配置文件，将端口分别修改为 27002、27003：

```sh
sed -i -e 's/27001/27002/g' -e 's/s1/s2/g' s2/sentinel.conf
sed -i -e 's/27001/27003/g' -e 's/s1/s3/g' s3/sentinel.conf
```

#### 启动

为了方便查看日志，我们打开 3 个 ssh 窗口，分别启动 3 个 redis 实例，启动命令：

```sh
# 第1个
redis-sentinel s1/sentinel.conf
# 第2个
redis-sentinel s2/sentinel.conf
# 第3个
redis-sentinel s3/sentinel.conf
```

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4mzu19ficpw0.png)

### Redis Template 的哨兵模式

在 Sentinel 集群监管下的 Redis 主从集群，其节点会因为自动故障转移而发生变化，Redis 的客户端必须感知这种变化，及时更新连接信息。Spring 的 Redis Template 底层利用 lettuce 实现了节点的感知和自动切换。

初始化工程：[链接](https://github.com/Lanqilu/HaloSpringCloud/commit/21a1e151dd294acabd648989d4638f39ad6bf625)

首先，在 pom 文件中引入 redis 的 starter 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

然后在配置文件 application.yml 中指定 sentinel 相关信息：

```yml
spring:
  redis:
    sentinel:
      master: mymaster # 指定master名称
      nodes: # 指定redis-sentinel集群信息
        - redis:27001
        - redis:27002
        - redis:27003
```

最后，配置主从读写分离

```java
@Bean 
public LettuceClientConfigurationBuilderCustomizer configurationBuilderCustomizer(){
    return configBuilder -> configBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
```

这里的 `ReadFrom` 是配置 Redis 的读取策略，是一个枚举，包括下面选择：

+ MASTER：从主节点读取
+ MASTER_PREFERRED：优先从 master 节点读取，master 不可用才读取 replica
+ REPLICA：从 slave（replica）节点读取
+ REPLICA _PREFERRED：优先从 slave（replica）节点读取，所有的 slave 都不可用才读取 master

## Redis 分片集群

主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：

+ 海量数据存储问题
+ 高并发写的问题

使用分片集群可以解决上述问题，分片集群特征：

+ 集群中有多个 master，每个 master 保存不同数据
+ 每个 master 都可以有多个 slave 节点
+ master 之间通过 ping 监测彼此健康状态
+ 客户端请求可以访问集群任意节点，最终都会被转发到正确节点

![redis分片集群](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/redis分片集群.3bscfw32y2g0.svg)

### 搭建分片集群

#### 集群结构

分片集群需要的节点数量较多，这里我们搭建一个最小的分片集群，包含 3 个 master 节点，每个 master 包含一个 slave 节点

这里我们会在同一台虚拟机中开启 6 个 redis 实例，模拟分片集群，信息如下：

|    IP     | PORT |  角色  |
| :-------: | :--: | :----: |
| public-ip | 7001 | master |
| public-ip | 7002 | master |
| public-ip | 7003 | master |
| public-ip | 8001 | slave  |
| public-ip | 8002 | slave  |
| public-ip | 8003 | slave  |

#### 准备实例和配置

删除之前的 7001、7002、7003 这几个目录，重新创建出 7001、7002、7003、8001、8002、8003 目录：

```sh
# 进入/tmp目录
cd /redis
# 删除旧的，避免配置干扰
rm -rf 7001 7002 7003
# 创建目录
mkdir 7001 7002 7003 8001 8002 8003
```

在 /redis 下准备一个新的 redis.conf 文件，内容如下：

```properties
port 6379
# 开启集群功能
cluster-enabled yes
# 集群的配置文件名称，不需要我们创建，由redis自己维护
cluster-config-file /develop/redis/6379/nodes.conf
# 节点心跳失败的超时时间
cluster-node-timeout 5000
# 持久化文件存放目录
dir /develop/redis/6379
# 绑定地址
bind 0.0.0.0
# 让redis后台运行
daemonize yes
# 注册的实例ip
replica-announce-ip 81.70.151.52
# 保护模式
protected-mode no
# 数据库数量
databases 1
# 日志
logfile /develop/redis/6379/run.log
```

将这个文件拷贝到每个目录下：

```sh
# 执行拷贝
echo 7001 7002 7003 8001 8002 8003 | xargs -t -n 1 cp redis.conf
```

修改每个目录下的 redis.conf，将其中的 6379 修改为与所在目录一致：

```sh
# 修改配置文件
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t sed -i 's/6379/{}/g' {}/redis.conf
```

#### 启动

因为已经配置了后台启动模式，所以可以直接启动服务：

```sh
# 一键启动所有服务
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t redis-server {}/redis.conf
```

通过 ps 查看状态：

```sh
ps -ef | grep redis
```

如果要关闭所有进程，可以执行命令：

```sh
ps -ef | grep redis | awk '{print $2}' | xargs kill
```

或者（推荐这种方式）：

```sh
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t redis-cli -p {} shutdown
```

#### 创建集群

虽然服务启动了，但是目前每个服务之间都是独立的，没有任何关联。

我们需要执行命令来创建集群，在 Redis 5.0 之前创建集群比较麻烦，5.0 之后集群管理命令都集成到了redis-cli 中。

我们使用的是 Redis 6.2.4 版本，集群管理以及集成到了 redis-cli 中，格式如下：

```sh
redis-cli --cluster create --cluster-replicas 1 81.70.151.52:7001 81.70.151.52:7002 81.70.151.52:7003 81.70.151.52:8001 81.70.151.52:8002 81.70.151.52:8003
```

命令说明：

- `redis-cli --cluster` 或者 `./redis-trib.rb`：代表集群操作命令
- `create`：代表是创建集群
- `--replicas 1` 或者 `--cluster-replicas 1` ：指定集群中每个 master 的副本个数为1，此时 `节点总数 ÷ (replicas + 1)` 得到的就是 master 的数量。因此节点列表中的前 n 个就是 master，其它节点都是 slave 节点，随机分配到不同 master

运行后的结果：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2kex1iskkag0.png)

这里输入 yes，则集群开始创建。

注意，如果出现 Waiting for the cluster to join 问题，说明这里需要开放端口，端口号为 1700x 和 1800x。[参考链接](https://blog.csdn.net/XIANZHIXIANZHIXIAN/article/details/82392172)

通过命令可以查看集群状态：

```sh
redis-cli -p 7001 cluster nodes
```

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.24itacoigij.png)

### 散列插槽

Redis 会把每一个 master 节点映射到 0~16383 共 16384 个插槽（hash slot）上，查看集群信息时就能看到。

数据 key 不是与节点绑定，而是与插槽绑定。redis 会根据 key 的有效部分计算插槽值，分两种情况：

+ key 中包含 `{}`，且 `{}` 中至少包含 1 个字符，`{}` 中的部分是有效部分
+ key 中不包含 `{}`，整个 key 都是有效部分

例如：

+ key 是 `num`，那么就根据 `num` 计算
+ key 是 `{halo}num`，则根据 `halo` 计算。
+ 计算方式是利用 CRC16 算法得到一个 hash 值，然后对 16384 取余，得到的结果就是 slot 值。

使用 `redis-cli -c -p 7001` 测试

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2p5uncp37x00.png)

Redis 如何判断某个 key 应该在哪个实例？

+ 将 16384 个插槽分配到不同的实例
+ 根据 key 的有效部分计算哈希值，对 16384 取余
+ 余数作为插槽，寻找插槽所在实例即可

如何将同一类数据固定的保存在同一个 Redis 实例？

+ 这一类数据使用相同的有效部分，例如 key 都以 `{typeId}` 为前缀

Redis 为什么设置 16384 个槽。CRC16 算法产生的 hash 值有 16bit，该算法可以产生 2<sup>16</sup> = 65536 个值。换句话说，值是分布在 0 到 65535 之间。那作者在做 `mod` 运算的时候，为什么不 `mod` 65536，而选择 `mod` 16384？

+ 如果槽位为 65536，发送心跳信息的消息头达 8k，发送的心跳包过于庞大。在消息头中，最占空间的是 `myslots[CLUSTER_SLOTS/8]`。 当槽位为 65536 时，这块的大小是： `65536÷8÷1024=8kb` 因为每秒钟，Redis 节点需要发送一定数量的 ping 消息作为心跳包，如果槽位为 65536，这个 ping 消息的消息头太大了，浪费带宽。16384 块的大小是：`16384÷8÷1024=2kb`
+ Redis 的集群主节点数量基本不可能超过1000个。集群节点越多，心跳包的消息体内携带的数据越多。如果节点过 1000 个，也会导致网络拥堵。因此 Redis 作者，不建议 redis cluster 节点数量超过 1000 个。 那么，对于节点数在 1000 以内的 redis cluster 集群，16384 个槽位够用了。没有必要拓展到 65536 个。
+ 槽位越小，节点少的情况下，压缩比高。

### 集群伸缩

#### 添加一个节点到集群

`redis-cli --cluster` 提供了很多操作集群的命令，可以通过 `redis-cli --cluster help` 进行查看

比如，添加节点的命令：

```sh
add-node       new_host:new_port existing_host:existing_port
               --cluster-slave
               --cluster-master-id <arg>
```

案例：向集群中添加一个新的 master 节点，并向其中存储 `num = 10`

需求：

+ 启动一个新的 redis 实例，端口为 7004
+ 添加 7004 到之前的集群，并作为一个 master 节点
+ 给 7004 节点分配插槽，使得 num 这个 key 可以存储到 7004 实例

```
mkdir 7004
cp redis.conf 7004
sed -i s/6379/7004/g 7004/redis.conf
redis-server 7004/redis.conf
ps -ef | grep redis
```

```
redis-cli --cluster add-node 81.70.151.52:7004 81.70.151.52:7001
redis-cli -p 7001 cluster nodes
```

记得设置放行防火墙的 7004 和 17004 端口

```
redis-cli --cluster reshard 81.70.151.52:7001
```

![pic](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/pic.3j8k7rbwya20.jpg)

```
redis-cli -p 7001 cluster nodes
```

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5f5wxyam21o0.png)

#### 删除集群中的一个节点

需求：删除 7004 节点

首先移动插槽

```
redis-cli --cluster reshard 81.70.151.52:7004
```

然后删除

```
redis-cli --cluster del-node 81.70.151.52:7004 a163eeb5bca08ecb1df92b4835c388a73d2a6ea3
redis-cli -p 7001 cluster nodes
```

### 故障转移

#### 测试故障转移

```
watch redis-cli -p 7001 cluster nodes
```

```
redis-cli -p 7002 shutdown
redis-server 7002/redis.conf
```

当集群中有一个 master 宕机会发生什么呢？

+ 首先是该实例与其它实例失去连接
+ 然后是疑似宕机
+ 最后是确定下线，自动提升一个 slave 为新的 master

#### 数据迁移

利用 cluster failover 命令可以手动让集群中的某个 master 宕机，切换到执行 cluster failover 命令的这个 slave 节点，实现无感知的数据迁移。其流程如下：

![数据迁移](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/数据迁移.5hub7i7gnx00.svg)

手动的 failover 支持三种不同模式：

+ 缺省：默认的流程，如图 1~6 步
+ force：省略了对 offset 的一致性校验
+ takeover：直接执行第 5 步，忽略数据一致性、忽略 master 状态和其它 master 的意见

```
redis-cli -p 7002
> cluster failover
```

### Redis Template 访问分片集群

Redis Template 底层同样基于 lettuce 实现了分片集群的支持，而使用的步骤与哨兵模式基本一致：

+ 引入 redis 的 starter 依赖
+ 配置分片集群地址
+ 配置读写分离

与哨兵模式相比，其中只有分片集群的配置方式略有差异，如下：

```yaml
spring:
  redis:
    cluster:
      nodes: # 指定分片集群的每一个节点信息
        - redis:7001
        - redis:7002
        - redis:7003
        - redis:8001
        - redis:8002
        - redis:8003
```











