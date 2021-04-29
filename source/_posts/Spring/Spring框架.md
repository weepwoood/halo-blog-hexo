---
title: Spring框架
tags:
  - Spring
categories:
  - JavaEE
  - Spring
date: 2020-03-21 20:11:20
---


Spring框架

---

## 基础知识

### 程序的耦合

耦合：程序间的依赖关系

包括：

+ 类之间的依赖
+ 方法间的依赖

解耦：降低程序间的依赖关系

实际开发中：应该做到：编译期不依赖，运行时才依赖。

解耦的思路：

+ 第一步：使用反射来创建对象，而避免使用new关键字。
+ 第二步：通过读取配置文件来获取要创建的对象全限定类名

### 工厂模式

Bean：在计算机英语中，有可重用组件的含义。

JavaBean：用java语言编写的可重用组件。JavaBean >  实体类

## Spring介绍

创建Spring的目的就是用来**替代更加重量级的的企业级Java技术**，**简化Java的开发**

+ 基于POJO轻量级和**最小侵入式开发**
+ 通过依赖注入和面向接口实现**松耦合**
+ **基于切面**和惯例进行声明式编程
+ 通过切面和模板**减少样板式代码**

> POJO（Plain Ordinary Java Object）简单的Java对象，实际就是普通JavaBeans，是为了避免和EJB混淆所创造的简称。
>
> JavaBean 是一种JAVA语言写成的可重用组件。为写成JavaBean，类必须是具体的和公共的，并且具有无参数的构造器。JavaBean 通过提供符合一致性设计模式的公共方法将内部域暴露成员属性，set和get方法获取。众所周知，属性名称符合这种模式，其他Java 类可以通过自省机制(反射机制)发现和操作这些JavaBean 的属性。

### Spring的狭义与广义

狭义上的Spring特指Spring框架（Spring Framework）

广义上的Spring是指以Spring框架为核心的Spring技术栈

## Spring框架概述

### 模块化的Spring框架

Spring框架是模块化的，允许开发人员自由选择需要使用的部分。

### 使用Spring的好处

Spring可以使用POJO来构建应用程序，并将企业服务非侵入性的应用到POJO。此功能适用于JavaSE编程模型和完全或部分的JavaEE模型

作为一个Java应用程序的开发者，可以从Spring平台获得以下好处：

+ 使本地Java方法可以执行数据库事务，而无须自己处理事务API
+ 使本地Java方法可以执行远程过程，而无须自己处理远程API
+ 使本地Java方法成为HTTP端点，而无须自己处理Servlet API
+ 使本地Java方法可以拥有管理操作，而无须自己处理JMX API
+ 使本地Java方法可以执行消息处理，而无须自己处理JMS API

### IoC

Spring框架的IoC组件就能够通过提供正规化的方法来组合不同的组件。

## 侵入式概念

Spring是一种非侵入式的框架

### 侵入式

对于EJB、Struts2等一些传统的框架，**通常是要实现特定的接口，继承特定的类才能增强功能，改变了java类的结构**

### 非侵入式

对于Hibernate、Spring等框架，**对现有的类结构没有影响，就能够增强JavaBean的功能**

## Spring IoC的概念

Spring的IoC容器是整个Spring框架的核心和基础

### Spring IoC 概述

IoC的全程为Inversion of Control，中文常翻译为“控制反转”，让别人为你服务，是一种通过描述（在Java中可以是XML或者注解）并通过第三方去产生或获取特定对象的方式。

在Spring中实现控制反转的是IoC容器，实现方法是依赖注入

“依赖注入”和“控制反转”其实就是一个事物的两种不同的说法。从技术上来讲，“依赖注入”是“控制反转”的特殊实现，但“依赖注入”还指一个对象应用另外一个对象来提供一个特殊能力。

**对象的创建交给外部容器完成，这个就做控制反转。**

> IoC的思想最核心的地方在于，资源不由使用资源的双方管理，而由不使用资源的第三方管理，这可以带来很多好处。
>
> 第一，资源集中管理，实现资源的可配置和易管理。
>
> 第二，降低了使用资源双方的依赖程度，也就是我们说的耦合度

### Spring IoC容器

无论是创建对象、处理对象之间的依赖关系、对象创建的时间还是对象的数量，我们都是在Spring为我们提供的IoC容器上配置对象的信息就好了。

Spring IoC容器的设计主要是基于`BeanFactory`和`ApplicationContext`两个接口，其中`ApplicationContext`是`BeanFactory`的子接口之一。`BeanFactory`是Spring IoC容器所定义的最底层接口，而`ApplicationContext`是其高级接口之一，并且对`BeanFactory`功能做了许多有用的扩展，故在绝大数工作情况下，都会使用`ApplicationContext`作为Spring IoC容器。

![ApplicationContext](http://img.whl123456.top/image/ApplicationContext.png)

### BeanFactory

```java
// 5.2.6.RELEASE 源码
public interface BeanFactory {

	String FACTORY_BEAN_PREFIX = "&";

	Object getBean(String name) throws BeansException;

	<T> T getBean(String name, Class<T> requiredType) throws BeansException;

	Object getBean(String name, Object... args) throws BeansException;

	<T> T getBean(Class<T> requiredType) throws BeansException;

	<T> T getBean(Class<T> requiredType, Object... args) throws BeansException;

	<T> ObjectProvider<T> getBeanProvider(Class<T> requiredType);

	<T> ObjectProvider<T> getBeanProvider(ResolvableType requiredType);

	boolean containsBean(String name);

	boolean isSingleton(String name) throws NoSuchBeanDefinitionException;

	boolean isPrototype(String name) throws NoSuchBeanDefinitionException;

	boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;

	boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;

	@Nullable
	Class<?> getType(String name) throws NoSuchBeanDefinitionException;

	@Nullable
	Class<?> getType(String name, boolean allowFactoryBeanInit) throws NoSuchBeanDefinitionException;

	String[] getAliases(String name);

}

```

![BeanFactory](E:\OneDrive - lanqilu\我的图片库\blogimg\Spring框架\BeanFactory.png)

+ `getBean`的方法多用于获取配置给Spring IoC容器的Bean。从参数类型来看可以是字符串，也可以是`Class`类型，由于`Class`类型可以扩展接口也可以继承父类，所以在一定程度上会存在使用父类类型无法准确获得实例的异常。
+ `isSingleton`用于判断是否为单例，如果判断为真，其意思是该Bean在容器中是作为一个唯一单例存在的。而`isPrototype`则相反，如果判断为真，意思是当你从容器中获取Bean，容器就为你生成一个新的实例。在默认情况下，Spring会为Bean创建一个单例，也就是默认情况下`isSingleton`返回true，而`isPrototype`返回false。
+ `getAliases`方法是获取别名的方法。

### 容器的初始化和依赖注入

Bean的定义和初始化在Spring IoC容器中的是两大步骤，它是先定义，然后初始化和依赖注入。

Bean的定义分为3步：

+ Resource定位：这步是Spring IoC容器根据开发者的配置，进行资源定位，在Spring的开发中，通过XML或者注解都是十分常见的方式，定位的内容是由开发者所提供。
+ BeanDefinition的载入：将BeanDefinition的信息保存到Bean定义中，此时不会创建Bean的实例。
+ BeanDefinition的注册：将BeanDefinition的信息发布到Spring IoC容器中，但此时仍旧没有对应的Bean的实例创建。

完成以上3步Bean就在Spring IoC容器中被定义了，而没有初始化，更没有完成依赖注入，也就是没有注入其配置的资源给Bean。

对于初始化和依赖注入，Spring Bean还有一个配置选项`lazy-init`，其含义就是是否初始化Spring Bean。在没有任何配置的情况下，它的默认值为default，实际值为false，也就是Spring IoC默认会自动初始化Bean。如果将其设置为true，那么只有当我们使用Spring IoC容器的`getBean`方法获取它时，它才会进行Bean的初始化，完成依赖注入。

### Bean的生命周期

对于Bean而言，在容器中存在其生命周期，它的初始化和销毁也需要一个过程。

![Bean的生命周期](E:\Pictures\blogimg\Spring框架.assets\Bean的生命周期.svg)

+ 如果Bean实现了`BeanNameAware`接口的`setBeanName`方法，那么它就会调用这个方法
+ 如果Bean实现了`BeanFactoryAware`接口的`setBeanFactory`方法，那么它就会调用这个方法
+ 如果Bean实现了`ApplicationContextAware`接口的`setApplicationContext`方法，且Spring IoC容器也必须是一个`ApplicationContext`接口的实现类，那么才会调用这个方法，否则是不调用
+ 如果Bean实现了`BeanPostProcessor`接口的`postProcessBeforeInitialization`方法，那么它就会调用这个方法（注意它针对全部Bean而言）
+ 如果Bean实现了`InitializingBean`接口的`afterPropertiesSet`方法，那么它就会调用这个方法
+ 如果Bean自定义了初始化方法，他就会调用已定义的初始化方法。
+ 如果Bean实现了`BeanPostProcessor`接口的`postProcessAfterInitialization`方法，完成了这些调用，这个时候Bean就完成了初始化，那么Bean就存在Spring IoC的容器中，使用者就可以从中获取Bean的服务

当服务器正常关闭，或者遇到其他关闭Spring IoC容器的事件，它就会调用对应的方法完成Bean的销毁，其步骤入下：

+ 如果Bean实现了接口`DisposableBean`的`destory`方法，那么就会调用它
+ 如果定义了自定义销毁方法，那么就会调用它

## 装配Spring Bean

### 依赖注入的3种方式

那么对象的对象之间的依赖关系Spring是怎么做的呢？？**依赖注入（Dependency Injection，DI）**

+ **Spring使用依赖注入来实现对象之间的依赖关系**
+ **在创建完对象之后，对象的关系处理就是依赖注入**

一般而言，依赖注入可以分为3种方式：

+ 构造器注入
+ setter注入
+ 接口注入

构造器注入和setter注入是主要的方式，而接口注入是从别的地方注入的方式。

#### 构造器注入

顾名思义，构造器注入依赖于构造方法实现，就是被注入对象可以通过在其构造方法中声明依赖对象的参数列表，让外部（通常是IoC容器）知道它需要哪些依赖对象。

在大部分情况下，我们通过类的构造方法来创建类对象，Spring也可以采用反射的方式通过使用构造方法来完成注入，这就是构造器注入的原理。

在配置文件中，`constructor-arg`元素用于定义类构造方法的参数。包括index方式、type方式和name方式

#### setter方法注入

setter注入式Spring中最主流的注入方式，它利用Java Bean规范所定义的setter方法来完成注入，灵活且可读性高。

它消除了使用构造器护注入时出现多个参数的可能性，首先可以把构造方法声明为无参数的，然后使用setter注入为其设置对应的值，其实也是通过Java反射技术实现的。

#### 三种注入方式的比较

+ 接口注入。从注入方式的使用上来说，接口注入是现在不甚提倡的一种方式，基本处于“退役状态”。因为它强制被注入对象实现不必要的接口，带有侵入性。而构造方法注入和setter方法注入则不需要如此。
+ 构造方法注入。这种注入方式的优点就是，对象在构造完成之后，即已进入就绪状态，可以马上使用。缺点就是，当依赖对象比较多的时候，构造方法的参数列表会比较长。而通过反射构造对象的时候，对相同类型的参数的处理会比较困难，维护和使用上也比较麻烦。而且在Java中，构造方法无法被继承，无法设置默认值。对于非必须的依赖处理，可能需要引入多个构造方法，而参数数量的变动可能造成维护上的不便。
+ setter方法注入。因为方法可以命名，所以setter方法注入在描述性上要比构造方法注入好一些。
  另外，seter方法可以被继承，允许设置默认值，而且有良好的IDE支持。缺点当然就是对象无法在构造完成后马上进入就绪状态。
+ 综上所述，构造方法注入和setter方法注入因为其侵入性较弱，且易于理解和使用，所以是现在使用最多的注入方式

### 装配Bean概述

将开发的Bean装配到Spring IoC容器中，在大部分场景下使用`ApplicationContext`的具体实现类，因为对应的Spring IoC容器功能强大。

而在Spring中提供了3种方法进行配置：

+ 在XML中显示配置
+ 在Java的接口和类中实现配置
+ 隐式Bean的发现机制和自动装配原则

在学习和工作中，这3种方式都会被用到，且常常混合使用，对于怎么选择有以下几点建议：

+ 基于约定优于配置的原则，最优先的应该时通过隐式Bean的发现机制和自动装配的原则。这样的好处是减少程序开发者的决定权，简单又不失灵活。
+ 在没有办法使用自动装配原则的情况下应该优先考虑Java接口和类中实现配置，这样的好处是避免XMl配置的泛滥，也更为容易。这种场景典型了例子是一个父类有多个子类，比如学术类有两个子类：男生类和女生类，通过IoC容器初始化一个学术类，容器将无法知道使用哪个子类去初始化，这个时候可以使用Java的注解配置去指定。
+ 上述方法都无法使用的情况下，那么只能选择XML去配置Sping IoC容器。由于现实中常常用到第三方的类库，有些类并不是我们开发的，我们无法修改里面的代码，这个时候就通过XML的方式配置使用了。

### 通过XML配置装配Bean

使用XML装配Bean需要定义对应的XML，这里需要引入对应的XML模式（XSD）文件，这些文件会定义配置Spring Bean的一些元素

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
    
</beans>
```

上述代码中引入了一个`beans`的定义，它是一个根元素，而XSD文件也被引入了，这样它所定义的元素将可以定义对应的Spring Bean

#### 装配简易值

```xml
<bean id="source" class="top.whl.pojo2.Source">
    <property name="fruit" value="橙汁"/>
    <property name="size" value="大杯"/>
    <property name="sugar" value="少糖"/>
</bean>

<bean id="juiceMaker2" class="top.whl.pojo2.JuiceMaker2">
    <property name="beverageShop" value="贡茶"/>
    <property name="source" ref="source"/>
</bean>
```

+ `id`属性是Spring找到这个Bean的编号，不过`id`不是一个必须的属性，如果没有声明它，那么Spring会采用`全限定名#{number}`的格式生成编号。例如：没有声明`id="source"`，那么Spring为其生成的编号就是`"top.whl.pojo2.Source#0"`。当第二次声明没有id的属性的Bean时，编号就是`"top.whl.pojo2.Source#1"`，以此类推。
+ `class` 是一个类的全限定名
+ `property` 元素是定义类的属性，其中`name`属性定义的是属性名称，而`value`是其值
+ `ref` 引用Spring中创建好的对象

#### 装配集合

```xml
<property name="list">
    <list>
        <value>value-list-1</value>
        <value>value-list-2</value>
        <value>value-list-3</value>
    </list>
</property>
```

`List`属性为对应的`<list>`元素进行装配，然后通过多个`<value>`元素设值

```xml
<property name="map">
    <map>
        <entry key="key1" value="value-key-1"/>
        <entry key="key2" value="value-key-2"/>
        <entry key="key3" value="value-key-3"/>
    </map>
</property>
```

`Map`属性为对应的`map`元素进行装配，然后通过多个`<entry>`元素设值，其中`entry`包含一个键（key）和一个值（value）的设置

```xml
<property name="props">
    <props>
        <prop key="prop1">value-prop-1</prop>
        <prop key="prop2">value-prop-2</prop>
        <prop key="prop3">value-prop-3</prop>
    </props>
</property>
```

`Properties`属性，为对应的`<props>`元素进行装配，通过多个`<prop>`元素设置，其中`<prop>`元素有一个必填属性key，然后在之间设置值

```xml
<property name="set">
    <set>
        <value>value-set-1</value>
        <value>value-set-2</value>
        <value>value-set-3</value>
    </set>
</property>
```

`Set`属性为对应的`<set>`元素进行装配，然后通过多个`<value>`元素设置

```xml
<property name="array">
    <array>
        <value>value-array-1</value>
        <value>value-array-2</value>
        <value>value-array-3</value>
    </array>
</property>
```

对于数组而言，可以使用`<array>`设置值，然后通过多个`<value>`元素设值。

---

有些时候需要更为复杂的装载，比如一个List可以是一系列的对象，又如Map集合类，键可以 是一个类的对象，而值也要是一个类的对象。

```xml
<property name="list">
    <list>
        <ref bean="role1"/>
        <ref bean="role2"/>
    </list>
</property>
```

`List`属性使用`<list>`元素定义注入，使用多个`<ref>`元素的Bean属性去引用之前定义好的Bean

```xml
<property name="map">
    <map>
        <entry key-ref="role2" value="user1"/>
        <entry key-ref="role2" value="user2"/>
    </map>
</property>
```

`Map`属性使用`<map>`元素定义注入，使用多个`<enty>`元素的`key-ref`属性去引用之前定义好的Bean做键，而用`value-ref`属性去引用之前定义好的Bean做值

```xml
<property name="set">
    <set>
        <ref bean="role1"/>
        <ref bean="role2"/>
    </set>
</property>
```

`Set`属性使用`<set>`元素定义注入，使用多个`<ref>`元素的Bean去引用之前定义好的Bean

#### 命名空间装配

### 通过注解装配Bean

使用注解的方式可以减少XML的配置，注解功能更为强大，它既能实现XML的功能，也提供了自动装配的功能，采用了自动装配后，程序员所需要做的决断就少了，更加有利于程序的开发，这就是“约定优于配置”的开发原则。

在Spring中，它提供了两种方式来让Spring IoC容器发现Bean

+ 组件扫描：通过定义资源的方式，让Sprnig IoC容器扫描对应的包，从而把Bean装配进来。
+ 自动装配：通过注解定义，使得一些依赖关系可以通过注解完成

#### 使用`@Component`装配Bean

```java
@Component(value = "role")
public class Role {
    @Value("1")
    private Long id;
    @Value("role_name_1")
    private String roleName;
    @Value("role_note_1")
    private String note;
    /**** setter and getter ****/
}
```

+ 注解`@Component`代表Spring IoC会把这个类扫描生成Bean实例，而其中的`value`属性代表这个类在Spring中的id，就相当于XML方式定义的Bean的id，也可以简写成`@Component(“role”)`，甚至直接写成`@Component`，对于不写的，Spring IoC容器就默认为类名，但是以首字母小写的形式作为id，为其生成对象，配置到容器中。
+ 注解`@Value`代表是是值的注入，这里只是简单注入一些值，其中id是一个long型，注入的时候Spring会为其转化类型

现在有了这个类，但是还不能进行测试，因为Spring IoC并不知道需要去哪里扫描对象，这个时候可以在同一个包下创建一个Java Config类来告诉它

```java
@ComponentScan
public class PojoConfig {
}
```

这个类十分简单但要注意`@ComponentScan`代表进行扫描，默认是扫描当前包的路径，POJO的包名和它保持一致才能进行扫描，否则不行。

接下来通过Spring定义好的Spring IoC容器实现类`AnnotationConfigApplicationContext`去生成IoC容器

```java
public class Pojo2Text {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(PojoConfig.class);
        Role role = context.getBean(Role.class);
        System.out.println("role.getId() = " + role.getId());
    }
}
```

由此可以看出两个明显的弊端：

+ 对于`@ComponentScan`注解，它只是扫描所在包的Java类，但更多的时候需要的是可以扫描所指定的类
+ 上面只注入了一些简单的值，而没有注入对象，同样在现实的开发中可以注入对象是十分重要的，也是常见的场景

`@ComponentScan`存在着两个配置项：

+ 第一个是`basePackages`，它是由base和package两个单词组成的，而package还使用了复数，意味着它可以配置一个Java包的数组，Spring会根据它的配置扫描对应的包和子包，将配置好的Bean装配起来
+ 第二个是`basePackageClasses`，Class采用复数，意味着它可以配置多个类，Spring会根据配置的类所在的包，为包和子包进行扫描装配对应配置的Bean

为验证`@ComponentScan`的配置项，首先定义一个接口

```java
package top.whl.service2;
import top.whl.pojo2.Role;
public interface RoleService {
    public void printRoleInfo(Role role);
}
```

使用接口来编写一些操作类是Spring所推荐的，它可以将定义和实现相分离。接口的实现类：

```java
package top.whl.service2.impl;

import org.springframework.stereotype.Component;
import top.whl.pojo2.Role;
import top.whl.service2.RoleService;

@Component
public class RoleServiceImpl implements RoleService {
    public void printRoleInfo(Role role) {
        System.out.println("id = " + role.getId());
        System.out.println("name = " + role.getRoleName());
        System.out.println("note = " + role.getNote());
    }
}
```

配置`@ComponentScan`指定包扫描

```java
package top.whl.config;

import org.springframework.context.annotation.ComponentScan;
import top.whl.pojo2.Role;
import top.whl.service2.RoleService;

@ComponentScan(basePackageClasses = {Role.class, RoleService.class})
public class ApplicationConfig {
}

```

测试配置

```java
public class Service2Test {
    public static void main(String[] args) {

        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);

        Role role = context.getBean(Role.class);
        RoleService roleService = context.getBean(RoleService.class);
        roleService.printRoleInfo(role);
        context.close();
    }
}
```

#### 自动装配——`@Autowired`







### Spring模块

**Spring可以分为6大模块**：

+ **Spring Core**： Spring的核心功能： IOC容器, 解决对象创建及依赖关系
+ **Spring Web**： Spring对web模块的支持。
  + 可以与struts整合,让struts的action创建交给spring
  + spring mvc模式
+ **Spring DAO**： Spring 对jdbc操作的支持 【JdbcTemplate模板工具类】
+ **Spring ORM**： Spring对orm的支持：
  + 既可以与hibernate整合，【session】
  + 也可以使用spring的对hibernate操作的封装
+ **Spring AOP**： 切面编程
+ **SpringEE**： Spring 对JavaEE其他模块的支持

![Sping](http://img.whl123456.top/image/Spring框架.png)

### Spring Core模块

核心容器

## HelloSpirng

> 对应源码实例为：HelloSpring

## applicationContext.xml的配置

### 别名

```xml
<alias name="原名" alias="别名"/>
```

原名和别名都可以被Spring创建，获取到原名对应的对象

### Bean的配置

```xml
<bean id="user" class="top.whl.pojo.User" name="user3,u3">
    <property name="name" value="whl_property"/>
</bean>
```

`id`：bean的唯一标识符，是java类中的对象名

`class`：bean对象对应的全限定名：包名+类名

`name`：别名，可以同时取多个别名（用逗号、空格或分号分开），比`alias`更高级

### import

用于团队开发，将多个配置文件导入合并

```xml
<import resource="beans2.xml"/>
```

> 对应源码实例为：SpringIoC02

## 依赖注入（DI）

### 构造器注入

```xml
<bean id="user" class="top.whl.pojo.User" name="user3,u3 u4">
	<!--index方式-->
	<constructor-arg index="0" value="whl_constructor-arg_index"/>
</bean>
```

```xml
<bean id="user" class="top.whl.pojo.User" name="user3,u3 u4">
	<!--type方式-->
	<constructor-arg type="java.lang.String" value="whl_constructor-arg_type"/>
</bean>
```

```xml
<bean id="user" class="top.whl.pojo.User" name="user3,u3 u4">
	<constructor-arg name="name" value="whl_constructor-arg_name"/>
</bean>
```

> 对应源码实例为：SpringIoC02

### Set方式注入

需要类中有Set方法

+ 依赖：bean对象的创建依赖于容器
+ 注入：bean对象中的所有属性，由容器来注入

测试对象

```java
public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbies;
    private Map<String,String> card;
    private Set<String> games;
    private String oh;
    private Properties info;
    //省略getter、setter和toString方法代码
}
```

测试类

```java
public class Text01 {
    public static void main(String[] args) {
        //CPX
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Student student = (Student) context.getBean("student");
        System.out.println("student.toString() = " + student.toString());

    }
}
```

注入方式

```xml
<bean id="address" class="top.whl.pojo.Address">
    <property name="address" value="浙江"/>
</bean>

<bean id="student" class="top.whl.pojo.Student">
    <!--01 普通值注入直接使用value-->
    <property name="name" value="whl"/>

    <!--02 Bean注入使用ref-->
    <property name="address" ref="address"/>

    <!--03 数组注入-->
    <property name="books">
        <array>
            <value>红楼梦</value>
            <value>西游记</value>
            <value>水浒传</value>
            <value>三国演义</value>
        </array>
    </property>

    <!--04 List注入-->
    <property name="hobbies">
        <list>
            <value>篮球</value>
            <value>足球</value>
            <value>乒乓球</value>
            <value>羽毛球</value>
        </list>
    </property>

    <!--05 Map注入-->
    <property name="card">
        <map>
            <entry key="学号" value="000000"/>
            <entry key="身份证号" value="000000"/>
            <entry key="银行卡号" value="000000"/>
        </map>
    </property>

    <!--06 Set注入-->
    <property name="games">
        <set>
            <value>LOL</value>
            <value>CS</value>
        </set>
    </property>

    <!--07 null注入-->
    <property name="oh">
        <null/>
    </property>

    <!--08 Properties-->
    <property name="info">
        <props >
            <prop key="姓名">whl</prop>
            <prop key="年龄">18</prop>
            <prop key="性别">男</prop>
            <prop key="driver">000</prop>
            <prop key="url">000</prop>
            <prop key="username">000</prop>
            <prop key="password">000</prop>
        </props>
    </property>
</bean>

```

> 对应源码实例：SpringDI

### 拓展方式注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入直接注入属性的值 property-->
    <bean id="user" class="top.whl.pojo.User" p:name="whl" p:age="18"/>

    <!--c命名空间注入 constructor-->
    <bean id="user2" class="top.whl.pojo.User" c:age="19" c:name="whl02"/>

</beans>

```

测试类

```java
@Test
public void test02(){

    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("user.xml");
    User user = context.getBean("user", User.class);
    System.out.println("user = " + user);
}

@Test
public void test03(){

    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("user.xml");
    User user2 = context.getBean("user2", User.class);
    System.out.println("user2 = " + user2);
}
```

> 测试对象User类中需要设置无参构造和有参构造才能使用c命名空间

## Bean作用域

| Scope                                                        | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [singleton](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container. |
| [prototype](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

### singleton单例模式

无论使用多少个Bean只有一个实例被创建，默认实现

测试类

```java
@Test
public void test04(){
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("user.xml");
    User user2 = context.getBean("singleton", User.class);
    User user3 = context.getBean("singleton", User.class);
    System.out.println(user3==user2);//true
}
```

显式创建

```xml
<bean id="singleton" class="top.whl.pojo.User" scope="singleton"/>
```

### prototype原型模式

每次从容器（context）中getBean都会产生新对象

创建

```xml
<bean id="prototype" class="top.whl.pojo.User" scope="prototype"/>
```

测试类

```java
@Test
public void test05(){
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("user.xml");
    User user2 = context.getBean("prototype", User.class);
    User user3 = context.getBean("prototype", User.class);
    System.out.println(user3==user2);//false
}
```

> 对应源码实例：SpringDI

### 其余

其余在Web开发中使用

## Bean的自动装配

3种装配方式：

+ 在xml中显式配置
+ 在java中显式配置
+ 隐式的自动装配

### ByName

自动在容器上下文中查找和对象set后对应的bean的id，id唯一且与set方法对应

```xml
<bean id="person2" class="top.whl.pojo.Person" autowire="byName"/>
```

### ByType

自动在容器上下文中查找和对象属性相同的bean，class唯一，可以省略bean的id

```xml
<bean id="person3" class="top.whl.pojo.Person" autowire="byType"/>
```

### 注解

xml中导入context约束和开启注解支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

类中属性和Setter方法中开启注解，在类中可以不用写Set方法

```java
public class Person {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
    //省略getter、setter和toString方法代码
}
```

`@Autowired(required = true)`默认为true，如果设置`@Autowired(required = false)`，什么对象可以为null

但Bean中一个类配置多个id时使用`@Qualifier(value = "对应id")`配合`@Autowired`可以指定bean，类型需要对应

```java
@Autowired
@Qualifier(value = "dog2")
private Dog dog;
```

> 对应源码实例：Autowire

## 使用注解开发

`@Autowired`：自动装配，放在属性前面

`@Nullable`：表示字段可以为空，放在形参前面

`@Resource`：Java中的注解，用于自动装配，Java11移除

`@Component`：被扫描类，放在类前，配合Bean中`<context:component-scan base-package="包名"/>`使用，等价于`<bean id="" class=""/>`

`@Value("值")`：等价于`<property name="" value="">`，复杂值推荐走配置文件，放在属性或对应Set方法前

### 前提配置bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--扫描指定目录下的包，使注解生效-->
    <context:component-scan base-package="top.whl"/>
    <context:annotation-config/>

</beans>
```

### 属性注入

```java
public class User {

//    @Value("WHL")
    public String name;

    @Value("WHL1")
    public void setName(String name) {
        this.name = name;
    }
}
```

### 衍生的注解

`@Component`有几个衍生注解，在Web开发中，按照MVC结构分层

+ DAO：使用`@Repository`
+ Service：使用`@Service`
+ Controller：使用`@Controller`

以上注解功能同`@Component`

### 自动装配

`@Autowired`：自动装配，放在属性前面

`@Nullable`：表示字段可以为空，放在形参前面

`@Resource`：Java中的注解，用于自动装配，Java11移除

详见Bean的自动装配

### 作用域

`@Scope("XXX")`详见Bean作用域

> 对应源码实例：Annotation

### 小结

+ 使用xml用来管理Bean
+ 使用注解来完成属性注入

## 使用Java的方式配置Bean

JavaConfig

```java
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("WHL")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```java
@Configuration
@ComponentScan("top.whl")
public class Config01 {
    @Bean
    public User getUser(){
        return new User();
    }
}
```

## 设计模式

### 静态代理

代理的好处

+ 可以使真实角色的操作更加纯粹!不用去关注一些公共的业务

+ 公共也就就交给代理角色!实现了业务的分工!

+ 公共业务发生扩展的时候，方便集中管理!

缺点

+ 一个真实角色就会产生1个代理角色
+ 代码量会翻倍开发效率会变低

接口

```java
public interface Rent {
    public void rent();
}
```

真实角色

```java
public class Host implements Rent{

    public void rent() {
        System.out.println("房东出租房子");
    }
}
```

代理

```java
public class Proxy implements Rent {
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    public void rent() {
        host.rent();
        fee();
        sign();
    }

    public void fee(){
        System.out.println("收小费");
    }

    public void sign(){
        System.out.println("签合同");
    }
}
```

客户端访问代理角色

```java
public class Client {
    public static void main(String[] args) {
        Host host = new Host();
        Proxy proxy = new Proxy(host);
        proxy.rent();
    }
}
```

在不改变真实角色的代码的前提下增加功能

> 对应实例源码：proxy

### 动态代理

在静态代理的优点基础上还有

+ 一个动态代理可以代理多个类，只要改变接口

实现方式

+ 基于接口的动态代理（JDK反射）

+ 基于类的动态代理（CGLib）

+ Java字节码实现（Javasist）

使用基于接口的动态代理

需要了解的两个类：`Proxy`（代理），`InvocationHandler`（调用处理）

```java
public class ProxyInvocationHandler implements InvocationHandler {

    //被代理的接口
    private Rent rent;

    public void setRent(Rent rent) {
        this.rent = rent;
    }

    //生成得到代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), rent.getClass().getInterfaces(), this);
    }

    //处理代理实例，并返回结果
    public Object invoke(Object o, Method method, Object[] args) throws Throwable {
        //反射实现
        Object result = method.invoke(rent, args);
        fee();
        sign();
        return result;
    }

    public void fee() {
        System.out.println("收小费");
    }

    public void sign() {
        System.out.println("签合同");
    }

}
```

```java
public class Client {
    public static void main(String[] args) {
        //真实角色
        Host host = new Host();
        //代理角色（不存在）
        ProxyInvocationHandler proxyInvocationHandler = new ProxyInvocationHandler();
        //通过调用程序处理角色
        proxyInvocationHandler.setRent(host);
        Rent proxy = (Rent) proxyInvocationHandler.getProxy();// 动态生成
        proxy.rent();
    }
}
```

## 面向切面编程（AOP）

AOP (Aspect Oriented Programming)意为：面向切面编程，通过**预编译**方式和运行期**动态代理**实现程序功能的统一维护的一种技术。

+ Aspect（切面）：将关注点进行模块化。某些关注点可能会横跨多个对象，如事务管理，它是Java企业级应用中一个关于横切关注点很好的例子。在 Spring AOP中，切面可以使用常规类（基于模式的方法）或使用`@Aspect`注解的常规类来实现切面。
+ Join Point（连接点）：在程序执行过程中某个特定的点，如某方法调用时或处理异常时。在 Spring AOP中，一个连接点总是代表一个方法的执行。
+ Advice（通知）：在切面的某个特定的连接点上执行的动作。通知有各种类型，其中包括"around"、"before"和"after"等通知。许多AOP框架，包括 Spring，都是以拦截器来实现通知模型的，并维护一个以连接点为中心的拦截器链。
+ Pointcut（切入点）：匹配连接点的断言。通知和一个切入点表达式关联，并在满足这个切入点的连接点上运行（如当执行某个特定名称的方法时）。切入点表达式如何和连接点匹配是AOP的核心。 Spring默认使用 AspectJ切入点语法。
+ Introduction（引入）：声明额外的方法或某个类型的字段。 Spring允许引入新的接口（及个对应的实现）到任何被通知的对象。例如，可以使用一个引入来使bean实现 IsModified 接口以便简化缓存机制。在 AspectJ社区， Introduction也被称为 Inter-type Declaration（内部类型声明）。
+ Target Object（目标对象）：被一个或多个切面所通知的对象。也有人把它称为 Advised（被通知）对象。既然 Spring AOP是通过运行时代理实现的，那这个对象永远是一个 Proxied（被代理）对象。
+ AOP Proxy（AOP代理）：AOP框架创建的对象，用来实现 Aspect Contract（切面契约）包括通知方法执行等功能。在 Spring中，AOP代理可以是JDK动态代理或CGLB代理。
+ Weaving（织入）：把切面连接到其他的应用程序类型或对象上，并创建一个 Advised（被通知）的对象。这些可以在编译时（如使用 AspectJ编译器）、类加载时完成。

Spring与其他纯Java AOP框架一样，在运行时完成织入。其中有关Advice（通知）的类型主要有以下几种：

+ Before Advice（前置通知）：在某连接点之前执行的通知，但这个通知不能阻止连接点前的执行（除非它抛出一个异常）。
+ After Returning Advice（返回后通知）：在某连接点正常完成后执行的通知，如果一个方法没有抛出任何异常，将正常返回。
+ After Throwing Advice（抛出异常后通知）：在方法抛出异常退出时执行的通知
+ After（ finally） Advice（最后通知）：当某连接点退出时执行的通知（不论是正常返回还是异常退出）
+ Around Advice（环绕通知）：包围一个连接点的通知，如方法调用。这是最强大的一种通知类型。环绕通知可以在方法调用前后完成自定义的行为，它也会选择是否继续执行连接点，或者直接返回它们自己的返回值或抛出异常来结束执行。 Around Advice是最常用的一种通知类型。与AspectJ一样， Spring提供所有类型的通知，推荐使用尽量简单的通知类型来实现需要的功能。例如，如果只是需要用一个方法的返回值来更新缓存，虽然使用环绕通知也能完成同样的事情，但最好是使用 After Returnin通知，而不是使用环绕通知。用最合适的通知类型可以使编程模型变得简单，并且能够避免很多潜在的错误。例如，如果不调用 Join point（用于 Around Advice）的 proceed()方法，就不会有调用的问题。

AOP是OOP的延续,是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

### AOP在Spring中的作用

提供声明式事务；允许用户自定义切面

+ 横切关注点：跨越应用程序多个模块的方法或功能。即使与业务逻辑无关，但需要关注的部分就是横切关注点。如日志、安全、缓存、事务等

+ 切面（Aspect）：横切关注点被模块化的特殊对象。即，一个类。
+ 通知（Advice）：切面必须要完成的工作。即，类中的一个方法。
+ 目标（Target）：被通知的对象。
+ 代理（Proxy）：向目标对象应用通知之后创建的对象。
+ 切入点（PointCut）：切面通知执行的“地点”的定义
+ 连接点（JointPoint）：与切入点匹配的执行点。

![AOP](E:\OneDrive - lanqilu\我的图片库\blogimg\Spring框架\AOP.svg)



| 通知类型     | 连接点               | 实现接口                                     |
| ------------ | -------------------- | -------------------------------------------- |
| 前置通知     | 方法前               | org.springframework.aop.MethodBeforeAdvice   |
| 后置通知     | 方法后               | org.springframework.aop.AfterReturningAdvice |
| 环绕通知     | 方法前后             | org.aopalliance.intercept.MethodInterceptor  |
| 异常抛出通知 | 方法抛出异常         | org.springframework.aop.ThrowsAdvice         |
| 引介通知     | 类中增加新的方法属性 | org.springframework.aop.IntroductionAdvisor  |

总结：AOP在不改变原有代码的情况下，去增加新的功能。

## 使用Spring实现AOP

### 导入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
    <scope>runtime</scope>
</dependency>
```

### 创建Service业务类

创建UserService接口

```
package com.halo.service;

public interface UserService {
    public void add();

    public void delete();

    public void update();

    public void select();
}

```

实现UserService接口

```java
package com.halo.service;

public class UserServiceImpl implements UserService {

    @Override
    public void add() {
        System.out.println("增加了用户");
    }

    @Override
    public void delete() {
        System.out.println("删除了用户");
    }

    @Override
    public void update() {
        System.out.println("更新了用户");
    }

    @Override
    public void select() {
        System.out.println("查询了用户");
    }
}
```

### 实现方式一：使用Spring的API接口

创建BeforeLog类

```java
package com.halo.log;

import org.springframework.aop.MethodBeforeAdvice;
import java.lang.reflect.Method;

public class BeforeLog implements MethodBeforeAdvice {
    @Override
    /**
     * @param method: 要执行的目标对象的方法
     * @param args: 参数
     * @param target:  目标对象
     * @return void
     */
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + " 的 " + method.getName() + " 被执行了");
    }
}

```

创建AfterLog类

```java
package com.halo.log;

import org.springframework.aop.AfterReturningAdvice;
import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {

    @Override
    /**
     * @param returnValue: 返回值
     */
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了 " + method.getName() + " 方法,返回的结果为: " + returnValue);
    }
}
```

创建并配置applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册Bean-->
    <bean id="userService" class="com.halo.service.UserServiceImpl"/>
    <bean id="beforeLog" class="com.halo.log.BeforeLog"/>
    <bean id="afterLog" class="com.halo.log.AfterLog"/>

    <!--使用原生SpringAPI接口-->
    <!--配置AOP,需要导入AOP的约束-->
    <aop:config>
        <!--切入点:开始执行的位置-->
        <!--execution(要执行的位置)-->
        <aop:pointcut id="pointcut" expression="execution(* com.halo.service.UserServiceImpl.*(..))"/>

        <!--执行环绕增加-->
        <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

进行测试

```java
import com.halo.service.UserService;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AOPTest {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        // 动态代理接口 不是实现类
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
        userService.delete();
        userService.select();
        userService.update();
    }
}
```



### 实现方式二：自定义来实现AOP

创建CustomPointCut类

```java
package com.halo.custom;

public class CustomPointCut {
    public void before() {
        System.out.println("======方法执行前======");
    }
    public void after() {
        System.out.println("======方法执行后======");
    }
}
```

配置applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册Bean-->
    <bean id="userService" class="com.halo.service.UserServiceImpl"/>
    <bean id="custom" class="com.halo.custom.CustomPointCut"/>

    <aop:config>
        <!--自定义切面 ref = 要引入的类-->
        <aop:aspect ref="custom">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.halo.service.UserServiceImpl.*(..)) "/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>
</beans>
```

测试类不变

### 实现方式三 注解

创建AnnotationAOP类

```java
package com.halo.Log;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class AnnotationAOP {
    @Before("execution(* com.halo.service.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("[Before]:方法执行前");
    }

    @After("execution(* com.halo.service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("[After] :方法执行后");
    }

    /**
     * 在环绕增强中我们可以给定一个参数，代表我们要获取处理切入的点
     */
    @Around("execution(* com.halo.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("[Around]:环绕前");
        // 执行方法
        Object proceed = joinPoint.proceed();
        System.out.println("[Around]:环绕后");

        // 获得签名
        Signature signature = joinPoint.getSignature();
        System.out.println("[signature]:" + signature);

    }

}
```

创建并配置applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="com.halo.service.UserServiceImpl"/>
    <bean id="annotationAOP" class="com.halo.Log.AnnotationAOP"/>

    <!--开启注解支持-->
    <!--proxy-target-class="false" 默认JDK,true是cglib-->
    <aop:aspectj-autoproxy proxy-target-class="false"/>

</beans>
```

测试Application同一二方法



