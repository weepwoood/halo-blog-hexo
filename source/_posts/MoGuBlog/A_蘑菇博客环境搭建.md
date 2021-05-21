---
title: 蘑菇博客环境搭建
date: 2021-5-15 15:45:20
large: true
tags:
categories:
- 蘑菇博客
description: 蘑菇博客是我目前找到理想的博客系统，基于对该项目的学习，进一步掌握 Java 后端过程
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/%E5%A3%81%E7%BA%B8%20(75).jpg
---

{% link 蘑菇博客项目地址, https://gitee.com/moxi159753/mogu_blog_v2, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/icon/Gitee.svg %}


蘑菇博客（MoguBlog），一个基于微服务架构的前后端分离博客系统。Web 端使用 Vue + Element , 移动端使用 uniapp 和 ColorUI。后端使用 Spring Cloud + Spring Boot + mybatis-plus 进行开发，使用 Jwt + Spring Security 做登录验证和权限校验，使用 ElasticSearch 和 Solr 作为全文检索服务，使用 Github Actions 完成博客的持续集成，使用 ELK 收集博客日志，文件支持上传七牛云和 Minio，支持 Docker Compose 脚本一键部署。


参考资料：

{% link Windows+VMware一键搭建蘑菇博客本地开发环境, http://www.moguit.cn/#/info?blogOid=583, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@master/img/网站.svg %}


## Docker 环境

在 VMware 或 云服务器中 配置 Docker 环境

### 安装Docker

因为我们需要拉取镜像，所以需要在服务器提前安装好 Docker，首先配置一下 Docker 的阿里 yum 源

```
cat >/etc/yum.repos.d/docker.repo<<EOF
[docker-ce-edge]
name=Docker CE Edge - \$basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/\$basearch/edge
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
EOF
```

然后 yum 方式安装 Docker

```
# yum安装
yum -y install docker-ce
# 查看docker版本
docker --version  
# 开机自启
systemctl enable docker
# 启动docker
systemctl start docker
```

配置 Docker 的镜像源

```
cat >> /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF
```

然后重启 Docker

```
systemctl restart docker
```

因为 Docker 容器之间，需要互相通信访问，所以我们需要创建我们的 Docker 网络

```
docker network create mogu
```

### 安装 Docker Compose

Docker Compose 是用来定义和运行多个 Docker 应用程序的工具。通过 Compose，可以使用 YAML 文件来配置应用程序需要的所有服务，然后使用一个命令即可从YML文件配置中创建并启动所有服务。

{% link 安装 Docker Compose 官方文档, https://docs.docker.com/compose/install/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/DockerIcon.svg %}

{% link Docker Compose 下载链接, https://github.com/docker/compose/releases, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/GithubIcon.jpg %}

把下载到的文件使用 ftp 工具，拷贝到 /usr/local/bin/ 目录下

```
# 重命名
mv docker-compose-Linux-x86_64  docker-compose
# 加入执行权限
sudo chmod +x /usr/local/bin/docker-compose
# 查看docker-compose版本
docker-compose -v
```

## 下载源码

下载蘑菇博客的[源码](https://gitee.com/moxi159753/mogu_blog_v2)

找到，doc\docker-compose 目录，目录结构如下

```
docker-compose
 ├── bin     # 相关一键启动脚本的目录
 ├── config  # 存放配置文件
 ├── data    # 存放数据文件
 ├── log     # 存放日志文件
 └── yaml    # 存放 docker compose 的 yaml 文件
```

下面我们开始，将 docker-compose 文件夹，拷贝服务器目录位置随意，我是拷贝到 /root/docker-compose 目录，然后给命令设置执行权限

```
# 进入目录
cd docker-compose
# 添加执行权限
chmod +x bin/middleware.sh
chmod +x bin/kernShutdown.sh
chmod +x bin/wait-for-it.sh
```

## 部署核心组件

下面我们将部署蘑菇博客所依赖的核心中间件，例如：Nacos、MySQL、Redis、RabbitMQ、Nginx 等

首先到 docker-compose/bin 目录下，执行脚本

```
# 开始部署核心组件
sh middleware.sh
# 以后打算关闭的话，执行 ./kernShutdown.sh
```

在部署完中间件后，我们需要进行测试中间件安装是否成功。

### MySQL

利用 SQLyog、IDEA等工具测试连接：

默认MySQL用户名：root
默认MySQL密码：mogu2018

![Idea测试mugo_mysql连接](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/Idea测试mugo_mysql连接.png)

### Nacos

Nacos 后台配置页面，默认账号和密码：nacos  nacos

访问下面 URL 进行测试 [http://nacos:8848/nacos](http://nacos:8848/nacos)

![测试nacos_mugo连接](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/测试nacos_mugo连接.png)

> 设置 host 将 nacos 改为对应服务器外网 IP ，记得开放对应端口

### Redis

默认Redis密码：mogu2018

### RabbitMQ

RabbitMQ是消息队列，我们可以访问其图形化界面

```
# 图形化地址
IP地址:15672
# 默认账号和密码为：admin  mogu2018
```

![测试rabbitmq_mugo连接](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/测试rabbitmq_mugo连接.png)

## 部署非核心组件

### Zipkin

Zipkin是一个开源的分布式的链路追踪系统，每个微服务都会向zipkin报告计时数据，聚合各业务系统调用延迟数据，达到链路调用监控跟踪


### Sentinel

### 搜索模块


## 启动后端项目

### 修改项目配置

修改的是 mogu_admin 项目的 application.yml，将 dev 改成 prod 然后修改 bootstrap.yaml ，将 dev 改成 prod，同时还需要修改 nacos 和 sentinel ，将 localhost改为对应的服务名称即可。

我们重复上面操作，将 mogu_web、mogu_sms、mogu_gateway、mogu_picture、mogu_montor 等项目都进行修改。

### 启动项目

修改完成后，直接启动项目即可。其中我们需要启动核心组件包含下面几个【服务启动没有先后顺序】

```
mogu_admin、mogu_web、mogu_sms、mogu_picture、mogu_gateway
```

等服务器都启动完成后，下面我们验证一下后台是否正常启动，回到我们的 Nacos 管理界面,如果我们看到下面五个服务都注册到 Nacos 中，那说明启动成功

+ mogu_picture
+ mogu_sms
+ mogu_admin
+ mogu_web
+ mogu_gateway

在通过访问下列 swagger 接口，测试接口是否正常

```
############ admin端swagger ##################
http://localhost:8601/swagger-ui/index.html

############ picture端swagger ##################
http://localhost:8602/swagger-ui/index.html

############ web端swagger ##################
http://localhost:8603/swagger-ui/index.html
```

如果能够进入下面页面的话，说明后台是没有问题的了，下面我们可以验证一下接口