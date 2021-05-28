---
title: Spring Boot 入门
tags:
  - SpringBoot
categories:
  - Spring
date: 2020-03-21 20:13:04
---

参考资料：

{% link Spring官网, https://spring.io/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/icon/Spring.svg
 %}

{% link xkcoding/spring-boot-demo, https://github.com/xkcoding/spring-boot-demo, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/GithubIcon.jpg %}

## 创建 Spring Boot 项目

### 项目环境

JDK1.8、Maven、SpringBoot2.2.4、IDEA

### 使用 IntelliJ IDEA创建

**第一步**：菜单栏中选择：File => New => Project..，我们可以看到如下图所示的创建功能窗口。

> 其中 Initial Service Url 指向的地址就是 Spring 官方提供的 Spring Initializr 工具地址，所以这里创建的工程实际上也是基于它的 Web 工具来实现的。

![创建SpringBoot项目](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/创建SpringBoot项目.png)

**第二步**：点击 Next，等待片刻后，我们可以看到如下图所示的工程信息窗口：

![设置SpringBoot项目](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/设置SpringBoot项目.png)

+ Type：使用什么构建工具，Maven还是Gradle；

+ Language：使用什么编程语言，Java、Kotlin 还是 Groovy；

**第三步**：继续点击Next，进入选择 Spring Boot 版本和依赖管理的窗口：

![添加SpringBoot项目依赖](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/添加SpringBoot项目依赖.png)

在这里值的我们关注的是，它不仅包含了Spring Boot Starter POMs中的各个依赖，还包含了 Spring Cloud 的各种依赖。

**第四步**：点击 Next，进入最后关于工程物理存储的一些细节。最后，点击Finish就能完成工程的构建了。

### 项目解析

#### 项目结构解析

![SpringBoot项目结构](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/SpringBoot项目结构.png)

+ `src/main/java` 下的程序入口：`DemoApplication`
+ `src/main/resources` 下的配置文件：`application.properties`
+ `src/test/` 下的测试入口：`DemoApplicationTests`

生成的 `DemoApplication` 和 `DemoApplicationTests` 类都可以直接运行来启动当前创建的项目，由于目前该项目未配合任何数据访问或 Web 模块，程序会在加载完 Spring 之后结束运行。
#### 项目依赖解析

打开 `pom.xml`，看看 Spring Boot 项目的依赖：

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

+ 项目元数据：创建时候输入的 Project Metadata 部分，也就是 Maven 项目的基本元素，包括：`groupId`、`artifactId`、`version`、`name`、`description`等
+ parent：继承 `spring-boot-starter-parent` 的依赖管理，控制版本与打包等内容
+ dependencies：项目具体依赖，这里包含了 `spring-boot-starter-web` 用于实现 HTTP 接口（该依赖中包含了Spring MVC）；`spring-boot-starter-test` 用于编写单元测试的依赖包。更多功能模块的使用我们将在后面的教程中逐步展开。
+ build：构建配置部分。默认使用了 `spring-boot-maven-plugin`，配合 `spring-boot-starter-parent`就可以把 Spring Boot 应用打包成JAR来直接运行。

### 启动 Spring Boot 项目

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

+ 启动主程序，进入浏览器输入[http://localhost:8080/hello](http://localhost:8080/hello)，可以看到页面返回：Hello World

#### 启动引导 Spring

创建 Spring Boot 项目后默认有一个 `DemoApplication` 类，其内容如下

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

`@SpringBootApplication` 开启了 Spring 的组件扫描和 Spring  Boot 的自动配置功能。实际上，`@SpringBootApplication` 将三个有用的注解组合在了一起。

+  Spring 的 `@Configuration` ：标明该类使用 Spring 基于 Java 的配置而不是 XML 的配置。
+ Spring 的 `@ComponentScan` ：启用组件扫描，这样你写的 Web 控制器类和其他组件才能被自动发现并注册为 Spring 应用程序上下文里的 Bean。
+ Spring Boot 的 `@EnableAutoConfiguration` ： 这个不起眼的小注解也可以称为 `@Abracadabra` ，就是这一行配置开启了 Spring Boot 自动配置的魔力，让你不用再写成篇的配置了。

`DemoApplication` 还是一个启动引导类，这里的 `main()` 方法让你可以在命令行里把该应用程序当作一个可执行 JAR 文件来运行。这里向 `SpringApplication.run()` 传递了一个 `DemoApplication` 类的引用，还有命令行参数，通过这些东西启动应用程序。

## 整合接口文档 Swagger 

{% link Swagger 参考资料, http://www.iocoder.cn/Spring-Boot/Swagger/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@master/img/网站.svg %}

{% link Swagger 配置代码, https://github.com/Lanqilu/CodeBase/tree/master/SpringBoot/HelloSwagger, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/GithubIcon.jpg %}


目前，大多数系统都采用前后端分离。在享受前后端分离的[好处](https://www.zhihu.com/question/28207685)的同时，接口联调往往成为团队效率的瓶颈，甚至产生前后端的矛盾。简单归结来说，有几方面的原因：

- 问题一，**接口设计滞后** 。后端团队往往不喜欢 API 接口设计先行，提前和前端沟通好接口。而在开发阶段的中后期，在后端提供 API 接口后，而这些接口和前端的预期有一些偏差，很容易就产生抱怨，特别是项目周期比较紧张的情况下。
- 问题二，**接口不规范** 。 当团队里没有同意明确的接口规范时，又或者代码 Review 做的不是很好的情况下，千奇百怪、各式各样的 API 接口可能就产生了。前端在对接这样的 API 接口，苦不堪言。
- 问题三，**接口文档更新不及时，或者遗忘更新** 。因为后端 API 代码和 API 接口在两个地方，我们无法保证提交 API 代码的同时，及时更新文档。有的时候，我们甚至会遗忘更新 API 接口。随着时间的流逝，API 文档和 API 接口不一致的地方越来越多，前端会对 API 接口的信任度越来越低，然后不知道不觉之中，回到原始时代，直接问后端开发 API 是什么样的。

对于以上问题有如下建议：

- **接口设计先行**。设计完成后，后端和前端进行简单沟通，看看是否能够满足诉求。
- **统一的接口规范**。一定要制定统一的接口规范文档，即使比较简陋，也能保证团队的 API 接口相对统一一致。即使错，咱也错的一模一样，而不是千奇百怪。当然，接口规范是无法覆盖到所有的场景的，借助于“接口设计先行”，我们可以提前去 Review 每个接口的设计。
- 对于**问题三**，就进入了本文的 **主角 Swagger** 。通过在 API 接口上，添加相应的 Swagger 提供的注解，自动生成 API 文档。

Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。

总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法、参数和模型紧密集成到服务器端的代码，允许 API 来始终保持同步。Swagger 让部署管理和使用功能强大的 API 从未如此简单。

### 引入依赖

在 `pom.xml`文件中，引入相关依赖。

```xml
<dependencies>
    <!-- 实现对 Spring MVC 的自动化配置 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- 引入 Swagger 依赖 -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <version>2.9.2</version>
    </dependency>

    <!-- 引入 Swagger UI 依赖，以实现 API 接口的 UI 界面 -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
        <version>2.9.2</version>
    </dependency>

</dependencies>
```

### SwaggerConfiguration

因为 Spring Boot 暂未提供 Swagger 内置的支持，所以我们需要自己定义配置类。

在 `com.halo.helloswagger.config` 包路径下，创建 SwaggerConfiguration 配置类，用于配置 Swagger 。代码如下：

```java
@Configuration
@EnableSwagger2
public class SwaggerConfiguration {

    @Bean
    public Docket createRestApi() {
        // 创建 Docket 对象
        // 文档类型，使用 Swagger2
        return new Docket(DocumentationType.SWAGGER_2)
                // 设置 API 信息
                .apiInfo(this.apiInfo())
                // 扫描 Controller 包路径，获得 API 接口
                .select()
            	// 注意改成自己的包名
                .apis(RequestHandlerSelectors.basePackage("com.halo.apidoc.controller"))
                .paths(PathSelectors.any())
                // 构建出 Docket 对象
                .build();
    }

    /**
     * 创建 API 信息
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("测试接口文档示例")
                .description("我是一段描述")
                // 版本号
                .version("1.0.0")
                // 联系人
                .contact(new Contact("Halo", "https://halo123.top", "lanqilu@foxmail.com"))
                .build();
    }
}
```

在类上，添加 [`@EnableSwagger2`](http://springfox.github.io/springfox/javadoc/2.5.0/index.html?springfox/documentation/swagger2/annotations/EnableSwagger2.html) 注解， 标记项目启用 Swagger API 接口文档。

### Application

创建 `HelloSwaggerApplication.java` 类，配置 `@SpringBootApplication` 注解即可。代码如下：

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

先暂时不启动项目。等我们添加好 Controller 

### UserController

在 `com.halo.helloswagger.controller` 包路径下，创建 `UserController` 类，提供用户 API 接口。代码如下：

```java
@RestController
@RequestMapping("/users")
@Api(tags = "用户 API 接口")
public class UserController {

    @GetMapping("/list")
    @ApiOperation(value = "查询用户列表", notes = "目前仅仅是作为测试，所以返回用户全列表")
    public List<UserVO> list() {
        // 查询列表
        List<UserVO> result = new ArrayList<>();
        result.add(new UserVO().setId(1).setUsername("yudaoyuanma"));
        result.add(new UserVO().setId(2).setUsername("woshiyutou"));
        result.add(new UserVO().setId(3).setUsername("chifanshuijiao"));
        // 返回列表
        return result;
    }

    @GetMapping("/get")
    @ApiOperation("获得指定用户编号的用户")
    @ApiImplicitParam(name = "id", value = "用户编号", paramType = "query", dataTypeClass = Integer.class, required = true, example = "1024")
    public UserVO get(@RequestParam("id") Integer id) {
        // 查询并返回用户
        return new UserVO().setId(id).setUsername(UUID.randomUUID().toString());
    }

    @PostMapping("add")
    @ApiOperation("添加用户")
    public Integer add(UserAddDTO addDTO) {
        // 插入用户记录，返回编号
        Integer returnId = UUID.randomUUID().hashCode();
        // 返回用户编号
        return returnId;
    }

    @PostMapping("/update")
    @ApiOperation("更新指定用户编号的用户")
    public Boolean update(UserUpdateDTO updateDTO) {
        // 更新用户记录
        Boolean success = true;
        // 返回更新是否成功
        return success;
    }

    @PostMapping("/delete")
    @ApiOperation(value = "删除指定用户编号的用户")
    @ApiImplicitParam(name = "id", value = "用户编号", paramType = "query", dataTypeClass = Integer.class, required = true, example = "1024")
    public Boolean delete(@RequestParam("id") Integer id) {
        // 删除用户记录
        Boolean success = false;
        // 返回是否更新成功
        return success;
    }
}

```

- 相比我们之前使用 SpringMVC 来说，我们在类和接口上，额外增加了 Swagger 提供的注解。
- 从使用习惯上，我比较喜欢先添加 SpringMVC 的注解，再添加 Swagger 的注解。
- 因为已经使用了 Swagger 的注解，所以类和方法上的注释，一般可以删除了，除非有特殊诉求。
- 其中涉及到的 POJO 类，有 `UserAddDTO`、`UserUpdateDTO`、`UserVO` 。

执行 Application 启动项目。然后浏览器访问 [http://localhost:8080//swagger-ui.html](http://localhost:8080//swagger-ui.html) 地址，就可以看到 Swagger 生成的 API 接口文档。如下图所示：

![Swagger示例](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/Swagger示例.png)

## 配置文件有关操作

{% link spring-boot-demo-properties, https://github.com/xkcoding/spring-boot-demo/tree/master/demo-properties, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/GithubIcon.jpg %}

演示如何获取配置文件的自定义配置，以及如何多环境下的配置文件信息的获取

```
main
 ├── java
 │   └── com
 │       └── halo
 │           └── properties
 │               ├── controller
 │               │   └── PropertyController.java
 │               ├── PropertiesApplication.java
 │               └── property
 │                   ├── ApplicationProperty.java
 │                   └── DeveloperProperty.java
 └── resources
     ├── application-dev.yml
     ├── application-prod.yml
     ├── application.yml
     └── META-INF
         └── additional-spring-configuration-metadata.json
```

通过修改 `application.yml` 的 `spring.profiles.active` 即可修改配置文件

```
server:
  port: 8080
  servlet:
    context-path: /demo
spring:
  profiles:
    active: prod
```

## 整合 Actuator 检查项目运行情况

演示了如何在 Spring Boot 中通过 actuator 检查项目运行情况

`pom.xml` 中添加相关依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

`application.yml` 中修改相关配置

```yml
server:
  port: 8080
  servlet:
    context-path: /demo
# 若要访问端点信息，需要配置用户名和密码
spring:
  security:
    user:
      name: root
      password: 123456
management:
  # 端点信息接口使用的端口，为了和主系统接口使用的端口进行分离
  server:
    port: 8090
    servlet:
      context-path: /sys
  # 端点健康情况，默认值"never"，设置为"always"可以显示硬盘使用情况和线程情况
  endpoint:
    health:
      show-details: always
  # 设置端点暴露的哪些内容，默认["health","info"]，设置"*"代表暴露所有可访问的端点
  endpoints:
    web:
      exposure:
        include: '*'
```

将项目运行起来之后，打开浏览器

+ 访问：http://localhost:8090/sys/actuator/mappings ，输入用户名（root）密码（123456）即可看到所有的 mapping 信息
+ 访问：http://localhost:8090/sys/actuator/beans ，输入用户名和密码即可看到所有 Spring 管理的Bean
+ 其余可访问的路径，参见[文档](https://docs.spring.io/spring-boot/docs/2.3.10.RELEASE/reference/htmlsingle/#production-ready-endpoints)

## 集成 Spring Boot Admin 可视化监控

集成 spring-boot-admin 来可视化的监控 spring-boot 程序的运行状态，可以与 actuator 互相搭配使用

### 客户端示例

`pom.xml` 

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.5</version>
    <relativePath/>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-starter-client</artifactId>
        <version>2.4.1</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 服务端示例

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-server-ui</artifactId>
        <version>2.4.1</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 小坑

在配置过程中，Spring Boot 和版本要和 admin-starter-server 对应，不然不明会报错

在项目启动后，出现 `Caused by: java.net.UnknownHostException: failed to resolve 'DESKTOP-V294IKA...` 问题，说明需要配置计算机的 host ，例如

```
127.0.0.1 DESKTOP-V294IKA
```

便解决此问题

最后结果如下

![SpringBootAdmin](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/SpringBootAdmin.png)


## 集成 LogBack 日志

演示了如何使用 logback 记录程序运行过程中的日志，以及如何配置 logback，可以同时生成控制台日志和文件日志记录，文件日志以日期和大小进行拆分生成。

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.halo</groupId>
    <artifactId>LogBack</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>LogBack</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
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

### LogBackApplication.java

```java
@SpringBootApplication
@Slf4j
public class LogBackApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(LogBackApplication.class, args);
        int length = context.getBeanDefinitionNames().length;
        log.trace("Spring boot启动初始化了 {} 个 Bean", length);
        log.debug("Spring boot启动初始化了 {} 个 Bean", length);
        log.info("Spring boot启动初始化了 {} 个 Bean", length);
        log.warn("Spring boot启动初始化了 {} 个 Bean", length);
        log.error("Spring boot启动初始化了 {} 个 Bean", length);
        try {
            int i = 0;
            int j = 1 / i;
        } catch (Exception e) {
            log.error("【SpringBootDemoLogbackApplication】启动异常：", e);
        }
    }
}
```

### logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
	<include resource="org/springframework/boot/logging/logback/defaults.xml"/>
	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<filter class="ch.qos.logback.classic.filter.LevelFilter">
			<level>INFO</level>
		</filter>
		<encoder>
			<pattern>%date [%thread] %-5level [%logger{50}] %file:%line - %msg%n</pattern>
			<charset>UTF-8</charset>
		</encoder>
	</appender>

	<appender name="FILE_INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<!--如果只是想要 Info 级别的日志，只是过滤 info 还是会输出 Error 日志，因为 Error 的级别高， 所以我们使用下面的策略，可以避免输出 Error 的日志-->
		<filter class="ch.qos.logback.classic.filter.LevelFilter">
			<!--过滤 Error-->
			<level>ERROR</level>
			<!--匹配到就禁止-->
			<onMatch>DENY</onMatch>
			<!--没有匹配到就允许-->
			<onMismatch>ACCEPT</onMismatch>
		</filter>
		<!--日志名称，如果没有File 属性，那么只会使用FileNamePattern的文件路径规则如果同时有<File>和<FileNamePattern>，那么当天日志是<File>，明天会自动把今天的日志改名为今天的日期。即，<File> 的日志都是当天的。-->
		<!--<File>logs/info.spring-boot-demo-logback.log</File>-->
		<!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
			<FileNamePattern>logs/spring-boot-demo-logback/info.created_on_%d{yyyy-MM-dd}.part_%i.log</FileNamePattern>
			<!--只保留最近90天的日志-->
			<maxHistory>90</maxHistory>
			<!--用来指定日志文件的上限大小，那么到了这个值，就会删除旧的日志-->
			<!--<totalSizeCap>1GB</totalSizeCap>-->
			<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
				<!-- maxFileSize:这是活动文件的大小，默认值是10MB,本篇设置为1KB，只是为了演示 -->
				<maxFileSize>2MB</maxFileSize>
			</timeBasedFileNamingAndTriggeringPolicy>
		</rollingPolicy>
		<!--<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">-->
		<!--<maxFileSize>1KB</maxFileSize>-->
		<!--</triggeringPolicy>-->
		<encoder>
			<pattern>%date [%thread] %-5level [%logger{50}] %file:%line - %msg%n</pattern>
			<charset>UTF-8</charset> <!-- 此处设置字符集 -->
		</encoder>
	</appender>

	<appender name="FILE_ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<!--如果只是想要 Error 级别的日志，那么需要过滤一下，默认是 info 级别的，ThresholdFilter-->
		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
			<level>Error</level>
		</filter>
		<!--日志名称，如果没有File 属性，那么只会使用FileNamePattern的文件路径规则如果同时有<File>和<FileNamePattern>，那么当天日志是<File>，明天会自动把今天的日志改名为今天的日期。即，<File> 的日志都是当天的。-->
		<!--<File>logs/error.spring-boot-demo-logback.log</File>-->
		<!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
			<FileNamePattern>logs/spring-boot-demo-logback/error.created_on_%d{yyyy-MM-dd}.part_%i.log</FileNamePattern>
			<!--只保留最近90天的日志-->
			<maxHistory>90</maxHistory>
			<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
				<!-- maxFileSize:这是活动文件的大小，默认值是10MB,本篇设置为1KB，只是为了演示 -->
				<maxFileSize>2MB</maxFileSize>
			</timeBasedFileNamingAndTriggeringPolicy>
		</rollingPolicy>
		<encoder>
			<pattern>%date [%thread] %-5level [%logger{50}] %file:%line - %msg%n</pattern>
			<charset>UTF-8</charset> <!-- 此处设置字符集 -->
		</encoder>
	</appender>

	<root level="info">
		<appender-ref ref="CONSOLE"/>
		<appender-ref ref="FILE_INFO"/>
		<appender-ref ref="FILE_ERROR"/>
	</root>
</configuration>
```

## 集成 Freemarker 模板引擎

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.10.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.halo</groupId>
    <artifactId>Freemarker</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Freemarker</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.6.5</version>
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

### resources

```
resources
 ├── application.yml
 └── templates
     ├── common
     │   └── head.ftl
     └── page
         ├── index.ftl
         └── login.ftl
```

head.ftl

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>spring-boot-template-freemarker</title>
</head>
```

index.ftl

```html
<!doctype html>
<html lang="en">
<#include "../common/head.ftl">
<body>
<div id="app" style="margin: 20px 20%">
    欢迎登录，${person.name}！
</div>
</body>
</html>
```

login.ftl

```html
<!doctype html>
<html lang="en">
<#include "../common/head.ftl">
<body>
<div id="app" style="margin: 20px 20%">
    <form action="/demo/user/login" method="post">
        用户名<input type="text" name="name" placeholder="用户名"/>
        密码<input type="password" name="password" placeholder="密码"/>
        <input type="submit" value="登录">
    </form>
</div>
</body>
</html>
```

application.yml

```yml
server:
  port: 8080
  servlet:
    context-path: /demo
spring:
  freemarker:
    suffix: .ftl
    cache: false
    charset: UTF-8
```

### java

```
com.halo.freemarker
    ├── controller
    │   ├── IndexController.java
    │   └── UserController.java
    ├── model
    │   └── Person.java
    └── FreemarkerApplication.java
```

IndexController.java

```java
@Controller
@Slf4j
public class IndexController {

    @GetMapping(value = {"", "/"})
    public ModelAndView index(HttpServletRequest request) {
        ModelAndView mv = new ModelAndView();

        Person person = (Person) request.getSession().getAttribute("person");
        if (ObjectUtil.isNull(person)) {
            mv.setViewName("redirect:/user/login");
        } else {
            mv.setViewName("page/index");
            mv.addObject(person);
        }

        return mv;
    }
}

```

UserController.java

```java
@Controller
@RequestMapping("/user")
@Slf4j
public class UserController {
    @PostMapping("/login")
    public ModelAndView login(Person person, HttpServletRequest request) {
        ModelAndView mv = new ModelAndView();

        mv.addObject(person);
        mv.setViewName("redirect:/");

        request.getSession().setAttribute("person", person);
        return mv;
    }

    @GetMapping("/login")
    public ModelAndView login() {
        return new ModelAndView("page/login");
    }
}
```

Person.java

```java
@Data
public class Person {
    private String name;
    private String password;
}
```

## 集成 Thymeleaf 模板引擎

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.halo</groupId>
    <artifactId>Thymeleaf</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Thymeleaf</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.6.5</version>
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

### resources

```
resources
 ├── application.yml
 └── templates
    ├── common
    │   └── head.html
    └── page
        ├── index.html
        └── login.html
```

application.yml

```yml
server:
  port: 8080
  servlet:
    context-path: /demo
spring:
  thymeleaf:
    mode: HTML
    encoding: UTF-8
    servlet:
      content-type: text/html
    cache: false
```

head.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head th:fragment="header">
	<meta charset="UTF-8">
	<meta name="viewport"
	      content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>spring-boot-demo-template-thymeleaf</title>
</head>
<body>
</body>
</html>
```

index.html

```html
<!doctype html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<header th:replace="~{common/head :: header}"></header>
<body>
<div id="app" style="margin: 20px 20%">
	欢迎登录，<span th:text="${user.name}"></span>！
</div>
</body>
</html>
```

login.html

```html
<!doctype html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<header th:replace="~{common/head :: header}"></header>
<body>
<div id="app" style="margin: 20px 20%">
	<form action="/demo/user/login" method="post">
		用户名<input type="text" name="name" placeholder="用户名"/>
		密码<input type="password" name="password" placeholder="密码"/>
		<input type="submit" value="登录">
	</form>
</div>
</body>
</html>
```

### java

```
com.halo.thymeleaf
    ├── controller
    │   ├── IndexController.java
    │   └── UserController.java
    ├── model
    │   └── User.java
    └── FreemarkerApplication.java
```

IndexController.java

```java
@Controller
@Slf4j
public class IndexController {

    @GetMapping(value = {"", "/"})
    public ModelAndView index(HttpServletRequest request) {
        ModelAndView mv = new ModelAndView();

        User user = (User) request.getSession().getAttribute("user");
        if (ObjectUtil.isNull(user)) {
            mv.setViewName("redirect:/user/login");
        } else {
            mv.setViewName("page/index");
            mv.addObject(user);
        }

        return mv;
    }
}

```

UserController.java

```java
@Controller
@RequestMapping("/user")
@Slf4j
public class UserController {
    @PostMapping("/login")
    public ModelAndView login(User user, HttpServletRequest request) {
        ModelAndView mv = new ModelAndView();

        mv.addObject(user);
        mv.setViewName("redirect:/");

        request.getSession().setAttribute("user", user);
        return mv;
    }

    @GetMapping("/login")
    public ModelAndView login() {
        return new ModelAndView("page/login");
    }
}

```

User.java

```java
@Data
public class User {
    private String name;
    private String password;
}

```

## 整合原生的 Mybatis

使用了 mybatis 官方提供的脚手架 `mybatis-spring-boot-starter `可以很容易的和 Spring Boot 整合。


