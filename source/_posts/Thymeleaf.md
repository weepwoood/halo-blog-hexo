---
title: Thymeleaf
date: 2020-10-29 17:59:02
tags:
- Thymeleaf
categories:
- JavaEE
summary: Thymeleaf是一个Java XML / XHTML / HTML5 模板引擎 ，可以在Web（基于servlet ）和非Web环境中工作。 它更适合在基于MVC的Web应用程序的视图层提供XHTML / HTML5，但它甚至可以在脱机环境中处理任何XML文件。 它提供完整的Spring Framework。
---

## Thymeleaf 基础知识

Thymeleaf 试一个模板引擎，以便显示由应用程序生成的数据或文本。它适合在Web应用程序中为HTML5提供服务，也可以处理任何XML文件。Thymeleaf具有开箱即用的特点：允许处理XML、HTML、JavaScript、CSS、普通文本等模板，每种模板都称为模板模式。

Thymeleaf 命名空间的被声明为 `th:*`属性，代码示例如下所示

```html
<html xmlns:th="http://www.thymeleaf.org">
```

## Thymeleaf的标准表达式

Thymeleaf 的标准表达式主要包括以下8类

### 简单表达式

|  语法  |              名称              |      描述      |          作用          |
| :----: | :----------------------------: | :------------: | :--------------------: |
| `${…}` |      Variable Expressions      |   变量表达式   |   取出上下文变量的值   |
| `*{…}` | Selection Variable Expressions | 选择变量表达式 | 取出选择的对象的属性值 |
| `#{…}` |      Message Expressions       |   消息表达式   | 使文字消息国际化，I18N |
| `@{…}` |      Link URL Expressions      |   链接表达式   | 用于表示各种超链接地址 |
| `~{…}` |      Fragment Expressions      |   片段表达式   | 引用一段公共的代码片段 |

### 字面量



参考资料:《SpringBoot 开发实战》吴胜