---
title: Java基本程序设计结构
date: 2020-10-29 16:32:43
tags:
- Java
categories:
- Java基础
---


参考资料：

+ 《Java核心技术 卷Ⅰ》（第11版）
+ 《Java编程思想》（第4版）
+ 《OnJava8》

---

## 一个简单的Java程序

```java
public class Hello {
    // Java程序的入口方法，程序将从这里开始执行
    public static void main(String[] args){
        // 向控制台打印一条语句，Java程序严格区分大小写
        System.out.println("Hello Word!");
    }
}
```

## 注释

注释是使用自然语言编写的文本，供某一程序的人类读者阅读。Java 编译器会忽略注释。Java 支持三种注释。

第一种是单行注释，以 `//` 字符开始，直到行尾结束。

```Java
// 这是注释...
```

第二种是多行注释，以 `/*` 字符开始，不管有多少行，直到 `*/` 字符结束，一般用于多行注释。这种注释不能嵌套，即 `/* */` 中不能再有 `/* */`。编写多行注释时，程序员经常使用额外的 `*` 字符，突出注释的内容。

```Java
/*
 * 这是注释
 * blablabla...
 * 这也是注释
 */
```

第三种注释是第二种的一个特例。如果注释以 `/**` 开头，会被当成特殊的文档注释。和普通的多行注释一样，文档注释也以 `*/` 结尾，而且不能嵌套。如果你编写了一个 Java 类，希望让其他程序员使用，可以直接在源码中嵌入关于这个类和其中每个方法的文档。名为 javadoc 的程序会提取这些文档，经过处理后生成这个类的在线文档。文档注释中可以包含 HTML 标签和 javadoc 能理解的其他句法。例如：

```Java
/**
 * 可以用来自动创建文档的注释
 *
 * @auther lanqilu
 */
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

这种特殊的多行注释需要写在类和方法的定义处，可以用于自动创建文档。

类文档标记

+ `@version 版本信息`
+ `@author 作者信息`

方法文档标记

+  `@param 参数名 说明`
+ `@return 说明`
+ `@exception 完整类名 说明`
+ `@deprecated`

Java程序对格式没有明确的要求，多几个空格或者回车不影响程序的正确性，但是我们要养成良好的编程习惯，注意遵守Java社区约定的编码格式。



## 数据类型

Java是一种强类型语言。这就意味着必须为每一个变量声明一种类型。

基本数据类型是CPU可以直接进行运算的类型。Java 支持八种基本数据类型，包括一种布尔类型、一种字符类型、四种整数类型和两种浮点数类型：

+ 整数类型：byte，short，int，long
+ 浮点数类型：float，double
+ 字符类型：char
+ 布尔类型：boolean

除了基本数据类型之外，Java 还支持称为引用类型的非基本数据类型：

+ 类（class）
+ 接口（interface）
+ 数组（array）

> Java有一个能够表示任意精度的算术包，通常称为“大数”（big number）。虽然称为大数但它并不是一种基本Java类型，而是一个Java对象。







## 变量与常量













