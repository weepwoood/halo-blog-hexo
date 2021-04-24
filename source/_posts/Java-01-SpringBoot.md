---
title: SpringBoot2
date: 2020-10-29 17:59:02
tags:
- SpringBoot
categories:
- Java框架
---









## SpringBoot实现RESTful风格Web应用

REST（REpresentational State Transfer）全称是 Resource Representational State Transfer：通俗来讲就是：资源在网络中以某种表现形式进行状态转移。分解开来：

+ Resource：资源，即数据（前面说过网络的核心）。比如 newsfeed，friends等；
+ Representational：某种表现形式，比如用JSON，XML，JPEG等；
+ State Transfer：状态变化。通过HTTP动词实现。

URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作。



### 创建类BolgController

在包controller中创建类BolgController

```Java
package com.example.restful.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.servlet.ModelAndView;


@Controller
@RequestMapping("/blog")
public class BolgController {
    @RequestMapping("/{id}")
    public ModelAndView show(@PathVariable("id") Integer id) {
        ModelAndView mav = new ModelAndView();
        mav.addObject("id", id);
        mav.setViewName("bolg");
        return mav;
    }

    @RequestMapping("/query")
    public ModelAndView query(@RequestParam(value = "q", required = false) String q) {
        ModelAndView mav = new ModelAndView();
        mav.addObject("q", q);
        mav.setViewName("query");
        return mav;
    }
}
```

### 创建文件 index.html

在 resource/static 目录下创建文件 index.html 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<a href="/blog/21">博客</a><br>
<a href="/blog/query?q=123456>">搜索</a>
</body>
</html>
```

### 创建文件 bolg.html

在 resource/templates 目录下创建文件 blog.html 

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Getting Started: Serving Web Content</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
<p th:text="'博客编号:' + ${id} + '!'"/>
</body>
</html>
```

### 创建文件 query.html

在 resource/templates 目录下创建文件 query.html 

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Getting Started: Serving Web Content</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
<p th:text="'查询,' + ${q} + '!'"/>
</body>
</html>
```

### 运行程序

运行程序后，在浏览器中输入 localhost:8080

![image-20201029185618289](E:\OneDrive - lanqilu\我的图片库\blogimg\Java-01-SpringBoot\image-20201029185618289.png)



点击博客或搜索显示如下页面



![image-20201029191916914](E:\OneDrive - lanqilu\我的图片库\blogimg\Java-01-SpringBoot\image-20201029191916914.png)



![image-20201029191930191](E:\OneDrive - lanqilu\我的图片库\blogimg\Java-01-SpringBoot\image-20201029191930191.png)



## 带 Bootstrap 和 JQuery  的 Web 应用

### 添加依赖



## 接口文档 Swagger [入门](http://www.iocoder.cn/Spring-Boot/Swagger/)

目前，大多数系统都采用前后端分离。在享受前后端分离的[好处](https://www.zhihu.com/question/28207685)的同时，接口联调往往成为团队效率的瓶颈，甚至产生前后端的矛盾。简单归结来说，有几方面的原因：

- 问题一，**接口设计滞后。** 后端团队往往不喜欢 API 接口设计先行，提前和前端沟通好接口。而在开发阶段的中后期，在后端提供 API 接口后，而这些接口和前端的预期有一些偏差，很容易就产生抱怨，特别是项目周期比较紧张的情况下。
- 问题二，**接口不规范。** 当团队里没有同意明确的接口规范时，又或者代码 Review 做的不是很好的情况下，千奇百怪、各式各样的 API 接口可能就产生了。前端在对接这样的 API 接口，苦不堪言，在一口 mmp 一嘴 fuck xxx 之中，调完接口。
- 问题三，**接口文档更新不及时，或者遗忘更新。** 因为后端 API 代码和 API 接口在两个地方，我们无法保证提交 API 代码的同时，及时更新文档。有的时候，我们甚至会遗忘更新 API 接口。随着时间的流逝，API 文档和 API 接口不一致的地方越来越多，前端会对 API 接口的信任度越来越低，然后不知道不觉之中，回到原始时代，直接问后端开发 API 是什么样的。

对于**问题一**和**问题二**，更多是开发流程上的问题，所以不在本文的范围内。当然话痨的艿艿，还是要给点粗浅的建议，完全拦不住我啊。

- **接口设计先行**。设计完成后，后端和前端进行简单沟通，看看是否能够满足诉求。
- **统一的接口规范**。一定要制定统一的接口规范文档，即使比较简陋，也能保证团队的 API 接口相对统一一致。😈 即使错，咱也错的一模一样，而不是千奇百怪。当然，接口规范是无法覆盖到所有的场景的，借助于“接口设计先行”，我们可以提前去 Review 每个接口的设计。

对于**问题三**，就进入了本文的**主角 Swagger** 。通过在 API 接口上，添加相应的 Swagger 提供的注解，自动生成 API 文档。酱紫，API 接口和文档就在一起了，从此过上了幸福快乐的生活。

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

在 `*.apidoc.config` 包路径下，创建 SwaggerConfiguration 配置类，用于配置 Swagger 。代码如下：

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
                .contact(new Contact("泷", "http://www.whl123456.top", "885240677@qq.com"))
                .build();
    }

}
```

在类上，添加 [`@EnableSwagger2`](http://springfox.github.io/springfox/javadoc/2.5.0/index.html?springfox/documentation/swagger2/annotations/EnableSwagger2.html) 注解， 标记项目启用 Swagger API 接口文档。

### Application

创建 [`Application.java`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-24/lab-24-apidoc-swagger/src/main/java/cn/iocoder/springboot/lab24/apidoc/Application.java) 类，配置 `@SpringBootApplication` 注解即可。代码如下：

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

在 `*.apidoc.controller` 包路径下，创建 [UserController](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-24/lab-24-apidoc-swagger/src/main/java/cn/iocoder/springboot/lab24/apidoc/controller/TestController.java) 类，提供用户 API 接口。代码如下：

- 相比我们之前使用 SpringMVC 来说，我们在类和接口上，额外增加了 Swagger 提供的注解。
- 从使用习惯上，我比较喜欢先添加 SpringMVC 的注解，再添加 Swagger 的注解。
- 因为已经使用了 Swagger 的注解，所以类和方法上的注释，一般可以删除了，除非有特殊诉求。
- 其中涉及到的 POJO 类，有 [UserAddDTO](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-24/lab-24-apidoc-swagger/src/main/java/cn/iocoder/springboot/lab24/apidoc/dto/UserAddDTO.java)、[UserUpdateDTO](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-24/lab-24-apidoc-swagger/src/main/java/cn/iocoder/springboot/lab24/apidoc/dto/UserUpdateDTO.java)、[UserVO](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-24/lab-24-apidoc-swagger/src/main/java/cn/iocoder/springboot/lab24/apidoc/vo/UserVO.java) 。

执行 Application 启动项目。然后浏览器访问 `http://127.0.0.1:8080/swagger-ui.html` 地址，就可以看到 Swagger 生成的 API 接口文档。如下图所示：

![Swagger-UI 示例](E:\OneDrive - lanqilu\我的图片库\blogimg\Java-01-SpringBoot\01.png)

至此，我们已经完成了 Swagger 的快速入门。不过考虑到胖友能够更好的使用，我们来一个一个注解了解。

