---
title: 创建型模式之简单工厂模式
date: 2021-04-18 18:04:00.612
updated: 2021-04-18 18:10:47.571
url: http://blog.halo123.top:8090/archives/创建型模式之简单工厂模式
categories: 设计模式
tags: 设计模式
---

## 简单工厂模式概述

简单工厂模式并不属于GoF 23个经典设计模式，但通常将它作为学习其他工厂模式的基础，它的设计思想很简单，其基本流程如下：

1. 首先将需要创建的各种不同对象（例如各种不同的Chart对象）的相关代码封装到不同的类中，这些类称为具体产品类，而将它们公共的代码进行抽象和提取后封装在一个抽象产品类中，每一个具体产品类都是抽象产品类的子类；
2. 然后提供一个工厂类用于创建各种产品，在工厂类中提供一个创建产品的工厂方法，该方法可以根据所传入的参数不同创建不同的具体产品对象；
3. 客户端只需调用工厂类的工厂方法并传入相应的参数即可得到一个产品对象。

简单工厂模式定义如下：

简单工厂模式(Simple Factory Pattern)：定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。

因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。

简单工厂模式的要点在于：当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。

## 简单工厂模式结构

<img src="https://halo-blog-img.oss-cn-hangzhou.aliyuncs.com/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E7%AE%80%E5%8D%95%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84.png" alt="简单工厂模式结构" style="object-fit: cover; border-radius: 10px; width: 100%;" />

在简单工厂模式结构中包含如下几个角色：

+ Factory（工厂角色）：工厂角色即工厂类，它是简单工厂模式的核心，负责实现创建所有产品实例的内部逻辑；工厂类可以被外界直接调用，创建所需的产品对象；在工厂类中提供了静态的工厂方法`factoryMethod()`，它的返回类型为抽象产品类型Product。
+ Product（抽象产品角色）：它是工厂类所创建的所有对象的父类，封装了各种产品对象的公有方法，它的引入将提高系统的灵活性，使得在工厂类中只需定义一个通用的工厂方法，因为所有创建的具体产品对象都是其子类对象。
+ ConcreteProduct（具体产品角色）：它是简单工厂模式的创建目标，所有被创建的对象都充当这个角色的某个具体类的实例。每一个具体产品角色都继承了抽象产品角色，需要实现在抽象产品中声明的抽象方法。

## 简单工厂模式实现

在简单工厂模式中，客户端通过工厂类来创建一个产品类的实例，而无须直接使用`new`关键字来创建对象，它是工厂模式家族中最简单的一员。

在使用简单工厂模式时，首先需要对产品类进行重构，不能设计一个包罗万象的产品类，而需根据实际情况设计一个产品层次结构，将所有产品类公共的代码移至抽象产品类，并在抽象产品类中声明一些抽象方法，以供不同的具体产品类来实现，典型的抽象产品类代码如下所示：

```java
public abstract class Product {
    //所有产品类的公共业务方法
    public void methodSame() {
        //公共方法的实现
    }
    //声明抽象业务方法
    public abstract void methodDiff();
}
```

在具体产品类中实现了抽象产品类中声明的抽象业务方法，不同的具体产品类可以提供不同的实现，典型的具体产品类代码如下所示：

```java
class ConcreteProduct extends Product {
    //实现业务方法
    public void methodDiff() {
        //业务方法的实现
    }
}
```

简单工厂模式的核心是工厂类，在没有工厂类之前，客户端一般会使用`new`关键字来直接创建产品对象，而在引入工厂类之后，客户端可以通过工厂类来创建产品，在简单工厂模式中，工厂类提供了一个静态工厂方法供客户端使用，根据所传入的参数不同可以创建不同的产品对象，典型的工厂类代码如下所示：

```java
class Factory {
    //静态工厂方法
    public static Product getProduct(String arg) {
        Product product = null;
        if (arg.equalsIgnoreCase("A")) {
            product = new ConcreteProductA();
            //初始化设置product
        }
        else if (arg.equalsIgnoreCase("B")) {
            product = new ConcreteProductB();
            //初始化设置product
        }
        return product;
    }
}
```

在客户端代码中，我们通过调用工厂类的工厂方法即可得到产品对象，典型代码如下所示：

```java
class Client {
    public static void main(String args[]) {
        Product product;
        product = Factory.getProduct("A"); //通过工厂类创建产品对象
        product.methodSame();
        product.methodDiff();
    }
}
```

## 简单工厂模式应用实例

通过设置不同参数即可得到不同类型的图表，且能易扩展。

<img src="https://halo-blog-img.oss-cn-hangzhou.aliyuncs.com/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E7%AE%80%E5%8D%95%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B%E7%BB%93%E6%9E%84.png" alt="简单工厂模式应用实例结构" style="object-fit: cover; border-radius: 20px; width: 100%;" />

`Chart`接口充当抽象产品类，其子类`HistogramChart`、`PieChart`和`LineChart`充当具体产品类，`ChartFactory`充当工厂类。完整代码如下所示：

1. `Chart`：抽象图表接口，充当抽象产品类

   ```java
   interface Chart {
       public void display();
   }
   ```

2. `HistogramChart`：柱状图类，充当具体产品类

   ```java
   class HistogramChart implements Chart {
       public HistogramChart() {
           System.out.println("创建柱状图！");
   }
       public void display() {
           System.out.println("显示柱状图！");
       }
   }
   ```

3. `PieChart`：饼状图类，充当具体产品类

   ```java
   class PieChart implements Chart {
       public PieChart() {
           System.out.println("创建饼状图！");
   }
       public void display() {
           System.out.println("显示饼状图！");
       }
   }
   ```

4. `LineChart`：折线图类，充当具体产品类

   ```java
   class LineChart implements Chart {
       public LineChart() {
           System.out.println("创建折线图！");
   }
       public void display() {
           System.out.println("显示折线图！");
       }
   }
   ```

5. `ChartFactory`：图表工厂类，充当工厂类

   ```java
   class ChartFactory {
       //静态工厂方法
       public static Chart getChart(String type) {
           Chart chart = null;
           if (type.equalsIgnoreCase("histogram")) {
               chart = new HistogramChart();
               System.out.println("初始化设置柱状图！");
           }
           else if (type.equalsIgnoreCase("pie")) {
               chart = new PieChart();
               System.out.println("初始化设置饼状图！");
           }
           else if (type.equalsIgnoreCase("line")) {
               chart = new LineChart();
               System.out.println("初始化设置折线图！");
           }
           return chart;
       }
   }
   ```

6. `Client`：客户端测试类

   ```java
   class Client {
       public static void main(String args[]) {
           Chart chart;
           chart = ChartFactory.getChart("histogram"); //通过静态工厂方法创建产品
           chart.display();
       }
   }
   ```

编译并运行程序，输出结果如下：

```
创建柱状图！
初始化设置柱状图！
显示柱状图！
```

在客户端测试类中，我们使用工厂类的静态工厂方法创建产品对象，如果需要更换产品，只 需修改静态工厂方法中的参数即可，例如将柱状图改为饼状图，只需将代码：

```java
chart = ChartFactory.getChart("histogram");
```

改为：

```java
chart = ChartFactory.getChart("pie");
```

编译并运行程序，输出结果如下：

```
创建饼状图！
初始化设置饼状图！
显示饼状图！
```

不难发现在创建具体`Chart`对象时，每更换一个`Chart`对象都需要修改客户端代码中静态工厂方法的参数，客户端代码将要重新编译，这对于客户端而言，违反了“开闭原则”，有没有一种方法能够在不修改客户端代码的前提下更换具体产品对象呢？答案是肯定的，下面将介绍一种常用的实现方式。

我们可以将静态工厂方法的参数存储在XML或properties格式的配置文件中，如下config.xml所示：

```xml
<?xml version="1.0"?>
<config>
    <chartType>histogram</chartType>
</config>
```

再通过一个工具类XMLUtil来读取配置文件中的字符串参数，XMLUtil类的代码如下所示：

```java
import javax.xml.parsers.*;
import org.w3c.dom.*;
import org.xml.sax.SAXException;
import java.io.*;

public class XMLUtil {
    //该方法用于从XML配置文件中提取图表类型，并返回类型名
    public static String getChartType() {
        try {
            //创建文档对象
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();
            DocumentBuilder builder = dFactory.newDocumentBuilder();
            Document doc;
            doc = builder.parse(new File("config.xml"));
            
            //获取包含图表类型的文本节点
            NodeList nl = doc.getElementsByTagName("chartType");
            Node classNode = nl.item(0).getFirstChild();
            String chartType = classNode.getNodeValue().trim();
            return chartType;
        }
        catch(Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

在引入了配置文件和工具类`XMLUtil`之后，客户端代码修改如下：

```java
class Client {
    public static void main(String args[]) {
        Chart chart;
        String type = XMLUtil.getChartType(); //读取配置文件中的参数
        chart = ChartFactory.getChart(type); //创建产品对象
        chart.display();
    }
}
```

不难发现，在上述客户端代码中不包含任何与具体图表对象相关的信息，如果需要更换具体图表对象，只需修改配置文件config.xml，无须修改任何源代码，符合“开闭原则”。

## 简单工厂模式的简化

有时候，为了简化简单工厂模式，我们可以将抽象产品类和工厂类合并，将静态工厂方法移至抽象产品类中，如图所示：

![简单工厂模式的简化结构](https://halo-blog-img.oss-cn-hangzhou.aliyuncs.com/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E7%AE%80%E5%8D%95%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F%E7%9A%84%E7%AE%80%E5%8C%96%E7%BB%93%E6%9E%84.png)

客户端可以通过产品父类的静态工厂方法，根据参数的不同创建不同类型的产品子 类对象，这种做法在JDK等类库和框架中也广泛存在。

## 简单工厂模式总结

### 简单工厂模式优点

1. 工厂类包含必要的判断逻辑，可以决定在什么时候创建哪一个产品类的实例，客户端可以免除直接创建产品对象的职责，而仅仅“消费”产品，简单工厂模式<font color="#faa755">实现了对象创建和使用的分离</font>。
2. 客户端无须知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可，对于一些复杂的类名，通过简单工厂模式可以在一定程度减少使用者的记忆量。
3. 通过引入配置文件，可以在<font color="#faa755">不修改任何客户端代码的情况下更换和增加新的具体产品类</font>，在一定程度上提高了系统的灵活性。

### 简单工厂模式缺点

1. 由于工厂类集中了所有产品的创建逻辑，职责过重，一旦不能正常工作，整个系统都要受到影响。
2. 使用简单工厂模式势必会增加系统中类的个数（引入了新的工厂类），增加了系统的复杂度和理解难度。
3. 系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。
4. 简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。

### 模式适用环境

1. 工厂类负责创建的对象比较少，由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂。
2. 客户端只知道传入工厂类的参数，对于如何创建对象并不关心。



