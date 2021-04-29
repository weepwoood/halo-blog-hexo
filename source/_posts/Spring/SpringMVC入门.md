---
title: SpringMVC入门
date: 2020-11-03 23:42:02
tags:
- SpringMVC
categories:
- Java框架
summary: 快速搭建一个SpringMVC项目
---

## 快速入门

本小节，我们会使用 [`spring-boot-starter-web`](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web) 实现 SpringMVC 的自动化配置。然后实现用户的增删改查接口。接口列表如下：

| 请求方法 | URL           | 功能                   |
| :------- | :------------ | :--------------------- |
| `GET`    | `/users`      | 查询用户列表           |
| `GET`    | `/users/{id}` | 获得指定用户编号的用户 |
| `POST`   | `/users`      | 添加用户               |
| `PUT`    | `/users/{id}` | 更新指定用户编号的用户 |
| `DELETE` | `/users/{id}` | 删除指定用户编号的用户 |

## 有关注解

### @Controller

[`@Controller`](https://github.com/spring-projects/spring-framework/blob/master/spring-context/src/main/java/org/springframework/stereotype/Controller.java) 注解，添加在类上，表示这是控制器 Controller 对象。属性如下：

- `name` 属性：该 Controller 对象的 Bean 名字。允许空。

[`@RestController`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/RestController.java) 注解，添加在类上，是 `@Controller` 和 [`@ResponseBody`](https://github.com/ndimiduk/spring-framework/blob/master/org.springframework.web/src/main/java/org/springframework/web/bind/annotation/ResponseBody.java) 的组合注解，直接使用接口方法的返回结果，经过 JSON/XML 等序列化方式，最终返回。也就是说，无需使用 InternalResourceViewResolver 解析视图，返回 HTML 结果。

目前主流的架构，都是 [前后端分离](https://blog.csdn.net/fuzhongmin05/article/details/81591072) 的架构，后端只需要提供 API 接口，仅仅返回数据。而视图部分的工作，全部交给前端来做。也因此，我们项目中 99.99% 使用 `@RestController` 注解。

### @RequestMapping

[`@RequestMapping`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/RequestMapping.java) 注解，添加在类或方法上，标记该类/方法对应接口的配置信息。

`@RequestMapping` 注解的**常用属性**，如下：

- `path` 属性：接口路径。`[]` 数组，可以填写多个接口路径。
- `values` 属性：和 `path` 属性相同，是它的别名。
- `method` 属性：请求方法 [RequestMethod](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/RequestMethod.java) ，可以填写 `GET`、`POST`、`POST`、`DELETE` 等等。`[]` 数组，可以填写多个请求方法。如果为空，表示匹配所有请求方法。

`@RequestMapping` 注解的**不常用属性**，如下：

- `name` 属性：接口名。一般情况下，我们不填写。
- `params` 属性：请求参数需要包含值的**参数名**。可以填写多个参数名。如果为空，表示匹配所有请你求方法。
- `headers` 属性：和 `params` 类似，只是从参数名变成**请求头**。
- `consumes` 属性：和 `params` 类似，只是从参数名变成请求头的**提交内容类型**( [Content-Type](https://juejin.im/post/5cb34fc06fb9a068a75d3555) )
- `produces` 属性：和 `params` 类似，只是从参数名变成请求头的( [Accept](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept) )**可接受类型**。

考虑到让开发更加方便，Spring 给每种请求方法提供了对应的注解：

- [`@GetMapping`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/GetMapping.java) 注解：对应 `@GET` 请求方法的 `@RequestMapping` 注解。
- [`@PostMapping`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/PostMapping.java) 注解：对应 `@POST` 请求方法的 `@RequestMapping` 注解。
- [`@PutMapping`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/PutMapping.java) 注解：对应 `@PUT` 请求方法的 `@RequestMapping` 注解。
- [`@DeleteMapping`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/DeleteMapping.java) 注解：对应 `@DELETE` 请求方法的 `@RequestMapping` 注解。
- ...

### @RequestParam

[`@RequestParam`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/RequestParam.java) 注解，添加在方法参数上，标记该方法参数对应的请求参数的信息。属性如下：

- `name` 属性：对应的请求参数名。如果为空，则直接使用方法上的参数变量名。
- `value` 属性：和 `name` 属性相同，是它的别名。
- `required` 属性：参数是否必须传。默认为 `true` ，表示必传。
- `defaultValue` 属性：参数默认值。

[`@PathVariable`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/PathVariable.java) 注解，添加在方法参数上，标记接口路径和方法参数的映射关系。具体的，我们在示例中来看。相比 `@RequestParam` 注解，少一个 `defaultValue` 属性。



## 引入依赖

在 [`pom.xml`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-23/lab-springmvc-23-01/pom.xml) 文件中，引入相关依赖。

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>lab-springmvc-23-01</artifactId>

    <dependencies>
        <!-- 实现对 Spring MVC 的自动化配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- 方便等会写单元测试 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

</project>
```

## 创建Application

创建 [`Application.java`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-23/lab-springmvc-23-01/src/main/java/cn/iocoder/springboot/lab23/springmvc/Application.java) 类，配置 `@SpringBootApplication` 注解即可。代码如下：

```java
@SpringBootApplication
public class HelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }
}
```

## 创建UserController

在controller包路径下，创建 [UserController](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-23/lab-springmvc-23-01/src/main/java/cn/iocoder/springboot/lab23/springmvc/controller/UserController.java) 类。代码如下：

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * 查询用户列表
     *
     * @return 用户列表
     */
    @GetMapping("")
    public List<UserVO> list() {
        // 查询列表
        List<UserVO> result = new ArrayList<>();
        result.add(new UserVO().setId(1).setUsername("yudaoyuanma"));
        result.add(new UserVO().setId(2).setUsername("woshiyutou"));
        result.add(new UserVO().setId(3).setUsername("chifanshuijiao"));
        // 返回列表
        return result;
    }

    /**
     * 获得指定用户编号的用户
     *
     * @param id 用户编号
     * @return 用户
     */
    @GetMapping("/{id}")
    public UserVO get(@PathVariable("id") Integer id) {
        // 查询并返回用户
        return new UserVO().setId(id).setUsername("username:" + id);
    }

    /**
     * 获得指定用户编号的用户
     *
     * @param id 用户编号
     * @return 用户
     */
    @GetMapping("/v2/{id}")
    public UserVO get2(@PathVariable("id") Integer id) {
        return userService.get(id);
    }

    /**
     * 添加用户
     *
     * @param addDTO 添加用户信息 DTO
     * @return 添加成功的用户编号
     */
    @PostMapping("")
    public Integer add(UserAddDTO addDTO) {
        // 插入用户记录，返回编号
        Integer returnId = 1;
        // 返回用户编号
        return returnId;
    }

    /**
     * 更新指定用户编号的用户
     *
     * @param id        用户编号
     * @param updateDTO 更新用户信息 DTO
     * @return 是否修改成功
     */
    @PutMapping("/{id}")
    public Boolean update(@PathVariable("id") Integer id, UserUpdateDTO updateDTO) {
        // 将 id 设置到 updateDTO 中
        updateDTO.setId(id);
        // 更新用户记录
        Boolean success = true;
        // 返回更新是否成功
        return success;
    }

    /**
     * 删除指定用户编号的用户
     *
     * @param id 用户编号
     * @return 是否删除成功
     */
    @DeleteMapping("/{id}")
    public Boolean delete(@PathVariable("id") Integer id) {
        // 删除用户记录
        Boolean success = false;
        // 返回是否更新成功
        return success;
    }

}
```

