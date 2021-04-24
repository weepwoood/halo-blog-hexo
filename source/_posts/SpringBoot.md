---
title: SpringBoot
tags:
  - SpringBoot
  - JavaEE
categories:
  - Spring
date: 2020-03-21 20:13:04
---


[SpringBoot](https://spring.io/)

---

## 创建项目

### 项目环境

JDK1.8、Maven、SpringBoot2.2.4、IDEA

### 使用 IntelliJ IDEA创建

**第一步：**菜单栏中选择：File => New => Project..，我们可以看到如下图所示的创建功能窗口。

> 其中Initial Service Url指向的地址就是Spring官方提供的Spring Initializr工具地址，所以这里创建的工程实际上也是基于它的Web工具来实现的。

![创建项目]( http://img.whl123456.top/image/image-20200220211502308.png)

**第二步**：点击Next，等待片刻后，我们可以看到如下图所示的工程信息窗口：
![设置项目]( http://img.whl123456.top/image/image-20200220211700051.png)

+ Type：使用什么构建工具，Maven还是Gradle；

+ Language：使用什么编程语言，Java、Kotlin还是Groovy；

**第三步**：继续点击Next，进入选择Spring Boot版本和依赖管理的窗口：

![项目依赖]( http://img.whl123456.top/image/image-20200220212223616.png)

在这里值的我们关注的是，它不仅包含了Spring Boot Starter POMs中的各个依赖，还包含了Spring Cloud的各种依赖。

**第四步**：点击Next，进入最后关于工程物理存储的一些细节。最后，点击Finish就能完成工程的构建了。

### 项目解析

#### 项目结构解析

![项目结构]( http://img.whl123456.top/image/image-20200220212945331.png)

+ `src/main/java`下的程序入口：`DemoApplication`
+ `src/main/resources`下的配置文件：`application.properties`
+ `src/test/`下的测试入口：`DemoApplicationTests`

生成的`DemoApplication`和`Demo``ApplicationTests`类都可以直接运行来启动当前创建的项目，由于目前该项目未配合任何数据访问或Web模块，程序会在加载完Spring之后结束运行。

##### 项目依赖解析

打开`pom.xml`，看看Spring Boot项目的依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

如上所示，主要有四个部分：

+ 项目元数据：创建时候输入的Project Metadata部分，也就是Maven项目的基本元素，包括：`groupId`、`artifactId`、`version`、`name`、`description`等
+ parent：继承`spring-boot-starter-parent`的依赖管理，控制版本与打包等内容
+ dependencies：项目具体依赖，这里包含了`spring-boot-starter-web`用于实现HTTP接口（该依赖中包含了Spring MVC）；`spring-boot-starter-test`用于编写单元测试的依赖包。更多功能模块的使用我们将在后面的教程中逐步展开。
+ build：构建配置部分。默认使用了`spring-boot-maven-plugin`，配合`spring-boot-starter-parent`就可以把Spring Boot应用打包成JAR来直接运行。

### 启动

#### 编写一个HTTP接口

+ 在com.example.demo下面创建`HelloController`类，内容如下：

  ```java
  @RestController
  public class HelloController {
      @RequestMapping("/hello")
      public String index() {
          return "Hello World";
      }
  }
  ```

+ 启动主程序，进入[浏览器](http://localhost:8080/hello)，可以看到页面返回：Hello World



### 启动引导Spring

创建SpringBoot项目后默认有一个DemoApplication类，其内容如下

```java
// 开启组件扫描和自动配置
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        //负责启动引导应用程序
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

`@SpringBootApplication`开启了Spring的组件扫描和Spring Boot的自动配置功能。实际上，`@SpringBootApplication`将三个有用的注解组合在了一起。

+  Spring的`@Configuration`：标明该类使用Spring基于Java的配置而不是XML的配置。
+ Spring的`@ComponentScan`：启用组件扫描，这样你写的Web控制器类和其他组件才能被自动发现并注册为Spring应用程序上下文里的Bean。
+ Spring Boot 的 `@EnableAutoConfiguration` ： 这 个 不 起 眼 的 小 注 解 也 可 以 称 为`@Abracadabra`，就是这一行配置开启了Spring Boot自动配置的魔力，让你不用再写成篇的配置了。

`DemoApplication`还是一个启动引导类，这里的`main()`方法让你可以在命令行里把该应用程序当作一个可执行JAR文件来运行。这里向`SpringApplication.run()`传递了一个`DemoApplication`类的引用，还有命令行参数，通过这些东西启动应用程序。

 