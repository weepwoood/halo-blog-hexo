---
title: Java类型系统
tags:
  - Java
categories:
  - [Java]
date: 2020-08-08 21:13:56
---


本文以基于类的面向对象编程为基础，介绍高效使用 Java 静态类型系统所需知道的其他概念。

---

静态类型语言的变量类型是确定的，如果把不兼容类型的值赋给变量，会导致编译时错误。Java 是一种静态类型语言。只在运行时检查类型兼容性的语言叫作动态类型语言，JavaScript 便是一种动态类型语言。

Java 的类型系统不仅涉及类和基本类型，还涉及与类的基本概念相关的其他引用类型，但这些引用类型有些不同，javac 或 JVM 往往会使用特殊的方式处理。

我们已经介绍了数组和类，它们是使用最广泛的两种 Java 引用类型。本章先介绍另一种重要的引用类型——接口。然后介绍 Java 的泛型，泛型在 Java 的类型系统中扮演着重要角色。掌握这些知识后，我们再介绍 Java 中编译时和运行时类型之间的区别。

为了完整介绍 Java 的引用类型，我们要介绍两种特殊的类和接口——枚举和注解。本章最后介绍嵌套类型和 Java 8 引入的 lambda 表达式。

## 接口

一个 Java 类只能继承一个类。这对我们要编写的面向对象程序来说是个相当严格的限制。Java 的设计者知道这一点，但他们也是为了确保Java 实现面向对象编程的方式比其他语言（例如 C++）简单。他们选择的方式是提出接口这个概念。和类一样，接口定义一种新的引用类型。如“接口”这个名称所示，接口的作用只是描绘 API，因此，接口提供类型的描述信息，以及实现这个 API 的类应该提供的方法（和签名）。

一般来说，Java 的接口不为它描述的方法提供实现代码。这些方法是强制要实现的——想实现接口的类必须实现这些方法。

不过，接口可能想把 API 中的某些方法标记为可选，如果实现接口的类不想实现就不用实现。这种机制通过 `default` 关键字实现，接口必须为可选的方法提供默认实现，未实现这些方法的类会使用默认实现。

> 接口中的可选方法是 Java 8 的新功能，之前的版本中没有。

接口不能直接实例化，也不能创建这种接口类型的成员。接口必须通过类实现，而且类要提供所需的方法主体。这个类的实例既属于这个类定义的类型，也属于这个接口定义的类型。不属于同一个类或超类的对象，通过实现同一个接口，也能属于同一种类型。

### 定义接口

定义接口的方式和定义类差不多，不过所有（非默认的）方法都是抽象方法，而且关键字`class` 要换成 `interface`。例如，下述代码定义了一个名为 `Centered` 的接口：

```Java、
interface Centered { 
  void setCenter(double x, double y); 
  double getCenterX(); 
  double getCenterY(); 
}
```

接口的成员有些限制：

+ 接口中所有强制方法都隐式使用 `abstract` 声明，不能有方法主体，要使用分号。可以使用 `abstract` 修饰符，但一般习惯省略。
+ 接口定义公开的 API。接口中的所有成员都隐式使用 `public` 声明，而且习惯省略不必要的 `public` 修饰符。如果在接口中使用 `protected` 或 `private` 定义方法，会导致编译时错误。
+ 接口不能定义任何实例字段。字段是实现细节，而接口是规格不是实现。在接口中只能定义同时使用 `static` 和 `final` 声明的常量。
+ 接口不能实例化，因此不定义构造方法。
+ 接口中可以包含嵌套类型。嵌套类型隐式使用 `public` 和 `static` 声明。
+ 从 Java 8 开始，接口中可以包含静态方法。

### 扩展接口

接口可以扩展其他接口，而且和类的定义一样，接口的定义可以包含一个 `extends` 子句。接口扩展另一个接口时，会继承父接口中的所有方法和常量，而且可以定义新方法和常量。不过，和类不同的是，接口的 `extends` 子句可以包含**多个**父接口。例如，下述接口扩展了其他接口：

```java
interface Positionable extends Centered { 
  void setUpperRightCorner(double x, double y); 
  double getUpperRightX(); 
  double getUpperRightY(); 
} 
interface Transformable extends Scalable, Translatable, Rotatable {} 
interface SuperShape extends Positionable, Transformable {}
```

扩展多个接口的接口，会继承每个父接口中的所有方法和常量，而且可以定义属于自己的方法和常量。实现这个接口的类必须实现这个接口直接定义的抽象方法，以及从所有父接口中继承的全部抽象方法。

### 实现接口

类使用 `extends` 指定超类，类似地，类使用 `implements` 列出它支持的一个或多个接口。`implements` 是一个 Java 关键字，可以出现在类声明中，但要放在 `extends` 子句后面。`implements` 关键字后面是这个类要实现的一组接口，接口之间使用逗号分隔。

类在 `implements` 子句中声明接口时，表明这个类要为接口中的每个强制方法提供实现（即主体）。如果实现接口的类没有为接口中的每个强制方法提供实现，那么这个类从接口中继承未实现的抽象方法，而且这个类本身必须使用 `abstract` 声明。如果类实现多个接口，必须实现每个接口中的所有强制方法（否则这个类要使用 `abstract` 声明）。

下述代码展示了如何定义 `CenteredRectangle` 类，这个类扩展第定义的 `Rectangle` 类，而且实现 `Centered` 接口：

```java
public class CenteredRectangle extends Rectangle implements Centered { 
  // 新实例字段 
  private double cx, cy; 
 
  // 构造方法 
  public CenteredRectangle(double cx, double cy, double w, double h) { 
    super(w, h); 
    this.cx = cx; 
    this.cy = cy; 
  } 
 
  // 继承了Rectangle类中的所有方法 
  // 但要为Centered接口中的所有方法提供实现 
  public void setCenter(double x, double y) { cx = x; cy = y; } 
  public double getCenterX() { return cx; } 
  public double getCenterY() { return cy; } 
}
```

假设我们按照 `CenteredRectangle` 类的实现方式实现了 `CenteredCircle` 和 `CenteredSquare`类。每个类都扩展 `Shape` 类，所以如前所示，这些类的实例都可以当成 `Shape` 类的实例。因为每个类都实现了 `Centered` 接口，所以这些实例还可以当成 `Centered` 类型的实例。下述代码演示了对象既可以作为类类型的成员，也可以作为接口类型的成员：

```java
Shape[] shapes = new Shape[3];     // 创建一个数组，保存形状对象 
 
// 创建一些Centered类型的形状，存储在这个Shape[]类型的数组中 
// 不用校正，因为都是放大转换 
shapes[0] = new CenteredCircle(1.0, 1.0, 1.0); 
shapes[1] = new CenteredSquare(2.5, 2, 3); 
shapes[2] = new CenteredRectangle(2.3, 4.5, 3, 4); 
 
// 计算这些形状的平均面积 
// 以及到原点的平均距离 
double totalArea = 0; 
double totalDistance = 0; 
for(int i = 0; i < shapes.length; i++) { 
  totalArea += shapes[i].area();   // 计算这些形状的面积 
 
  // 注意，一般来说，使用instanceof判断对象的运行时类型经常表明设计有问题 
  if (shapes[i] instanceof Centered) { // 形状属于Centered类型 
    // 注意，把Shape类型转换成Centered类型要校正 
    // （不过，把CenteredSquare类型转换成Centered类型不用校正） 
    Centered c = (Centered) shapes[i]; 
 
    double cx = c.getCenterX();    // 获取中心点的坐标 
    double cy = c.getCenterY();    // 计算到原点的距离 
    totalDistance += Math.sqrt(cx*cx + cy*cy);
    } 
} 
System.out.println("Average area: " + totalArea/shapes.length); 
System.out.println("Average distance: " + totalDistance/shapes.length);
```

> 在 Java 中，接口和类一样，也是数据类型。如果一个类实现了一个接口，那么这个类的实例可以赋值给这个接口类型的变量。

看过这个示例之后，别错误地认为必须先把 `CenteredRectangle` 对象赋值给 `Centered` 类型的变量才能调用 `setCenter()` 方法，或者要先赋值给 `Shape` 类型的变量才能调用 `area()`方法。`CenteredRectangle` 类定义了 `setCenter()` 方法，而且从超类 `Rectangle` 中继承了`area()` 方法，所以始终可以调用这两个方法。

### 实现多个接口

假设我们不仅想通过中心点摆放形状对象，也想通过右上角摆放形状对象，而且还想放大和缩小形状。还记得吗？虽然一个类只能扩展一个超类，但可以实现任意多个接口。假设我们已经定义好了合适的 `UpperRightCornered` 和 `Scalable` 接口，那么可以按照下述方式声明类：

```java
public class SuperDuperSquare extends Shape 
  implements Centered, UpperRightCornered, Scalable { 
  // 类的成员省略了 
}
```

一个类实现多个接口只是表明这个类要实现所有接口中的全部抽象方法（即强制方法）。

### 默认方法

Java 8 出现后，接口中的方法可以包含实现了。本节介绍这种方法——在接口描述的 API中通过可选的方法表示，一般叫作默认方法。首先说明为什么需要这种默认机制。

#### 向后兼容性

Java 平台始终关注向后兼容性。这意味着，为前一版平台编写（或者已经编译）的代码在最新版平台中必须能继续使用。这个原则让开发团队坚信，升级 JDK 或 JRE 后不会破坏之前能正常运行的应用。

向后兼容性是 Java 平台的一大优势，但是为此，Java 平台有诸多约束。其中一个约束是，新发布的接口不能添加新的强制方法。

例如，假设我们要升级 `Positionable` 接口，添加获取和设定左下角顶点的功能：

```java
public interface Positionable extends Centered { 
  void setUpperRightCorner(double x, double y); 
  double getUpperRightX(); 
  double getUpperRightY(); 
  void setLowerLeftCorner(double x, double y); 
  double getLowerLeftX(); 
  double getLowerLeftY(); 
}
```

重新定义接口之后，如果尝试在为旧接口编写的代码中使用这个新接口，不会成功，因为现有的代码中没有 `setLowerLeftCorner()`、`getLowerLeftX()` 和 `getLowerLeftY()` 这三个强制方法。

> 在你的代码中可以轻易地看到效果。编译一个依赖接口的类文件，在接口中添加一个新的强制方法，然后使用新版接口和旧的类文件尝试运行程序。你会看到程序崩溃，抛出 `NoClassDefError` 异常。

Java 8 的设计者注意到了这个缺陷，因为设计者的目标之一是升级 Java 核心中的集合库，引入使用 lambda 表达式的方法。

若想解决这个问题，需要一种新机制。这种机制必须要允许向接口中添加可选的新方法，而不破坏向后兼容性。

#### 实现默认方法

在接口中添加新方法而不破坏向后兼容性，这需要为接口的旧实现提供一些新实现，以便接口能继续使用。这个机制是默认方法，在 JDK 8 中首次添加到 Java 平台。

> 默认方法（有时也叫可选方法）可以添加到任何接口中。默认方法必须包含实现，即默认实现，写在接口定义中。

默认方法的基本行为如下：

+ 实现接口的类可以（但不是必须）实现默认方法；
+ 如果实现接口的类实现了默认方法，那么使用这个类中的实现；
+ 如果找不到其他实现，就使用默认实现。

`sort()` 方法是默认方法的一例，JDK 8 把它添加到 `java.util.List` 接口中，定义如下：

```java
interface List<E> { 
  // 省略了其他成员 
 
  public default void sort(Comparator<? super E> c) { 
    Collections.<E>sort(this, c); 
  } 
}
```

因此，从 Java 8 开始，实现 List 接口的对象都有一个名为 `sort()` 的实例方法，使用合适的 `Comparator` 排序列表。因为返回类型是 `void`，所以我们猜测这是就地排序，而事实确实如此。

### 标记接口

有时，定义全空的接口很有用。类实现这种接口时只需在 `implements` 子句中列出这个接口，而不用实现任何方法。此时，这个类的任何实例都是这个接口的有效实例。Java 代码可以使用 `instanceof` 运算符检查实例是否属于这个接口，因此这种技术是为对象提供额外信息的有力方式。

`java.io.Serializable` 接口就是一种标记接口。实现 `Serializable` 接口的类告诉`ObjectOutputStream` 类，这个类的实例可以安全地序列化。`java.util.RandomAccess` 也是标记接口：`java.util.List` 接口实现了这个接口，表明这个接口能快速随机访问列表中的元素。例如，`ArrayList` 类实现了 `RandomAccess` 接口，而 `LinkedList` 类没实现。注重随机访问操作性能的算法可以使用下述方式测试 `RandomAccess`：

```java
// 排序任意长度的列表元素之前，我们或许想确认列表是否支持快速随机访问 
// 如果不支持，先创建一个支持随机访问的副本再排序，速度可能更快 
// 注意，使用java.util.Collections.sort()时不必这么做 
List l = ...; // 随意一个列表 
if (l.size() > 2 && !(l instanceof RandomAccess)) l = new ArrayList(l);  
sortListInPlace(l);
```

后面会看到，Java 的类型系统和类型的名称联系紧密，这种方式叫作名义类型（nominal typing）。标记接口是个很好的例子，因为它除了名称什么都没有。

## Java泛型

Java 平台的一大优势是它提供的标准库。标准库提供了大量有用的功能，特别是实现了健壮的通用数据结构。这些实现使用起来相当简单，而且文档编写良好。虽然这些库一直很有用，但在早期版本中有相当大的不足——数据结构（经常叫作容器）完全隐藏了存储其中的数据类型。

> 数据隐藏和封装是面向对象编程的重要原则，但在这种情况下，容器的不透明会为开发者带来很多问题。

### 介绍泛型

如果想构建一个由 Shape 实例组成的集合，可以把这个集合保存在一个 List 对象中，如下所示：

```java
List shapes = new ArrayList(); // 创建一个List对象，保存形状 
 
// 指定中心点，创建一些形状，保存在这个列表中 
shapes.add(new CenteredCircle(1.0, 1.0, 1.0)); 
// 这是合法的Java代码，但不是好的设计方式 
shapes.add(new CenteredSquare(2.5, 2, 3)); 
 
// List.get()返回Object对象，所以要想得到CenteredCircle对象，必须校正 
CenteredCircle c = (CentredCircle)shapes.get(0); 
 
// 下面这行代码会导致运行时失败 
CenteredCircle c = (CentredCircle)shapes.get(1);
```

上述代码有个问题，为了取回有用的形状对象形式，必须校正，因为 List 不知道其中的对象是什么类型。不仅如此，其实可以把不同类型的对象放在同一个容器中，一切都能正常运行，但是如果做了不合法的校正，程序就会崩溃。

我们真正需要的是一种知道所含元素类型的 List。这样，如果把不合法的参数传给 List 的方法，javac 就能检测到，导致编译出错，而不用等到运行时才发现问题。

为了解决这个问题，Java 提供了一种句法，指明某种类型是一个容器，这个容器中保存着其他引用类型的实例。容器中保存的负载类型（payload type）在尖括号中指定：

```java
// 创建一个由CenteredCircle对象组成的List 
List<CenteredCircle> shapes = new ArrayList<CenteredCircle>(); 
 
// 指定中心点，创建一些形状，保存在这个列表中 
shapes.add(new CenteredCircle(1.0, 1.0, 1.0)); 
 // 下面这行代码会导致编译出错 
shapes.add(new CenteredSquare(2.5, 2, 3));

// List<CenteredCircle>.get()返回一个CenteredCircle对象，无需校正 
CenteredCircle c = shapes.get(0);
```

这种句法能让编译器捕获大量不安全的代码，根本不能靠近运行时。当然，这正是静态类型系统的关键所在——使用编译时信息协助排除大量运行时问题。

容器类型一般叫作泛型（generic type），使用下述方式声明：

```java
interface Box<T> { 
  void box(T t); 
  T unbox(); 
}
```

上述代码表明，`Box` 接口是通用结构，可以保存任意类型的负载。这不是一个完整的接口，更像是一系列接口的通用描述，每个接口对应的类型都能用在 `T` 的位置上。

### 泛型和类型参数

我们已经知道如何使用泛型增强程序的安全性——使用编译时信息避免简单的类型错误。本节深入介绍泛型的特性。

`<T>` 句法有个专门的名称——类型参数（type parameter）。因此，泛型还有一个名称——参数化类型（parameterized type）。这表明，容器类型（例如 `List`）由其他类型（负载类型）参数化。把类型写为 `Map<String, Integer>` 时，我们就为类型参数指定了具体的值。

定义有参数的类型时，要使用一种不对类型参数做任何假设的方式指定具体的值。所以`List` 类型使用通用的方式 `List<E>` 声明，而且自始至终都使用类型参数 `E` 作占位符，代表程序员使用 `List` 数据结构时负载的真实类型。

类型参数可以在方法的签名和主体中使用，就像是真正的类型一样，例如：

```java
interface List<E> extends Collection<E> { 
  boolean add(E e); 
  E get(int index); 
  // 其他方法省略了 
}
```

注意，类型参数 E 既可以作为返回类型的参数，也可以作为方法参数类型的参数。我们不假设负载类型有任何具体的特性，只对一致性做了基本假设，即存入的类型和后来取回的类型一致。

### 菱形句法

创建泛型的实例时，赋值语句的右侧会重复类型参数的值。一般情况下，这个信息是不必要的，因为编译器能推导出类型参数的值。在 Java 的现代版本中，可以使用菱形句法省略重复的类型值。

下面通过一个示例说明如何使用菱形句法，这个例子改自之前的示例：

```java
// 使用菱形句法创建一个由CenteredCircle对象组成的List 
List<CenteredCircle> shapes = new ArrayList<>();
```

对这种冗长的赋值语句来说，这是个小改进，能少输入几个字符。本章末尾介绍 lambda 表达式时会再次讨论类型推导。

### 类型擦除

Java 平台十分看重向后兼容性。Java 5 添加的泛型又是一个会导致向后兼容性问题的新语言特性。

问题的关键是，如何让类型系统既能使用旧的非泛型集合类又能使用新的泛型集合类。设计者选择的解决方式是使用校正：

```java
List someThings = getSomeThings(); 
// 这种校正不安全，但我们知道someThings的内容确实是字符串 
List<String> myStrings = (List<String>)someThings;
```

上述代码表明，作为类型，`List` 和 `List<String>` 是兼容的，至少在某种程度上是兼容的。Java 通过类型擦除实现这种兼容性。这表明，泛型的类型参数只在编译时可见——javac会去掉类型参数，而且在字节码中不体现出来。

非泛型的 List 一般叫作原始类型（raw type）。就算现在有泛型了，Java 也完全能处理类型的原始形式。不过，这么做几乎就表明代码的质量不高。

类型擦除机制扩大了 javac 和 JVM 使用的类型系统之间的区别。

类型擦除还能禁止使用某些其他定义方式，如果没有这个机制，代码看起来是合法的。在下述代码中，我们想使用两个稍微不同的数据结构计算订单数量：

```java
// 不会编译 
interface OrderCounter { 
  // 把名称映射到由订单号组成的列表上 
  int totalOrders(Map<String, List<String>> orders); 
 
  // 把名称映射到目前已下订单的总数上 
  int totalOrders(Map<String, Integer> orders); 
}
```



## 枚举和注解

Java 有两种特殊形式的类和接口，在类型系统中扮演着特定的角色。这两种类型是枚举类型（enumerated type）和注解类型（annotation type），一般直接称为枚举和注解。

### 枚举

枚举是类的变种，功能有限，而且允许使用的值很少。

> 类的对象只有有限个，确定的。如果枚举只有一个对象，则可以作为一种单例模式的实现方式。

例如，假设我们想定义一个类型，表示三原色红绿蓝，而且希望这个类型只有这三个可以使用的值。我们可以使用 `enum` 关键字定义这个类型：

```java
public enum PrimaryColor { 
  // 实例列表末尾的分号是可选的 
  RED, GREEN, BLUE 
}
```

PrimaryColor 类型的实例可以按照静态字段的方式引用：`PrimaryColor.RED`、`PrimaryColor.GREEN` 和 `PrimaryColor.BLUE`。

例如，假设我们要定义一个枚举，包含前几个正多边形（等边等角的形状），而且想为这些形状指定一些属性（在方法中指定）。我们可以使用接收一个参数的枚举实现这个需求，如下所示：

```java
public enum RegularPolygon { 
  // 有参数的枚举必须使用分号 
  TRIANGLE(3), SQUARE(4), PENTAGON(5), HEXAGON(6); 
 
  private Shape shape; 
 
  public Shape getShape() { 
    return shape; 
  } 
 
  private RegularPolygon(int sides) { 
    switch (sides) { 
      case 3: 
        // 假设这些形状的构造方法接收的参数是边长和角度 
        shape = new Triangle(1,1,1,60,60,60); 
        break; 
      case 4: 
        shape = new Rectangle(1,1); 
        break; 
      case 5: 
        shape = new Pentagon(1,1,1,1,1,108,108,108,108,108); 
        break; 
      case 6: 
        shape = new Hexagon(1,1,1,1,1,1,120,120,120,120,120,120); 
        break; 
    } 
  } 
}
```

参数（在这个例子中只有一个参数）传入构造方法，创建单个枚举实例。因为枚举实例由Java 运行时创建，而且在外部不能实例化，所以把构造方法声明为私有方法。

枚举有些特殊的特性：

+ 都（隐式）扩展 java.lang.Enum 类；
+ 不能泛型化；
+ 可以实现接口；
+ 不能被扩展；
+ 如果枚举中的所有值都有实现主体，那么只能定义为抽象方法；
+ 只能有一个私有（或使用默认访问权限）的构造方法。

> 当需要定义一组常量时，强烈建议使用枚举类

### Enum类的主要方法

`values()`方法：返回枚举类型的对象数组。可以遍历所有的枚举值。

`valueOf(String objName)`：把一个字符串转换为对应的枚举对象



### 注解

注解是一种特殊的接口。如名称所示，其作用是注解 Java 程序的某个部分。

注解能为编译器和集成开发环境（Integrated Development Environment，IDE）提供有用的提示。如果开发者把方法的名称拼写错了，而这个方法本来是要覆盖超类的方法，那么，在这个名称拼错的方法上使用 @Override 注解，可以提醒编译器什么地方出错了。

注解不能改变程序的语义，只能提供可选的元信息。严格说来，这意味着注解不能影响程序的执行，只能为编译器和其他预执行阶段提供信息。

Java 平 台 在 java.lang 中 定 义 了 为 数 不 多 的 基 本 注 解。 一 开 始 只 支 持 `@Override`、 `@Deprecated` 和 `@SuppressWarnings`，这三个注解的作用分别是：注明方法是覆盖的，注明方法废弃了，以及静默编译器生成的警告。

后来，Java 7 增加了 `@SafeVarargs`（为变长参数方法提供增强的警告静默功能），Java 8 增加了 `@FunctionalInterface`。`@FunctionalInterface` 表示接口可以用作 lambda 表达式的目标。这是个很有用的标记注解，但不是必须使用的，后文会介绍。

和普通的接口相比，注解有些特殊的特性：

+ 都（隐式）扩展 `java.lang.annotation.Annotation` 接口；
+ 不能泛型化；
+ 不能扩展其他接口；
+ 只能定义没有参数的方法；
+ 不能定义会抛出异常的方法；
+ 方法的返回类型有限制；
+ 方法可以有一个默认返回值。



## 嵌套类型



## lambda表达式

