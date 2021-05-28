---
title: Java 泛型
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-c@master/img/maximilian-weisbecker-1td5Iq5IvNc-unsplash.2cx83e37bytc.jpg
description: 泛型是一种“代码模板”，可以用一套代码套用各种类型。
large: true
tags:
  - Java
categories:
  - Java
date: 2021-05-20 21:47:40
---

## 什么是泛型


在讲解什么是泛型之前，我们先观察 Java 标准库提供的 `ArrayList`，它可以看作“可变长度”的数组，因为用起来比数组更方便。

实际上 `ArrayList` 内部就是一个 `Object[]` 数组，配合存储一个当前分配的长度，就可以充当“可变数组”：

```java
public class ArrayList {
    private Object[] array;
    private int size;
    public void add(Object e) {...}
    public void remove(int index) {...}
    public Object get(int index) {...}
}
```

如果用上述 `ArrayList` 存储 String 类型，会有这么几个缺点：

+ 需要强制转型；
+ 不方便，易出错。

例如，代码必须这么写：

```java
ArrayList list = new ArrayList();
list.add("Hello");
// 获取到Object，必须强制转型为String:
String first = (String) list.get(0);
```

很容易出现 `ClassCastException`，因为容易“误转型”：

```java
list.add(new Integer(123));
// ERROR: ClassCastException:
String second = (String) list.get(1);
```

要解决上述问题，我们可以为 String 单独编写一种 ArrayList：

```java
public class StringArrayList {
    private String[] array;
    private int size;
    public void add(String e) {...}
    public void remove(int index) {...}
    public String get(int index) {...}
}
```

这样一来，存入的必须是 String，取出的也一定是 String，不需要强制转型，因为编译器会强制检查放入的类型：

```java
StringArrayList list = new StringArrayList();
list.add("Hello");
String first = list.get(0);
// 编译错误: 不允许放入非String类型:
list.add(new Integer(123));
```

问题暂时解决。然而，新的问题是，如果要存储 Integer，还需要为 Integer 单独编写一种 ArrayList，实际上，还需要为其他所有 class 单独编写一种 ArrayList。这是不可能的，JDK 的 class 就有上千个，而且它还不知道其他人编写的 class。

为了解决新的问题，我们必须把 `ArrayList` 变成一种模板：`ArrayList<T>`，代码如下：

```java
public class ArrayList<T> {
    private T[] array;
    private int size;
    public void add(T e) {...}
    public void remove(int index) {...}
    public T get(int index) {...}
}
```

`T` 可以是任何 class。这样一来，我们就实现了，编写一次模版，可以创建任意类型的 `ArrayList`：

```java
// 创建可以存储String的ArrayList:
ArrayList<String> strList = new ArrayList<String>();
// 创建可以存储Float的ArrayList:
ArrayList<Float> floatList = new ArrayList<Float>();
// 创建可以存储Person的ArrayList:
ArrayList<Person> personList = new ArrayList<Person>();
```
 
因此，泛型就是定义一种模板，例如 `ArrayList<T>`，然后在代码中为用到的类创建对应的 `ArrayList<类型>`。由编译器针对类型作检查：

```java
strList.add("hello"); // OK
String s = strList.get(0); // OK
strList.add(new Integer(123)); // compile error!
Integer n = strList.get(0); // compile error!
```

这样一来，既实现了编写一次，万能匹配，又通过编译器保证了类型安全：这就是泛型。

### 向上转型

在 Java 标准库中的 `ArrayList<T>` 实现了 `List<T>` 接口，它可以向上转型为 `List<T>`：

```java
public class ArrayList<T> implements List<T> {
    //...
}
```

即类型 `ArrayList<T>` 可以向上转型为 `List<T>`。

```java
List<String> list = new ArrayList<String>();
```

要特别注意：不能把 `ArrayList<Integer>` 向上转型为 `ArrayList<Number>` 或 `List<Number>` 。

假设 `ArrayList<Integer>` 可以向上转型为 `ArrayList<Number>`，观察一下代码：

```java
// 创建ArrayList<Integer>类型：
ArrayList<Integer> integerList = new ArrayList<Integer>();
// 添加一个Integer：
integerList.add(new Integer(123));
// “向上转型”为ArrayList<Number>：
ArrayList<Number> numberList = integerList;
// 添加一个Float，因为Float也是Number：
numberList.add(new Float(12.34));
// 从ArrayList<Integer>获取索引为1的元素（即添加的Float）：
Integer n = integerList.get(1); // ClassCastException!
```

我们把一个 `ArrayList<Integer>` 转型为 `ArrayList<Number>` 类型后，这个 `ArrayList<Number>` 就可以接受 Float 类型，因为 Float 是 Number 的子类。但是，`ArrayList<Number>` 实际上和 `ArrayList<Integer>` 是同一个对象，也就是 `ArrayList<Integer>` 类型，它不可能接受 Float 类型， 所以在获取 Integer 的时候将产生  `ClassCastException`。

实际上，编译器为了避免这种错误，根本就不允许把 `ArrayList<Integer>` 转型为 `ArrayList<Number>`。

`ArrayList<Integer>` 和 `ArrayList<Number>` 两者完全没有继承关系。

## 使用泛型

使用 ArrayList 时，如果不定义泛型类型时，泛型类型实际上就是 Object：

```java
// 编译器警告
List list = new ArrayList();
list.add("Hello");
list.add("World");
String first = (String) list.get(0);
String second = (String) list.get(1);
```

此时，只能把 `<T>` 当作 Object 使用，没有发挥泛型的优势。

当我们定义泛型类型 `<String>` 后，`List<T>` 的泛型接口变为强类型 `List<String>` ：

```java
// 无编译器警告
List<String> list = new ArrayList<String>();
list.add("Hello");
list.add("World");
// 无强制转型:
String first = list.get(0);
String second = list.get(1);
```

当我们定义泛型类型 `<Number>` 后，`List<T>` 的泛型接口变为强类型 `List<Number>`：

```java
List<Number> list = new ArrayList<Number>();
list.add(new Integer(123));
list.add(new Double(12.34));
Number first = list.get(0);
Number second = list.get(1);
```

编译器如果能自动推断出泛型类型，就可以省略后面的泛型类型。例如，对于下面的代码：

```java
List<Number> list = new ArrayList<Number>();
```

编译器看到泛型类型 `List<Number>` 就可以自动推断出后面的 `ArrayList<T>` 的泛型类型必须是 `ArrayList<Number>`，因此，可以把代码简写为：

```java
// 可以省略后面的Number，编译器可以自动推断泛型类型
List<Number> list = new ArrayList<>();
```

除了 `ArrayList<T>` 使用了泛型，还可以在接口中使用泛型。例如，`Arrays.sort(Object[])` 可以对任意数组进行排序，但待排序的元素必须实现 `Comparable<T>` 这个泛型接口：

```java
public interface Comparable<T> {
    /**
     * 返回负数: 当前实例比参数o小
     * 返回0: 当前实例与参数o相等
     * 返回正数: 当前实例比参数o大
     */
    int compareTo(T o);
}
```

可以直接对 String 数组进行排序：

```java
String[] ss = new String[] { "Orange", "Apple", "Pear" };
Arrays.sort(ss);
System.out.println(Arrays.toString(ss));
```

这是因为 String 本身已经实现了 `Comparable<String>` 接口。如果换成我们自定义的 Person 类型

```java
// sort
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        Person[] ps = new Person[]{
                new Person("Bob", 61),
                new Person("Alice", 88),
                new Person("Lily", 75),
        };
        Arrays.sort(ps);
        System.out.println(Arrays.toString(ps));

    }
}

class Person {
    String name;
    int score;

    Person(String name, int score) {
        this.name = name;
        this.score = score;
    }

    public String toString() {
        return this.name + "," + this.score;
    }
}
```

运行程序，我们会得到 `ClassCastException`，即无法将 Person 转型为 Comparable。我们修改代码，让 Person 实现 `Comparable<T>` 接口：

```java
class Person implements Comparable<Person> {
    String name;
    int score;
    Person(String name, int score) {
        this.name = name;
        this.score = score;
    }
    public int compareTo(Person other) {
        return this.name.compareTo(other.name);
    }
    public String toString() {
        return this.name + "," + this.score;
    }
}
```

运行上述代码，可以正确实现按 `name` 进行排序。也可以修改比较逻辑，例如，按 `score` 从高到低排序。
## 编写泛型

编写泛型类比普通类要复杂。通常来说，泛型类一般用在集合类中，例如 `ArrayList<T>`，我们很少需要编写泛型类。

如果我们确实需要编写一个泛型类，那么，应该如何编写它？

可以按照以下步骤来编写一个泛型类。

首先，按照某种类型，例如 `String`，来编写类：

```java
public class Pair {
    private String first;
    private String last;
    public Pair(String first, String last) {
        this.first = first;
        this.last = last;
    }
    public String getFirst() {
        return first;
    }
    public String getLast() {
        return last;
    }
}
```

然后，标记所有的特定类型，这里是 `String`，最后，把特定类型 `String` 替换为 `T`，并申明 `<T>`：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() {
        return first;
    }
    public T getLast() {
        return last;
    }
}
```

熟练后即可直接从 `T` 开始编写。

### 静态方法

编写泛型类时，要特别注意，泛型类型 `<T>` 不能用于静态方法。例如：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() { ... }
    public T getLast() { ... }

    // 对静态方法使用<T>:
    public static Pair<T> create(T first, T last) {
        return new Pair<T>(first, last);
    }
}
```

上述代码会导致编译错误，我们无法在静态方法 `create()` 的方法参数和返回类型上使用泛型类型 `T`。

可以在 `static` 修饰符后面加一个 `<T>`，编译就能通过：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() { ... }
    public T getLast() { ... }

    // 可以编译通过
    public static <T> Pair<T> create(T first, T last) {
        return new Pair<T>(first, last);
    }
}
```

但实际上，这个 `<T>` 和 `Pair<T>` 类型的 `<T>` 已经没有任何关系了。

对于静态方法，我们可以单独改写为“泛型”方法，只需要使用另一个类型即可。对于上面的 `create()` 静态方法，我们应该把它改为另一种泛型类型，例如，`<K>`：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() { ... }
    public T getLast() { ... }

    // 静态泛型方法应该使用其他类型区分
    public static <K> Pair<K> create(K first, K last) {
        return new Pair<K>(first, last);
    }
}
```

这样才能清楚地将静态方法的泛型类型和实例类型的泛型类型区分开。

### 多个泛型类型

泛型还可以定义多种类型。例如，我们希望 `Pair` 不总是存储两个类型一样的对象，就可以使用类型 `<T, K>`：

```java
public class Pair<T, K> {
    private T first;
    private K last;
    public Pair(T first, K last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() { ... }
    public K getLast() { ... }
}
```

使用的时候，需要指出两种类型：

```java
Pair<String, Integer> p = new Pair<>("test", 123);
```

Java 标准库的 `Map<K, V>` 就是使用两种泛型类型的例子。它对 Key 使用一种类型，对 Value 使用另一种类型。

### 擦拭法

泛型是一种类似“模板代码”的技术，不同语言的泛型实现方式不一定相同。Java 语言的泛型实现方式是擦拭法（Type Erasure）。

所谓擦拭法是指，虚拟机对泛型其实一无所知，所有的工作都是编译器做的。例如，我们编写了一个泛型类 `Pair<T>`，这是编译器看到的代码：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() {
        return first;
    }
    public T getLast() {
        return last;
    }
}
```

而虚拟机根本不知道泛型。这是虚拟机执行的代码：

```java
public class Pair {
    private Object first;
    private Object last;
    public Pair(Object first, Object last) {
        this.first = first;
        this.last = last;
    }
    public Object getFirst() {
        return first;
    }
    public Object getLast() {
        return last;
    }
}
```

因此，Java 使用擦拭法实现泛型，导致了：

+ 编译器把类型 `<T>` 视为 Object；
+ 编译器根据 `<T>` 实现安全的强制转型。

使用泛型的时候，我们编写的代码也是编译器看到的代码：

```java
Pair<String> p = new Pair<>("Hello", "world");
String first = p.getFirst();
String last = p.getLast();
```

而虚拟机执行的代码并没有泛型：

```java
Pair p = new Pair("Hello", "world");
String first = (String) p.getFirst();
String last = (String) p.getLast();
```

所以，Java 的泛型是由编译器在编译时实行的，编译器内部永远把所有类型 `T` 视为 Object 处理，但是，在需要转型的时候，编译器会根据 `T` 的类型自动为我们实行安全地强制转型。

了解了 Java 泛型的实现方式——擦拭法，我们就知道了 Java 泛型的局限：

局限一：`<T>` 不能是基本类型，例如 int，因为实际类型是 Object，Object 类型无法持有基本类型：

```java
Pair<int> p = new Pair<>(1, 2); // compile error!
```

局限二：无法取得带泛型的 Class。观察以下代码：

```java
public class Main {
    public static void main(String[] args) {
        Pair<String> p1 = new Pair<>("Hello", "world");
        Pair<Integer> p2 = new Pair<>(123, 456);
        Class c1 = p1.getClass();
        Class c2 = p2.getClass();
        System.out.println(c1==c2); // true
        System.out.println(c1==Pair.class); // true
    }
}

class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() {
        return first;
    }
    public T getLast() {
        return last;
    }
}
```

因为 `T` 是 Object，我们对 `Pair<String>` 和 `Pair<Integer>` 类型获取 Class 时，获取到的是同一个 Class，也就是 Pair 类的 Class。

换句话说，所有泛型实例，无论 `T` 的类型是什么，`getClass()` 返回同一个 `Class` 实例，因为编译后它们全部都是 `Pair<Object>`。

局限三：无法判断带泛型的类型：

```java
Pair<Integer> p = new Pair<>(123, 456);
// Compile error:
if (p instanceof Pair<String>) {
}
```
原因和前面一样，并不存在 `Pair<String>.class`，而是只有唯一的 `Pair.class`。

局限四：不能实例化 `T` 类型：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair() {
        // Compile error:
        first = new T();
        last = new T();
    }
}
```

上述代码无法通过编译，因为构造方法的两行语句：

```java
first = new T();
last = new T();
```

擦拭后实际上变成了：

```java
first = new Object();
last = new Object();
```

这样一来，创建 `new Pair<String>()` 和创建 `new Pair<Integer>()` 就全部成了 Object，显然编译器要阻止这种类型不对的代码。

要实例化 `T` 类型，我们必须借助额外的 `Class<T>` 参数：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(Class<T> clazz) {
        first = clazz.newInstance();
        last = clazz.newInstance();
    }
}
```

上述代码借助 `Class<T>` 参数并通过反射来实例化T类型，使用的时候，也必须传入 `Class<T>` 。例如：

```java
Pair<String> pair = new Pair<>(String.class);
```

因为传入了 `Class<String>` 的实例，所以我们借助 `String.class` 就可以实例化 String 类型。

### 不恰当的覆写方法

有些时候，一个看似正确定义的方法会无法通过编译。例如：

```java
public class Pair<T> {
    public boolean equals(T t) {
        return this == t;
    }
}
```

这是因为，定义的 `equals(T t)` 方法实际上会被擦拭成 `equals(Object t)`，而这个方法是继承自 Object 的，编译器会阻止一个实际上会变成覆写的泛型方法定义。

换个方法名，避开与 `Object.equals(Object)` 的冲突就可以成功编译：

```java
public class Pair<T> {
    public boolean same(T t) {
        return this == t;
    }
}
```

### 泛型继承

一个类可以继承自一个泛型类。例如：父类的类型是 `Pair<Integer>`，子类的类型是 `IntPair`，可以这么继承：

```java
public class IntPair extends Pair<Integer> {
}
```

使用的时候，因为子类 `IntPair` 并没有泛型类型，所以，正常使用即可。

前面讲了，我们无法获取 `Pair<T>` 的 `T` 类型，即给定一个变量 `Pair<Integer> p`，无法从 `p` 中获取到 `Integer` 类型。

但是，在父类是泛型类型的情况下，编译器就必须把类型 `T`（对 `IntPair` 来说，也就是 `Integer` 类型）保存到子类的 class 文件中，不然编译器就不知道 `IntPair` 只能存取 Integer 这种类型。

在继承了泛型类型的情况下，子类可以获取父类的泛型类型。例如：`IntPair` 可以获取到父类的泛型类型 Integer。获取父类的泛型类型代码比较复杂：

```java
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;

public class Main {
    public static void main(String[] args) {
        Class<IntPair> clazz = IntPair.class;
        Type t = clazz.getGenericSuperclass();
        if (t instanceof ParameterizedType) {
            ParameterizedType pt = (ParameterizedType) t;
            Type[] types = pt.getActualTypeArguments(); // 可能有多个泛型类型
            Type firstType = types[0]; // 取第一个泛型类型
            Class<?> typeClass = (Class<?>) firstType;
            System.out.println(typeClass); // Integer
        }
    }
}

class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() {
        return first;
    }
    public T getLast() {
        return last;
    }
}

class IntPair extends Pair<Integer> {
    public IntPair(Integer first, Integer last) {
        super(first, last);
    }
}
```

因为 Java 引入了泛型，所以，只用 Class 来标识类型已经不够了。实际上，Java 的类型系统结构如下：

```
                      ┌────┐
                      │Type│
                      └────┘
                         ▲
                         │
   ┌────────────┬────────┴─────────┬───────────────┐
   │            │                  │               │
┌─────┐┌─────────────────┐┌────────────────┐┌────────────┐
│Class││ParameterizedType││GenericArrayType││WildcardType│
└─────┘└─────────────────┘└────────────────┘└────────────┘

```

## extends 通配符

我们前面已经讲到了泛型的继承关系：`Pair<Integer>` 不是 `Pair<Number>` 的子类。

假设我们定义了 `Pair<T>`，然后，我们又针对 `Pair<Number>` 类型写了一个静态方法，它接收的参数类型是 `Pair<Number>`：

```java
public class PairHelper {
    static int add(Pair<Number> p) {
        Number first = p.getFirst();
        Number last = p.getLast();
        return first.intValue() + last.intValue();
    }
}
```

上述代码是可以正常编译的。使用的时候，我们传入：

```java
int sum = PairHelper.add(new Pair<Number>(1, 2));
```