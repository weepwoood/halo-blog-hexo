---
title: Docker
create: 2021-09-10
data: 2021-09-10
---

## 初识 Docker

### 什么是 Docker

微服务虽然具备各种各样的优势，但服务的拆分通用给部署带来了很大的麻烦。

- 分布式系统中，依赖的组件非常多，不同组件之间部署时往往会产生一些冲突。
- 在数百上千台服务中重复部署，环境不一定一致，会遇到各种问题

#### 应用部署的环境问题

大型项目组件较多，运行环境也较为复杂，部署时会碰到一些问题：

- 依赖关系复杂，容易出现兼容性问题

- 开发、测试、生产环境有差异

例如一个项目中，部署时需要依赖于 node.js、Redis、RabbitMQ、MySQL 等，这些服务部署时所需要的函数库、依赖项各不相同，甚至会有冲突。给部署带来了极大的困难。

#### Docker 解决依赖兼容问题

而 Docker 确巧妙的解决了这些问题，Docker 是如何实现的呢？

Docker 为了解决依赖的兼容问题的，采用了两个手段：

- 将应用的 Libs（函数库）、Deps（依赖）、配置与应用一起打包

- 将每个应用放到一个隔离**容器**去运行，避免互相干扰

这样打包好的应用包中，既包含应用本身，也保护应用所需要的 Libs、Deps，无需再操作系统上安装这些，自然就不存在不同应用之间的兼容问题了。

虽然解决了不同应用的兼容问题，但是开发、测试等环境会存在差异，操作系统版本也会有差异，怎么解决这些问题呢？

#### Docker 解决操作系统环境差异

要解决不同操作系统环境差异问题，必须先了解操作系统结构。计算机系统结构如下：

- 计算机硬件：例如 CPU、内存、磁盘等
- 系统内核：所有 Linux 发行版的内核都是 Linux，例如 CentOS、Ubuntu、Fedora 等。内核可以与计算机硬件交互，对外提供内核指令，用于操作计算机硬件。
- 系统应用：操作系统本身提供的应用、函数库。这些函数库是对内核指令的封装，使用更加方便。

应用于计算机交互的流程如下：

+ 应用调用操作系统应用（函数库），实现各种功能
+ 系统函数库是对内核指令集的封装，会调用内核指令
+ 内核指令操作计算机硬件

Ubuntu 和 CentOS 都是基于 Linux 内核，无非是系统应用不同，提供的函数库有差异。此时，如果将一个 Ubuntu 版本的 MySQL 应用安装到 CentOS 系统，MySQL 在调用 Ubuntu 函数库时，会发现找不到或者不匹配，就会报错了。

Docker 如何解决不同系统环境的问题？

- Docker 将用户程序与所需要调用的系统（比如 Ubuntu）函数库一起打包
- Docker 运行到不同操作系统时，直接基于打包的函数库，借助于操作系统的 Linux 内核来运行

#### 什么是 Docker 小结

Docker 如何解决大型项目依赖关系复杂，不同组件依赖的兼容性问题？

- Docker 允许开发中将应用、依赖、函数库、配置一起**打包**，形成可移植镜像
- Docker 应用运行在容器中，使用沙箱机制，相互**隔离**

Docker如何解决开发、测试、生产环境有差异的问题？

- Docker 镜像中包含完整运行环境，包括系统函数库，仅依赖系统的 Linux 内核，因此可以在任意 Linux 操作系统上运行

Docker 是一个快速交付应用、运行应用的技术，具备下列优势：

- 可以将程序及其依赖、运行环境一起打包为一个镜像，可以迁移到任意 Linux 操作系统
- 运行时利用沙箱机制形成隔离容器，各个应用互不干扰
- 启动、移除都可以通过一行命令完成，方便快捷

### Docker 和虚拟机的区别

Docker 可以让一个应用在任何操作系统中非常方便的运行。而以前我们接触的虚拟机，也能在一个操作系统中，运行另外一个操作系统，保护系统中的任何应用。

虚拟机（virtual machine）是在操作系统中模拟硬件设备，然后运行另一个操作系统，比如在 Windows 系统里面运行 Ubuntu 系统，这样就可以运行任意的 Ubuntu 应用了。

Docker 仅仅是封装函数库，并没有模拟完整的操作系统

两者有什么差异呢？

- Docker 是一个系统进程；虚拟机是在操作系统中的操作系统

- Docker 体积小、启动速度快、性能好；虚拟机体积大、启动速度慢、性能一般

### Docker 基本知识

#### 镜像和容器

Docker 中有几个重要的概念：

+ 镜像（Image）：Docker 将应用程序及其所需的依赖、函数库、环境、配置等文件打包在一起，称为镜像。
+ 容器（Container）：镜像中的应用程序运行后形成的进程就是**容器**，只是 Docker 会给容器进程做隔离，对外不可见。

一切应用最终都是代码组成，都是硬盘中的一个个的字节形成的**文件**。只有运行时，才会加载到内存，形成进程。

+ 镜像，就是把一个应用在硬盘上的文件、及其运行环境、部分系统函数库文件一起打包形成的文件包。这个文件包是只读的。
+ 容器，就是将这些文件中编写的程序、函数加载到内存中允许，形成进程，只不过要隔离起来。

因此一个镜像可以启动多次，形成多个容器进程。

#### DockerHub

开源应用程序非常多，打包这些应用往往是重复的劳动。为了避免这些重复劳动，人们就会将自己打包的应用镜像，例如 Redis、MySQL 镜像放到网络上，共享使用，就像 GitHub 的代码共享一样。

- DockerHub ：DockerHub 是一个官方的 Docker 镜像的托管平台。这样的平台称为 Docker Registry。

- 国内也有类似于 DockerHub 的公开服务，比如 [网易云镜像服务](https://c.163yun.com/hub)、[阿里云镜像库](https://cr.console.aliyun.com/) 等。

#### Docker 架构

我们要使用 Docker 来操作镜像、容器，就必须要安装 Docker。

Docker 是一个 CS 架构的程序，由两部分组成：

- 服务端（server）：Docker 守护进程，负责处理 Docker 指令，管理镜像、容器等

- 客户端（client）：通过命令或 RestAPI 向 Docker 服务端发送指令。可以在本地或远程向服务端发送指令。

![Docker架构](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Docker架构.4alzm4dto2g0.svg)

## 配置 Docker

Docker 分为 CE 和 EE 两大版本。CE 即社区版（免费，支持周期 7 个月），EE 即企业版，强调安全，付费使用，支持周期 24 个月。

Docker CE 分为 `stable` `test` 和 `nightly` 三个更新频道。

官方网站上有各种环境下的 [安装指南](https://docs.docker.com/install/)，这里主要介绍 Docker CE 在 CentOS上的安装。

### CentOS 安装 Docker

Docker CE 支持 64 位版本 CentOS 7，并且要求内核版本不低于 3.10， CentOS 7 满足最低内核的要求，所以我们在 CentOS 7 安装 Docker。

#### 卸载 Docker（可选）

如果之前安装过旧版本的 Docker，可以使用下面命令卸载：

```sh
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```

#### 安装 Docker

安装 yum 工具

```sh
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```

然后更新本地镜像源：

```sh
# 设置docker镜像源
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo

yum makecache fast
```

然后输入命令：

```sh
yum install -y docker-ce
```

docker-ce 为社区免费版本。稍等片刻，docker 即可安装成功。

### 启动 Docker

Docker 应用需要用到各种端口，逐一去修改防火墙设置。学习时可以直接关闭防火墙。

```sh
# 关闭
systemctl stop firewalld
# 禁止开机启动防火墙
systemctl disable firewalld
```

通过命令启动 Docker：

```sh
systemctl start docker  # 启动docker服务
systemctl stop docker  # 停止docker服务
systemctl restart docker  # 重启docker服务
```

然后输入命令，可以查看 Docker 版本：

```sh
docker -v
```

### 配置镜像加速

Docker 官方镜像仓库网速较差，我们需要设置国内镜像服务：

参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

通过修改 daemon 配置文件 /etc/docker/daemon.json 来使用加速器

```sh
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://578xeysa.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Docker 的基本操作

### 镜像操作

#### 镜像名称

首先来看下镜像的名称组成：

- 镜名称一般分两部分组成：[repository]:[tag]。
- 在没有指定 tag 时，默认是 latest，代表最新版本的镜像

#### 镜像命令

常见的镜像操作命令如图：

![Docker镜像操作命令](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Docker镜像操作命令.71pfsgm3nn00.svg)

#### 拉取、查看镜像

需求：从 DockerHub 中拉取一个 Nginx 镜像并查看

1. 首先去镜像仓库搜索 Nginx 镜像，比如 [DockerHub](https://hub.docker.com/):
2. 根据查看到的镜像名称，拉取自己需要的镜像，通过命令：`docker pull nginx`
3. 通过命令：`docker images` 查看拉取到的镜像

#### 保存镜像

需求：利用 docker save 将 Nginx 镜像导出磁盘，然后再通过 load 加载回来

利用 `docker xx --help` 命令查看 `docker save` 和 `docker load` 的语法。例如，查看save命令用法，可以输入命令：

```sh
docker save --help
```

命令格式：

```sh
docker save -o [保存的目标文件名称] [镜像名称]
```

使用 `docker save` 导出镜像到磁盘，运行命令：

```sh
docker save -o nginx.tar nginx:latest
```

#### 导入镜像

先删除本地的 Nginx 镜像：

```sh
docker rmi nginx:latest
```

然后运行命令，加载本地文件：

```sh
docker load -i nginx.tar
```

### 容器操作

#### 容器相关命令

容器操作的命令如图：

![Docker容器相关命令](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/Docker容器相关命令.54kd7pt4p2w0.svg)

容器保护三个状态：

- 运行：进程正常运行
- 暂停：进程暂停，CPU 不再运行，并不释放内存
- 停止：进程终止，回收进程占用的内存、CPU 等资源

其中：

- `docker run`：创建并运行一个容器，处于运行状态
- `docker pause`：让一个运行的容器暂停
- `docker unpause`：让一个容器从暂停状态恢复运行
- `docker stop`：停止一个运行的容器
- `docker start`：让一个停止的容器再次运行
- `docker rm`：删除一个容器
- `docker logs` ：查看容器日志的命令，添加 `-f` 参数可以持续查看日志
- `docker ps` ：查看容器状态，`-a` 查看所有容器，包括已经停止的

#### 创建并运行一个容器

创建并运行 Nginx 容器的命令：

```sh
docker run --name haloNginx -p 81:80 -d nginx
```

命令解读：

- `docker run` ：创建并运行一个容器
- `--name` : 给容器起一个名字，比如叫做 haloNginx
- `-p` ：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
- `-d`：后台运行容器
- `nginx`：镜像名称，例如 nginx

这里的 `-p` 参数，是将容器端口映射到宿主机端口。

默认情况下，容器是隔离环境，我们直接访问宿主机的 81 端口，肯定访问不到容器中的 Nginx。

现在，将容器的 80 与宿主机的 81 关联起来，当我们访问宿主机的 81 端口时，就会被映射到容器的80，这样就能访问到 Nginx 了

#### 进入容器并修改文件

**需求**：进入 Nginx 容器，修改 HTML 文件内容，添加“Halo World!”

> 提示：进入容器要用到 `docker exec` 命令。

① 进入容器。进入我们刚刚创建的 Nginx 容器的命令为：

```sh
docker exec -it haloNginx bash
```

命令解读：

- `docker exec` ：进入容器内部，执行一个命令

- `-it` : 给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互

- `haloNginx`：要进入的容器的名称

- `bash`：进入容器后执行的命令，bash 是一个 Linux 终端交互命令

② 进入 Nginx 的 HTML 所在目录 /usr/share/nginx/html

容器内部会模拟一个独立的 Linux 文件系统，看起来如同一个 Linux 服务器一样

Nginx 的环境、配置、运行文件全部都在这个文件系统中，包括我们要修改的 html 文件。

查看 DockerHub 网站中的 Nginx 页面，可以知道 Nginx 的 html 目录位置在 `/usr/share/nginx/html`

我们执行命令，进入该目录：

```sh
cd /usr/share/nginx/html
```

③ 修改 index.html 的内容

容器内没有 vi 命令，无法直接修改，我们用下面的命令来修改：

```sh
sed -i -e 's#Welcome to nginx#Halo World!#g' -e 's#<head>#<head><meta charset="utf-8">#g' index.html
```

### 数据卷（容器数据管理）

在之前的 Nginx 案例中，修改 Nginx 的 html 页面时，需要进入 Nginx 内部。并且因为没有编辑器，修改文件也很麻烦。

这就是因为容器与数据（容器内文件）耦合带来的后果。容器与数据耦合的问题：

+ 不便于修改：当我们要修改 Nginx 的 html 内容时，需要进入容器内部修改，很不方便。
+ 数据不可复用：在容器内的修改对外是不可见的。所有修改对新创建的容器是不可复用的。
+ 升级维护困难：数据在容器内，如果要升级容器必然删除旧容器，所有数据都跟着删除了

要解决这个问题，必须将数据与容器解耦，这就要用到数据卷了。

#### 什么是数据卷

数据卷（volume）是一个虚拟目录，指向宿主机文件系统中的某个目录。

一旦完成数据卷挂载，对容器的一切操作都会作用在数据卷对应的宿主机目录了。

这样，我们操作宿主机的 /var/lib/docker/volumes/html 目录，就等于操作容器内的 /usr/share/nginx/html 目录了

#### 数据集操作命令

数据卷操作的基本语法如下：

```sh
docker volume [COMMAND]
```

`docker volume` 命令是数据卷操作，根据命令后跟随的 `command` 来确定下一步的操作：

- `create` ：创建一个 volume
- `inspect` ：显示一个或多个 volume 的信息
- `ls` ：列出所有的 volume
- `prune` ：删除未使用的 volume
- `rm` ：删除一个或多个指定的 volume

#### 创建和查看数据卷

① 创建数据卷

```sh
docker volume create nginx-html
```

② 查看所有数据

```sh
docker volume ls
```

③ 查看数据卷详细信息卷

```sh
docker volume inspect nginx-html
```

可以看到，我们创建的 nginx-html 这个数据卷关联的宿主机目录为 `/var/lib/docker/volumes/nginx-html/_data` 目录。

#### 挂载数据卷

我们在创建容器时，可以通过 `-v` 参数来挂载一个数据卷到某个容器内目录，命令格式如下：

```sh
docker run \
  --name halo-nginx \
  -v nginx-html:/usr/share/nginx/html \
  -p 81:80
  nginx \
```

这里的 `-v` 就是挂载数据卷的命令。`-v nginx-html:/root/htm` ：把 nginx-html 数据卷挂载到容器内的 /root/html 这个目录中

需求：创建一个 Nginx 容器，修改容器内的 html 目录内的 index.html 内容

分析：上个案例中，我们进入 Nginx 容器内部，已经知道 Nginx 的 html 目录所在位置 /usr/share/nginx/html ，我们需要把这个目录挂载到 html 这个数据卷上，方便操作其中的内容。

① 创建容器并挂载数据卷到容器内的 HTML 目录

```sh
docker run --name halo-nginx -v nginx-html:/usr/share/nginx/html -p 81:80 -d nginx
```

② 进入 nginx-html 数据卷所在位置，并修改 HTML 内容

```sh
# 查看html数据卷的位置
docker volume inspect nginx-html
# 进入该目录
cd /var/lib/docker/volumes/nginx-html/_data
# 修改文件
vi index.html
```

> 数据卷不存在会自动创建

#### 挂载本地目录

容器不仅仅可以挂载数据卷，也可以直接挂载到宿主机目录上。关联关系如下：

- 带数据卷模式：宿主机目录 → 数据卷 → 容器内目录
- 直接挂载模式：宿主机目录 → 容器内目录

目录挂载与数据卷挂载的语法是类似的：

- `-v [宿主机目录]:[容器内目录]`
- `-v [宿主机文件]:[容器内文件]`

需求：创建并运行一个 MySQL 容器，将宿主机目录直接挂载到容器

1. 拉取 MySQL 镜像

2. 创建目录 /develop/docker-volumes/halo-mysql/data

3. 创建目录 /develop/docker-volumes/halo-mysql/conf ，并在其中创建 halo.conf，内容如下

   ```
   [mysqld]
   skip-name-resolve
   character_set_server=utf8
   datadir=/var/lib/mysql
   server-id=1000
   ```

4. 进行挂载

   ```
   docker run \
     --name halo-mysql \
     -e MYSQL_ROOT_PASSWORD=halo \
     -p 3307:3306 \
     -v /develop/docker-volumes/halo-mysql/conf/halo.conf:/etc/mysql/conf.d/halo.conf \
     -v /develop/docker-volumes/halo-mysql/data:/var/lib/mysql \
     -d \
     mysql:latest
   ```

## Dockerfile 自定义镜像

### 镜像结构

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包而成。

+ 基础镜像（Base Image）：应用依赖的系统函数库、环境、配置、文件等
+ 层（ Layer ）：在 Base Image 基础上添加安装包、依赖、配置等，每次操作都形成新的一层。
+ 入口（Entry Point）：镜像运行入口，一般是程序启动的脚本和参数

简单来说，镜像就是在系统函数库、运行环境基础上，添加应用程序文件、配置文件、依赖文件等组合，然后编写好启动脚本打包在一起形成的文件。

我们要构建镜像，其实就是实现上述打包的过程。

### Dockerfile 语法

构建自定义的镜像时，并不需要一个个文件去拷贝，打包。

我们只需要告诉 Docker，我们的镜像的组成，需要哪些 Base Image、需要拷贝什么文件、需要安装什么依赖、启动脚本是什么，将来 Docker 会帮助我们构建镜像。

而描述上述信息的文件就是 Dockerfile 文件。

Dockerfile 就是一个文本文件，其中包含一个个的指令（Instruction），用指令来说明要执行什么操作来构建镜像。每一个指令都会形成一层 Layer。

| 指令       | 说明                                           | 示例                          |
| ---------- | ---------------------------------------------- | ----------------------------- |
| FROM       | 指定基础镜像                                   | `FROM centos:6`               |
| ENV        | 设置环境变量，可在后面指令使用                 | `ENV key value`               |
| COPY       | 拷贝本地文件到镜像的指定目录                   | `COPY ./mysql-5.7.rpm /tmp`   |
| RUN        | 执行 Linux 的 shell 命令，一般是安装过程的命令 | `RUN yum install gcc`         |
| EXPOSE     | 指定容器运行时监听的端口，是给镜像使用者看的   | `EXPOSE 8080`                 |
| ENTRYPOINT | 镜像中应用的启动命令，容器运行时调用           | `ENTRYPOINT java -jar xx.jar` |

更新详细语法说明，请参考官网文档： https://docs.docker.com/engine/reference/builder

### 直接构建 Java 项目

需求：基于 Ubuntu 镜像构建一个新镜像，运行一个 Java 项目

1. Linux 下新建一个空文件夹 docker-demo

2. 拷贝 docker-demo.jar 文件到 docker-demo 这个目录

3. 拷贝 jdk8.tar.gz 文件到 docker-demo 这个目录

4. 拷贝 Dockerfile 到 docker-demo 这个目录，其中的内容如下：

   ```dockerfile
   # 指定基础镜像
   FROM ubuntu:16.04
   # 配置环境变量，JDK的安装目录
   ENV JAVA_DIR=/usr/local
   
   # 拷贝jdk和java项目的包
   COPY ./jdk8.tar.gz $JAVA_DIR/
   COPY ./docker-demo.jar /tmp/app.jar
   
   # 安装JDK
   RUN cd $JAVA_DIR \
    && tar -xf ./jdk8.tar.gz \
    && mv ./jdk1.8.0_144 ./java8
   
   # 配置环境变量
   ENV JAVA_HOME=$JAVA_DIR/java8
   ENV PATH=$PATH:$JAVA_HOME/bin
   
   # 暴露端口
   EXPOSE 8090
   # 入口，java项目的启动命令
   ENTRYPOINT java -jar /tmp/app.jar
   ```

5. 进入 docker-demo ，运行命令：

   ```sh
   docker build -t javaweb:1.0 .
   ```

6. 运行测试

### 基于 java8-alpine  构建 Java 项目

虽然我们可以基于 Ubuntu 基础镜像，添加任意自己需要的安装包，构建镜像，但是却比较麻烦。所以大多数情况下，我们都可以在一些安装了部分软件的基础镜像上做改造。

例如，构建 Java 项目的镜像，可以在已经准备了JDK 的基础镜像基础上构建。

需求：基于 java:8-alpine 镜像，将一个 Java 项目构建为镜像

实现思路如下：

+  新建一个空的目录，然后在目录中新建一个文件，命名为 Dockerfile

+ 拷贝 docker-demo.jar 到这个目录中

+ 编写 Dockerfile 文件，内容如下：

  ```dockerfile
  FROM java:8-alpine
  COPY ./app.jar /tmp/app.jar
  EXPOSE 8090
  ENTRYPOINT java -jar /tmp/app.jar
  ```

+ 进入 docker-demo ，运行命令：

  ```
  docker build -t javaweb:2.0 .
  ```

### Dockerfile 小结

1. Dockerfile 的本质是一个文件，通过指令描述镜像的构建过程

2. Dockerfile 的第一行必须是 FROM，从一个基础镜像来构建

3. 基础镜像可以是基本操作系统，如 Ubuntu。也可以是其他人制作好的镜像，例如：java:8-alpine

## Docker Compose

Docker Compose 可以基于 Compose 文件帮我们快速的部署分布式应用，而无需手动一个个创建和运行容器。

### 初识 Docker Compose

Compose 文件是一个文本文件，通过指令定义集群中的每个容器如何运行。格式如下：

```json
version: "3.8"
 services:
  mysql:
    image: mysql:5.7.25
    environment:
     MYSQL_ROOT_PASSWORD: 123 
    volumes:
     - "/tmp/mysql/data:/var/lib/mysql"
     - "/tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf"
  web:
    build: .
    ports:
     - "8090:8090"
```

上面的 Compose 文件就描述一个项目，其中包含两个容器：

- mysql：一个基于 `mysql:5.7.25` 镜像构建的容器，并且挂载了两个目录
- web：一个基于 `docker build` 临时构建的镜像容器，映射端口时 8090

Docker Compose 的详细语法参考官网：https://docs.docker.com/compose/compose-file/

其实 Docker Compose 文件可以看做是将多个 docker run 命令写到一个文件，只是语法稍有差异。

### 安装 Docker Compose

1. 下载 docker-compose，Linux 下需要通过命令下载（速度较慢）：

   ```sh
   curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
   ```

2. 修改文件权限

   ```sh
   chmod +x /usr/local/bin/docker-compose
   ```

配置 Base 自动补全命令

```sh
curl -L https://raw.githubusercontent.com/docker/compose/1.29.1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

如果这里出现错误，需要修改自己的 hosts 文件：

```sh
echo "199.232.68.133 raw.githubusercontent.com" >> /etc/hosts
```

### 部署微服务集群

需求：将之前学习的 spring-cloud-demo 微服务集群利用 Docker Compose 部署

实现思路：

1. 编写 docker-compose 文件
2. 修改自己的 cloud-demo 项目，将数据库、nacos 地址都命名为 docker-compose 中的服务名
3. 使用 maven 打包工具，将项目中的每个微服务都打包为 app.jar
4. 将打包好的 app.jar 拷贝到 cloud-demo 中的每一个对应的子目录中
5. 到 cloud-demo 目录，利用 `docker-compose up -d` 来部署

## Docker 镜像仓库 

### 搭建私有镜像仓库

搭建镜像仓库可以基于 Docker 官方提供的 Docker Registry 来实现。

官网地址：https://hub.docker.com/_/registry

#### 简化版镜像仓库

Docker 官方的 Docker Registry 是一个基础版本的 Docker 镜像仓库，具备仓库管理的完整功能，但是没有图形化界面。

搭建方式比较简单，命令如下：

```sh
docker run -d \
    --restart=always \
    --name registry	\
    -p 5000:5000 \
    -v registry-data:/var/lib/registry \
    registry
```

命令中挂载了一个数据卷 registry-data 到容器内的 /var/lib/registry 目录，这是私有镜像库存放数据的目录。

访问 http://halo:5000/v2/_catalog 可以查看当前私有镜像服务中包含的镜像

#### 带有图形化界面版本

使用 Docker Compose 部署带有图象界面的 Docker Registry，命令如下：

```yaml
version: '3.0'
services:
  registry:
    image: registry
    volumes:
      - ./registry-data:/var/lib/registry
  ui:
    image: joxit/docker-registry-ui:static
    ports:
      - 8181:80
    environment:
      - REGISTRY_TITLE=Halo-Docker-Registry
      - REGISTRY_URL=http://registry:5000
    depends_on:
      - registry
```

配置 Docker 信任地址，私服采用的是 http 协议，默认不被 Docker 信任，所以需要做一个配置：

```sh
# 打开要修改的文件
vi /etc/docker/daemon.json
# 添加内容：
"insecure-registries":["http://halo:8181"]
# 重加载
systemctl daemon-reload
# 重启docker
systemctl restart docker
```

### 推送、拉取镜像

推送镜像到私有镜像服务必须先 tag，步骤如下：

① 重新 tag 本地镜像，名称前缀为私有仓库的地址：halo:8181/

```sh
docker tag nginx:latest halo:8181/nginx:1.0 
```

② 推送镜像

```sh
docker push halo:8181/nginx:1.0 
```

③ 拉取镜像

```
docker pull halo:8181/nginx:1.0 
```



