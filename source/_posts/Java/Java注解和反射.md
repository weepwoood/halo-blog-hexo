---
title: Java注解和反射
tags:
  - Java
categories:
  - [Java]
date: 2020-06-13 21:59:02
---


Java注解和反射

---

## 反射

Java 反射技术应用广泛，它能够配置：类的全限定名、方法和参数，完成对象的初始化，甚至是反射某些方法。可以大大增强 Java 的可配置性。

反射：框架设计的灵魂
框架：半成品软件。可以在框架的基础上进行软件开发，简化编码
反射：将类的各个组成部分封装为其他对象，这就是反射机制

### 通过反射构建对象

在 Java 中允许通过反射配置信息构建对象，比如 `ReflectServicelmpl` 类

```java
public class ReflectServiceImpl {
	public void sayHello(String name) {
		System.err.println("Hello " + name);
	}
}
```

然后通过反射的方法去构建它

```java
public ReflectServiceImpl getInstance() {
    ReflectServiceImpl object = null;
    try {
        object = (ReflectServiceImpl) Class.forName("com.lean.ssm.chapter2.reflect.ReflectServiceImpl")
            .newInstance();
    } catch (ClassNotFoundException | InstantiationException | IllegalAccessException ex) {
        ex.printStackTrace();
    }
    return object;
}
```

2020年7月10日





### 获取Class对象

+ 源代码阶段：`Class.forName(“全类名”)`将字节码文件加载进内存，返回Class对象，多用于配置文件

+ 已经加载到内存中`类名.class`，多用于参数传递

+ `对象.getClass`，多用于对象的获取字节码的方式

> 同一个字节码文件在一次程序运行中，只会加载一次，不论通过哪一种方式创建

### **获取成员变量(Field)**

+ `getFields()`获取public修饰的成员变量
+ `getField()`获取指定名称的public修饰的成员变量
+ `getDeclaredFields()`获取所有的成员变量，不考虑修饰符

> 操作：
>
> 1. 设置值`set()`
> 2. 获取值`get()`
> 3. 忽略访问权限修饰符的安全检查`setAccessible(true)`

### **获取构造方法**

构造方法用来创建对象

`getConstructor()`获取构造方法

`newInstance()`创建对象

### **获取成员方法**

成员方法用来执行方法

`getMethod()`获取成员方法

`invoke()`执行方法







## 注解 Annotation

JDK1.5之后的新特性

### 基本内置注解

+ `@Override`：检测被标注的方法是否继承于父类(接口)

+ `Deprecated`：被注解的内容已过时

+ `@SuppressWarning`：压制警告编译器

  > 一般传递参数`all`  `@SuppressWarning(“all”)`

### 定义注解

格式

```java
元注解
public @interface 注解名称{
    属性列表
}
```

本质：是一个接口，默认继承Annotation接口

```java
public interface 注解名称 extends java.lang.annotation.Annotation{}
```

属性： 接口中的抽象方法

1. 不能定义`void`方法

2. 定义了属性，使用时需要对属性赋值，定义时使用default，则使用默认值

3. 如果只有一个属性且属性名时`value`，定义时则可以省略`value`直接赋值
4. 数组赋值需要使用`{}`包裹，只有一个值时可以省略`{}`

#### 元注解

用于描述注解的注解

**`@Target`：描述注解能够作用的位置**

```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE,TYPE_PARAMETER,TYPE_USE})
```

> ElementType取值：
>
> + TYPE：可以作用于类上
> + METHOD：可以作用于方法上
> + FIELD：可以作用于成员方法上

**`@Retention`：描述注解被保留的阶段**

```java
@Retention(RetentionPolicy.RUNTIME)//当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
```

```java
@Retention(RetentionPolicy.CLASS)//当前被描述的注解，会保留到class字节码文件中但不会被JVM读取到
```

```java
@Retention(RetentionPolicy.SOURCE)//不会保留到class字节码文件
```

**`@Documented`：描述注解是否被抽取到API文档中**

**`@Inherited`：描述注解是否被子类继承**