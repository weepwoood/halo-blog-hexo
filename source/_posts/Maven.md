---
title: Maven
tags:
  - Java
  - Maven
categories:
  - Java
date: 2020-03-21 20:17:36
---


Maven是一个Java项目管理和构建工具，它可以定义项目结构、项目依赖，并使用统一的方式进行自动化构建，是Java项目不可缺少的工具。可以管理项目的整个声明周期，包括清除、编译，测试，报告、打包、部署等等。

---

Maven是专门为Java项目打造的管理和构建工具，它的主要功能有：

- 提供了一套标准化的项目结构；

- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；

- 提供了一套依赖管理机制。

## Maven项目结构

一个使用Maven管理的普通的Java项目，它的目录结构默认如下：

```ascii
a-maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```

项目的根目录`a-maven-project`是项目名，它有一个项目描述文件`pom.xml`，存放Java源码的目录是`src/main/java`，存放资源文件的目录是`src/main/resources`，存放测试源码的目录是`src/test/java`，存放测试资源的目录是`src/test/resources`，最后，所有编译、打包生成的文件都放在`target`目录里。这些就是一个Maven项目的标准目录结构。

我们再来看最关键的一个项目描述文件`pom.xml`，它的内容长得像下面：

```xml
<project ...>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.itranswarp.learnjava</groupId>
  <artifactId>hello</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>
  <properties>
        ...
  </properties>
  <dependencies>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
  </dependencies>
</project>
```

其中，`groupId`类似于Java的包名，通常是公司或组织名称，`artifactId`类似于Java的类名，通常是项目名称，再加上`version`，一个Maven工程就是由`groupId`，`artifactId`和`version`作为唯一标识。我们在引用其他第三方库的时候，也是通过这3个变量确定。例如，依赖`commons-logging`：

## Maven配置

### Maven 安装

以Windows为例，其他系统安装可以参考[菜鸟教程](https://www.runoob.com/maven/maven-setup.html)

**第一步：**进入官网[下载](http://maven.apache.org/download.cgi)或者直接去[清华镜像下载](https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/)

![image-20200220220803063]( http://img.whl123456.top/image/image-20200220220737178.png)

不同平台下载对应的包：Windows下选择Binary zip archive下载

![image-20200220220737178]( http://img.whl123456.top/image/image-20200220220803063.png)

**第二步：**解压压缩包，然后设置环境变量

新建系统变量 **MAVEN_HOME**，变量值：为解压后目录的绝对地址

![image-20200220222023598]( http://img.whl123456.top/image/image-20200220222023598.png)

编辑系统变量 **Path**，新建变量值：**%MAVEN_HOME%\bin**

**第三步：**在控制台输入`mvn -v`，如果能看到 Maven 相关版本信息，则说明 Maven 已经安装成功：

```shell
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: D:\Apache-Maven-3.6.3\bin\..
Java version: 13.0.1, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jdk-13.0.1
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

### Maven镜像

中国区用户可以使用阿里云提供的[Maven镜像仓库](https://help.aliyun.com/document_detail/102512.html?spm=a2c4e.11153940.0.0.213c7bdeWnIr1i)。使用Maven镜像仓库需要一个配置，在安装目录下进入conf目录，`settings.xml`配置文件，找到 `<mirrors>`如下：

```xml
  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |     -->
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云公共仓库</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
  </mirrors>
```

### 设置本地库

继续在`settings.xml`文件中找到`<localRepository>`中间填入本地库
![image-20200220223951120]( http://img.whl123456.top/image/image-20200305202841108.png)

### IDEA中配置Maven

![image-20200220224606360]( http://img.whl123456.top/image/image-20200220224606360.png)

注意**For current project**仅对当前项目有效

如果想要对所有项目生效需要在**欢迎页**进行上述设置

![image-20200305202841108]( http://img.whl123456.top/image/image-20200220223951120.png)

自动下载变化后的依赖

![image-20200220225318659]( http://img.whl123456.top/image/image-20200220225318659.png)

## 有关概念

### 仓库

#### 本地仓库

Maven会把我们项目所构建出来的jar包等等资源存放在本地仓库中。当我们需要jar包的时候，Maven第一时间也是去本地仓库中寻找jar包

#### 中心仓库

当Maven在本地仓库和私服找不到我们需要的jar包的时候，就去中心仓库中帮我们下载对应的jar包。

#### 私有服务器

存储一些jar包的服务器，由于本地仓库不可能拥有大部分的jar包，一个团队要去开发，免不了总是去中心仓库下载，这就非常耗费时间了。

### maven软件构建的生命周期

清除--> 编译-->测试-->报告-->打包（jar\war）-->安装-->部署

![image-20200221210511211]( http://img.whl123456.top/image/image-20200221210511211.png)

### maven坐标

坐标的组成： groupId + artifactId+ version

- groupId：组id ,机构名，公司名：好比公司的id，或者是公司包名
- artifactId：构建物id ，产品名或者产品的id
- version ：版本号

> Alibaba——▶支付宝——▶5.01版本

jar包组成：artifactId-version.jar

## pom.xml

pom.xml介绍：**project object model 项目对象模型 ，它是maven核心配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Maven在导入jar包时会导入这个jar包所依赖的其他包-->
<!--Maven版本和头文件-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!--配置的GAV-->
  <groupId>top.whl123456</groupId>
  <artifactId>JavaWeb</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!--项目的打包方式-->
  <!--jar:java应用-->
  <!--war:JavaWbe应用-->
  <packaging>war</packaging>

  <name>JavaWeb Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <!--配置-->
  <properties>
    <!--默认构建编码-->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--编码版本-->
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <!--项目依赖-->
  <dependencies>
    <!--具体依赖的jar包-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>

    </dependency>
  </dependencies>

  <!--项目构建需要的插件-->
  <build>
    <finalName>JavaWeb</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>

    <!--在build中配置resource,来防止资源导出失败的问题-->
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>

</project>

```

