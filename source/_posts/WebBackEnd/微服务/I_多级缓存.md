---
title: 多级缓存
create: 2021-09-16
data: 2021-09-16
---

## 什么是多级缓存

传统的缓存策略一般是请求到达 Tomcat 后，先查询 Redis，如果未命中则查询数据库。会存在下面的问题：

+ 请求要经过 Tomcat 处理，Tomcat 的性能成为整个系统的瓶颈
+ Redis 缓存失效时，会对数据库产生冲击

多级缓存就是充分利用请求处理的每个环节，分别添加缓存，减轻 Tomcat 压力，提升服务性能：

+ 浏览器访问静态资源时，优先读取浏览器本地缓存
+ 访问非静态资源（Ajax查询数据）时，访问服务端
+ 请求到达 Nginx 后，优先读取 Nginx 本地缓存
+ 如果 Nginx 本地缓存未命中，则去直接查询 Redis（不经过 Tomcat）
+ 如果 Redis 查询未命中，则查询 Tomcat
+ 请求进入 Tomcat 后，优先查询 JVM 进程缓存
+ 如果 JVM 进程缓存未命中，则查询数据库

![多级缓存](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/多级缓存.4wo03x7ehfs0.svg)

在多级缓存架构中，Nginx 内部需要编写本地缓存查询、Redis 查询、Tomcat 查询的业务逻辑，因此这样的 Nginx 服务不再是一个反向代理服务器，而是一个编写业务的 Web 服务器了。

因此这样的业务 Nginx 服务也需要搭建集群来提高并发，再有专门的 Nginx 服务来做反向代理，另外，我们的 Tomcat 服务将来也会部署为集群模式

可见，多级缓存的关键有两个：

- 一个是在 Nginx 中编写业务，实现 Nginx 本地缓存、Redis、Tomcat 的查询

- 另一个就是在 Tomcat 中实现 JVM 进程缓存

其中 Nginx 编程则会用到 OpenResty 框架结合 Lua 这样的语言。















