---
title: Spring MVC
tags:
  - Spring
  - Java
categories:
  - [Spring]
date: 2020-07-10 16:40:35
---


长期以来 Struts2 与 Spring 的结合一直存在很多的问题，比如兼容性和类臃肿。加之近年来 Struts2 漏洞问题频发，导致使用率大减。与此同时，生于 Spring Web 项目的 MVC (Model View Controller ）框架走到了我们的面前，Spring MVC 结构层次清晰，类比较简单，并且与 Spring 的核心 IoC 和 AOP 无缝对接，成为了互联网时代的主流框架。

MVC 模式把应用程序（输入逻辑、业务逻辑和 UI 逻辑）分成不同的方面，同时提供这些元素之间的松耦合。

+ Model （模型），封装了应用程序的数据和由它们组成的 POJO 。
+ View （视图） ，负责把模型数据渲染到视图上，将数据以一定的形式展现给用户。
+ Controller （控制器），负责处理用户请求，并建立适当的模型把它传递给视图渲染。

在 Spring MVC 中还可以定义逻辑视图，通过其提供的视图解析器就能够很方便地找到对应的视图进行渲染，或者使用其消息转换的功能，比如在 `Controller` 的方法内加入注解`@ResponseBody` 后，Spring MVC 就可以通过其消息转换系统，将数据转换为 JSON，提供给前端 Ajax 请求使用。

Spring MVC 中的重点在于它的流程和一些重要的注解，包括控制器、视图解析器、视图等重要内容。

## 注解

> 参考[文档](http://www.iocoder.cn/Spring-Boot/SpringMVC/?self)

### `@Controller`

[`@Controller`](https://github.com/spring-projects/spring-framework/blob/master/spring-context/src/main/java/org/springframework/stereotype/Controller.java) 注解，添加在类上，表示这是控制器 Controller 对象。属性如下：

- `name` 属性：该 Controller 对象的 Bean 名字。允许空。

[`@RestController`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/RestController.java) 注解，添加在类上，是 `@Controller` 和 [`@ResponseBody`](https://github.com/ndimiduk/spring-framework/blob/master/org.springframework.web/src/main/java/org/springframework/web/bind/annotation/ResponseBody.java) 的组合注解，直接使用接口方法的返回结果，经过 JSON/XML 等序列化方式，最终返回。也就是说，无需使用 InternalResourceViewResolver 解析视图，返回 HTML 结果。

目前主流的架构，都是 [前后端分离](https://blog.csdn.net/fuzhongmin05/article/details/81591072) 的架构，后端只需要提供 API 接口，仅仅返回数据。而视图部分的工作，全部交给前端来做。也因此，我们项目中 99.99% 使用 `@RestController` 注解。

### `@RequestMapping`

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
- 还有其它几个，就不一一列举了。

### `@RequestParam`

[`@RequestParam`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/RequestParam.java) 注解，添加在方法参数上，标记该方法参数对应的请求参数的信息。属性如下：

- `name` 属性：对应的请求参数名。如果为空，则直接使用方法上的参数变量名。
- `value` 属性：和 `name` 属性相同，是它的别名。
- `required` 属性：参数是否必须传。默认为 `true` ，表示必传。
- `defaultValue` 属性：参数默认值。

[`@PathVariable`](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/bind/annotation/PathVariable.java) 注解，添加在方法参数上，标记接口路径和方法参数的映射关系。具体的，我们在示例中来看。相比 `@RequestParam` 注解，少一个 `defaultValue` 属性。