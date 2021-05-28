---
title: Java 异常处理
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-c@master/img/maximilian-weisbecker-1td5Iq5IvNc-unsplash.2cx83e37bytc.jpg
description: Java 异常和日志
tags:
  - Java
categories:
  - Java
date: 2021-05-20 17:33:40
---

参考资料：

{% link 廖雪峰Java教程, https://www.liaoxuefeng.com/wiki/1252599548343744/1255943543190176, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@master/img/网站.svg %}

## Java 的异常

在计算机程序运行的过程中，总是会出现各种各样的错误。

有一些错误是用户造成的，比如，希望用户输入一个 int 类型的年龄，但是用户的输入是 abc：

```java
// 假设用户输入了abc：
String s = "abc";
int n = Integer.parseInt(s); // NumberFormatException
```

程序想要读写某个文件的内容，但是用户已经把它删除了：

```java
// 用户删除了该文件：
String t = readFile("C:\\abc.txt"); // FileNotFoundException!
```

还有一些错误是随机出现，并且永远不可能避免的。比如：

+ 网络突然断了，连接不到远程服务器；
+ 内存耗尽，程序崩溃了；
+ 用户点“打印”，但根本没有打印机；
+ ……

所以，一个健壮的程序必须处理各种各样的错误。

所谓错误，就是程序调用某个函数的时候，如果失败了，就表示出错。

调用方如何获知调用失败的信息？有两种方法：

方法一：约定返回错误码。

例如，处理一个文件，如果返回 0，表示成功，返回其他整数，表示约定的错误码：

```java
int code = processFile("C:\\test.txt");
if (code == 0) {
    // ok:
} else {
    // error:
    switch (code) {
    case 1:
        // file not found:
    case 2:
        // no read permission:
    default:
        // unknown error:
    }
}
```

因为使用 int 类型的错误码，想要处理就非常麻烦。这种方式常见于底层C函数。

方法二：在语言层面上提供一个异常处理机制。

Java 内置了一套异常处理机制，总是使用异常来表示错误。

异常是一种 class，因此它本身带有类型信息。异常可以在任何地方抛出，但只需要在上层捕获，这样就和方法调用分离了：

```java
try {
    String s = processFile(“C:\\test.txt”);
    // ok:
} catch (FileNotFoundException e) {
    // file not found:
} catch (SecurityException e) {
    // no read permission:
} catch (IOException e) {
    // io error:
} catch (Exception e) {
    // other error:
}
```

从继承关系可知：`Throwable` 是异常体系的根，它继承自 `Object`。`Throwable` 有两个体系：`Error` 和 `Exception`，`Error` 表示严重的错误，程序对此一般无能为力，例如：

+ `OutOfMemoryError`：内存耗尽
+ `NoClassDefFoundError`：无法加载某个Class
+ `StackOverflowError`：栈溢出

而 `Exception` 则是运行时的错误，它可以被捕获并处理。

某些异常是应用程序逻辑处理的一部分，应该捕获并处理。例如：

+ `NumberFormatException`：数值类型的格式错误
+ `FileNotFoundException`：未找到文件
+ `SocketException`：读取网络失败

还有一些异常是程序逻辑编写不对造成的，应该修复程序本身。例如：

+ `NullPointerException`：对某个 null 的对象调用方法或字段
+ `IndexOutOfBoundsException`：数组索引越界

`Exception` 又分为两大类：

+ `RuntimeException` 以及它的子类；
+ 非 `RuntimeException`（包括 `IOException`、`ReflectiveOperationException` 等等）

Java规定：

+ 必须捕获的异常，包括 `Exception` 及其子类，但不包括 `RuntimeException` 及其子类，这种类型的异常称为 Checked Exception。
+ 不需要捕获的异常，包括 `Error` 及其子类，`RuntimeException` 及其子类。

注意：编译器对 `RuntimeException` 及其子类不做强制捕获要求，不是指应用程序本身不应该捕获并处理 `RuntimeException`。是否需要捕获，具体问题具体分析。

## 捕获异常

捕获异常使用 `try...catch` 语句，把可能发生异常的代码放到 `try {...}` 中，然后使用 `catch` 捕获对应的 `Exception` 及其子类：

```java
public class Main {
    public static void main(String[] args) {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
    }

    static byte[] toGBK(String s) {
        try {
            // 用指定编码转换String为byte[]:
            return s.getBytes("GBK");
        } catch (UnsupportedEncodingException e) {
            // 如果系统不支持GBK编码，会捕获到UnsupportedEncodingException:
            System.out.println(e); // 打印异常信息
            return s.getBytes(); // 尝试使用用默认编码
        }
    }
}
```

如果我们不捕获 `UnsupportedEncodingException`，会出现编译失败的问题

编译器会报错，错误信息类似：`unreported exception UnsupportedEncodingException; must be caught or declared to be thrown`，并且准确地指出需要捕获的语句是 `return s.getBytes("GBK");` 。意思是说，像 `UnsupportedEncodingException` 这样的 Checked Exception，必须被捕获。

这是因为 `String.getBytes(String)` 方法定义是：

```java
public byte[] getBytes(String charsetName) throws UnsupportedEncodingException {
    ...
}
```

在方法定义的时候，使用 `throws Xxx` 表示该方法可能抛出的异常类型。调用方在调用的时候，必须强制捕获这些异常，否则编译器会报错。
`
在 `toGBK()` 方法中，因为调用了 `String.getBytes(String)` 方法，就必须捕获 `UnsupportedEncodingException`。我们也可以不捕获它，而是在方法定义处用 `throws` 表示 `toGBK()` 方法可能会抛出`UnsupportedEncodingException`，就可以让 `toGBK()` 方法通过编译器检查：

```java
public class Main {
    public static void main(String[] args) {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
    }

    static byte[] toGBK(String s) throws UnsupportedEncodingException {
        return s.getBytes("GBK");
    }
}
```

上述代码仍然会得到编译错误，但这一次，编译器提示的不是调用 `return s.getBytes("GBK");` 的问题，而是 `byte[] bs = toGBK("中文");`。因为在 `main()` 方法中，调用 `toGBK()`，没有捕获它声明的可能抛出的 `UnsupportedEncodingException` 。修复方法是在 `main()` 方法中捕获异常并处理：

```java
public class Main {
    public static void main(String[] args) {
        try {
            byte[] bs = toGBK("中文");
            System.out.println(Arrays.toString(bs));
        } catch (UnsupportedEncodingException e) {
            System.out.println(e);
        }
    }

    static byte[] toGBK(String s) throws UnsupportedEncodingException {
        // 用指定编码转换String为byte[]:
        return s.getBytes("GBK");
    }
}
```

可见，只要是方法声明的 Checked Exception，不在调用层捕获，也必须在更高的调用层捕获。所有未捕获的异常，最终也必须在 `main()` 方法中捕获，不会出现漏写 `try` 的情况。这是由编译器保证的。`main()` 方法也是最后捕获 `Exception` 的机会。

如果是测试代码，上面的写法就略显麻烦。如果不想写任何 `try` 代码，可以直接把 `main()` 方法定义为 `throws Exception`：

```java
public class Main {
    public static void main(String[] args) throws Exception {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
    }

    static byte[] toGBK(String s) throws UnsupportedEncodingException {
        // 用指定编码转换String为byte[]:
        return s.getBytes("GBK");
    }
}
```

因为 `main()` 方法声明了可能抛出 `Exception`，也就声明了可能抛出所有的 `Exception`，因此在内部就无需捕获了。代价就是一旦发生异常，程序会立刻退出。

捕获后不处理的方式是非常不好的，即使真的什么也做不了，也要先把异常记录下来。所有异常都可以调用 `printStackTrace()` 方法打印异常栈，这是一个简单有用的快速打印异常的方法。

```java
static byte[] toGBK(String s) {
    try {
        return s.getBytes("GBK");
    } catch (UnsupportedEncodingException e) {
        // 先记下来再说:
        e.printStackTrace();
    }
    return null;
```

### 多 catch 语句

可以使用多个 `catch` 语句，每个 `catch` 分别捕获对应的 `Exception` 及其子类。JVM 在捕获到异常后，会从上到下匹配 `catch` 语句，匹配到某个 `catch` 后，执行 `catch` 代码块，然后不再继续匹配。

简单地说就是：多个 `catch` 语句只有一个能被执行。例如：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
    } catch (IOException e) {
        System.out.println(e);
    } catch (NumberFormatException e) {
        System.out.println(e);
    }
}
```

存在多个 `catch` 的时候，`catch` 的顺序非常重要，子类必须写在前面。例如：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
    } catch (IOException e) {
        System.out.println("IO error");
    } catch (UnsupportedEncodingException e) { // 永远捕获不到
        System.out.println("Bad encoding");
    }
}
```

对于上面的代码，`UnsupportedEncodingException` 异常是永远捕获不到的，因为它是 `IOException` 的子类。当抛出 `UnsupportedEncodingException` 异常时，会被 `catch (IOException e) { ... }` 捕获并执行。因此，正确的写法是把子类放到前面：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
    } catch (UnsupportedEncodingException e) {
        System.out.println("Bad encoding");
    } catch (IOException e) {
        System.out.println("IO error");
    }
}
```

### finally 语句

无论是否有异常发生，如果我们都希望执行一些语句，例如清理工作，怎么写？

可以把执行语句写若干遍：正常执行的放到 try 中，每个 catch 再写一遍。例如：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
        System.out.println("END");
    } catch (UnsupportedEncodingException e) {
        System.out.println("Bad encoding");
        System.out.println("END");
    } catch (IOException e) {
        System.out.println("IO error");
        System.out.println("END");
    }
}
```

上述代码无论是否发生异常，都会执行 `System.out.println("END");` 这条语句。

那么如何消除这些重复的代码？Java 的 `try ... catch` 机制还提供了 `finally` 语句，`finally` 语句块保证有无错误都会执行。上述代码可以改写如下：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
    } catch (UnsupportedEncodingException e) {
        System.out.println("Bad encoding");
    } catch (IOException e) {
        System.out.println("IO error");
    } finally {
        System.out.println("END");
    }
}
```

注意 `finally` 有几个特点：

+ `finally` 语句不是必须的，可写可不写；
+ `finally` 总是最后执行。


如果没有发生异常，就正常执行 `try { ... }` 语句块，然后执行 `finally`。如果发生了异常，就中断执行 `try { ... }` 语句块，然后跳转执行匹配的 `catch` 语句块，最后执行 `finally`。

可见，`finally` 是用来保证一些代码必须执行的。

某些情况下，可以没有 `catch`，只使用 `try ... finally` 结构。例如：

```java
void process(String file) throws IOException {
    try {
        ...
    } finally {
        System.out.println("END");
    }
}
```

因为方法声明了可能抛出的异常，所以可以不写 `catch`。

TODO: IO情况下不安全问题


### 捕获多种异常


如果某些异常的处理逻辑相同，但是异常本身不存在继承关系，那么就得编写多条 `catch` 子句：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
    } catch (IOException e) {
        System.out.println("Bad input");
    } catch (NumberFormatException e) {
        System.out.println("Bad input");
    } catch (Exception e) {
        System.out.println("Unknown error");
    }
}
```

因为处理 `IOException` 和 `NumberFormatException` 的代码是相同的，所以我们可以把它两用 `|` 合并到一起：

```java
public static void main(String[] args) {
    try {
        process1();
        process2();
        process3();
    } catch (IOException | NumberFormatException e) { // IOException或NumberFormatException
        System.out.println("Bad input");
    } catch (Exception e) {
        System.out.println("Unknown error");
    }
}
```

## 抛出异常


### 异常的传播

当某个方法抛出了异常时，如果当前方法没有捕获异常，异常就会被抛到上层调用方法，直到遇到某个 `try ... catch` 被捕获为止：

```java
public class Main {
    public static void main(String[] args) {
        try {
            process1();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static void process1() {
        process2();
    }

    static void process2() {
        Integer.parseInt(null); // 会抛出NumberFormatException
    }
}
```

通过 `printStackTrace()` 可以打印出方法的调用栈，类似：

```
java.lang.NumberFormatException: null
    at java.base/java.lang.Integer.parseInt(Integer.java:614)
    at java.base/java.lang.Integer.parseInt(Integer.java:770)
    at Main.process2(Main.java:16)
    at Main.process1(Main.java:12)
    at Main.main(Main.java:5)
```

`printStackTrace()` 对于调试错误非常有用，上述信息表示：`NumberFormatException` 是在 `java.lang.Integer.parseInt` 方法中被抛出的，从下往上看，调用层次依次是：

1. `main()` 调用 `process1()`
2. `process1()` 调用 `process2()` 
3. `process2()` 调用 `Integer.parseInt(String)`
4. `Integer.parseInt(String)` 调用 `Integer.parseInt(String, int)`

查看 `Integer.java` 源码可知，抛出异常的方法代码如下：

```java
public static int parseInt(String s, int radix) throws NumberFormatException {
    if (s == null) {
        throw new NumberFormatException("null");
    }
    // ...
}
```

并且，每层调用均给出了源代码的行号，可直接定位。

### 抛出异常

当发生错误时，例如，用户输入了非法的字符，我们就可以抛出异常。

如何抛出异常？参考 `Integer.parseInt()` 方法，抛出异常分两步：

1. 创建某个 `Exception` 的实例；
2. 用 `throw` 语句抛出。

下面是一个例子：

```java
void process2(String s) {
    if (s==null) {
        NullPointerException e = new NullPointerException();
        throw e;
    }
}
```

实际上，绝大部分抛出异常的代码都会合并写成一行：

```java
void process2(String s) {
    if (s==null) {
        throw new NullPointerException();
    }
}
```

如果一个方法捕获了某个异常后，又在 `catch` 子句中抛出新的异常，就相当于把抛出的异常类型“转换”了，当 `process2()` 抛出 `NullPointerException` 后，被 `process1()` 捕获，然后抛出 `IllegalArgumentException()`：

```java
void process1(String s) {
    try {
        process2();
    } catch (NullPointerException e) {
        throw new IllegalArgumentException();
    }
}

void process2(String s) {
    if (s==null) {
        throw new NullPointerException();
    }
}
```

如果在 `main()` 中捕获 `IllegalArgumentException`，我们看看打印的异常栈：

```java
public class Main {
    public static void main(String[] args) {
        try {
            process1();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static void process1() {
        try {
            process2();
        } catch (NullPointerException e) {
            throw new IllegalArgumentException();
        }
    }

    static void process2() {
        throw new NullPointerException();
    }
}
```

打印出的异常栈类似：

```
java.lang.IllegalArgumentException
    at Main.process1(Main.java:15)
    at Main.main(Main.java:5)
```

这说明新的异常丢失了原始异常信息，我们已经看不到原始异常 `NullPointerException` 的信息了。

为了能追踪到完整的异常栈，在构造异常的时候，把原始的 `Exception` 实例传进去，新的 `Exception` 就可以持有原始 `Exception` 信息。对上述代码改进如下：

```java
public class Main {
    public static void main(String[] args) {
        try {
            process1();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static void process1() {
        try {
            process2();
        } catch (NullPointerException e) {
            throw new IllegalArgumentException(e);
        }
    }

    static void process2() {
        throw new NullPointerException();
    }
}
```

```
java.lang.IllegalArgumentException: java.lang.NullPointerException
    at Main.process1(Main.java:15)
    at Main.main(Main.java:5)
Caused by: java.lang.NullPointerException
    at Main.process2(Main.java:20)
    at Main.process1(Main.java:13)
```

注意到 `Caused by: Xxx`，说明捕获的 `IllegalArgumentException` 并不是造成问题的根源，根源在于 `NullPointerException`，是在 `Main.process2()` 方法抛出的。

在代码中获取原始异常可以使用 `Throwable.getCause()` 方法。如果返回 `null`，说明已经是“根异常”了。

有了完整的异常栈的信息，我们才能快速定位并修复代码的问题。捕获到异常并再次抛出时，一定要留住原始异常，否则很难定位第一案发现场！

如果我们在 `try` 或者 `catch` 语句块中抛出异常，`finally` 语句是否会执行？例如：

```java
public class Main {
    public static void main(String[] args) {
        try {
            Integer.parseInt("abc");
        } catch (Exception e) {
            System.out.println("caught");
            throw new RuntimeException(e);
        } finally {
            System.out.println("finally");
        }
    }
}
```

上述代码执行结果如下：

```
caught
finally
Exception in thread "main" java.lang.RuntimeException: java.lang.NumberFormatException: For input string: "abc"
    at Main.main(Main.java:8)
Caused by: java.lang.NumberFormatException: For input string: "abc"
	  at ...
```

第一行打印了 `caught`，说明进入了 catch 语句块。第二行打印了 `finally` ，说明执行了 finally 语句块。

因此，在 catch 中抛出异常，不会影响 finally 的执行。JVM 会先执行 finally，然后抛出异常。

### 异常屏蔽

如果在执行 finally 语句时抛出异常，那么，catch 语句的异常还能否继续抛出？例如：

```java
public class Main {
    public static void main(String[] args) {
        try {
            Integer.parseInt("abc");
        } catch (Exception e) {
            System.out.println("caught");
            throw new RuntimeException(e);
        } finally {
            System.out.println("finally");
            throw new IllegalArgumentException();
        }
    }
}
```

执行上述代码，发现异常信息如下：

```
caught
finally
Exception in thread "main" java.lang.IllegalArgumentException
    at Main.main(Main.java:11)
```

这说明 finally 抛出异常后，原来在 catch 中准备抛出的异常就“消失”了，因为只能抛出一个异常。没有被抛出的异常称为“被屏蔽”的异常（Suppressed Exception）。

在极少数的情况下，我们需要获知所有的异常。如何保存所有的异常信息？方法是先用 `origin` 变量保存原始异常，然后调用 `Throwable.addSuppressed()`，把原始异常添加进来，最后在 finally 抛出：

```java
public class Main {
    public static void main(String[] args) throws Exception {
        Exception origin = null;
        try {
            System.out.println(Integer.parseInt("abc"));
        } catch (Exception e) {
            origin = e;
            throw e;
        } finally {
            Exception e = new IllegalArgumentException();
            if (origin != null) {
                e.addSuppressed(origin);
            }
            throw e;
        }
    }
}
```

当 catch 和 finally 都抛出了异常时，虽然 catch 的异常被屏蔽了，但是，finally 抛出的异常仍然包含了它：

```
Exception in thread "main" java.lang.IllegalArgumentException
    at Main.main(Main.java:11)
Suppressed: java.lang.NumberFormatException: For input string: "abc"
    at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
    at java.base/java.lang.Integer.parseInt(Integer.java:652)
    at java.base/java.lang.Integer.parseInt(Integer.java:770)
    at Main.main(Main.java:6)
```

通过 `Throwable.getSuppressed()` 可以获取所有的 `Suppressed Exception`。

绝大多数情况下，在 finally 中不要抛出异常。因此，我们通常不需要关心 `Suppressed Exception`。

## 自定义异常

Java 标准库定义的常用异常包括：

```
Exception
│
├─ RuntimeException
│  │
│  ├─ NullPointerException
│  │
│  ├─ IndexOutOfBoundsException
│  │
│  ├─ SecurityException
│  │
│  └─ IllegalArgumentException
│     │
│     └─ NumberFormatException
│
├─ IOException
│  │
│  ├─ UnsupportedCharsetException
│  │
│  ├─ FileNotFoundException
│  │
│  └─ SocketException
│
├─ ParseException
│
├─ GeneralSecurityException
│
├─ SQLException
│
└─ TimeoutException
```

当我们在代码中需要抛出异常时，尽量使用 JDK 已定义的异常类型。例如，参数检查不合法，应该抛出 `IllegalArgumentException`：

```java
static void process1(int age) {
    if (age <= 0) {
        throw new IllegalArgumentException();
    }
}
```

在一个大型项目中，可以自定义新的异常类型，但是，保持一个合理的异常继承体系是非常重要的。

一个常见的做法是自定义一个 `BaseException` 作为“根异常”，然后，派生出各种业务类型的异常。

`BaseException` 需要从一个适合的 `Exception` 派生，通常建议从 `RuntimeException` 派生：

```java
public class BaseException extends RuntimeException {
}
```

其他业务类型的异常就可以从 `BaseException` 派生：

```java
public class UserNotFoundException extends BaseException {
}

public class LoginFailedException extends BaseException {
}

// ...
```

自定义的 `BaseException` 应该提供多个构造方法：

```java
public class BaseException extends RuntimeException {
    public BaseException() {
        super();
    }

    public BaseException(String message, Throwable cause) {
        super(message, cause);
    }

    public BaseException(String message) {
        super(message);
    }

    public BaseException(Throwable cause) {
        super(cause);
    }
}
```

上述构造方法实际上都是原样照抄 `RuntimeException`。这样，抛出异常的时候，就可以选择合适的构造方法。通过IDE可以根据父类快速生成子类的构造方法。

## NullPointerException

在所有的 `RuntimeException` 异常中，Java 程序员最熟悉的恐怕就是 `NullPointerException` 了。

`NullPointerException` 即空指针异常，俗称 NPE。如果一个对象为 `null`，调用其方法或访问其字段就会产生 `NullPointerException`，这个异常通常是由 JVM 抛出的，例如：

```java
public class Main {
    public static void main(String[] args) {
        String s = null;
        System.out.println(s.toLowerCase());
    }
}
```

指针这个概念实际上源自 C 语言，Java 语言中并无指针。我们定义的变量实际上是引用，Null Pointer 更确切地说是 Null Reference，不过两者区别不大。

### 处理NullPointerException

如果遇到 `NullPointerException`，我们应该如何处理？首先，必须明确，`NullPointerException` 是一种代码逻辑错误，遇到 `NullPointerException`，遵循原则是早暴露，早修复，严禁使用 `catch` 来隐藏这种编码错误，好的编码习惯可以极大地降低 `NullPointerException` 的产生，例如成员变量在定义时初始化：

```java
public class Person {
    private String name = "";
}
```

使用空字符串 `""` 而不是默认的 `null` 可避免很多 `NullPointerException` ，编写业务逻辑时，用空字符串 `""` 表示未填写比 `null` 安全得多。

返回空字符串 `""` 、空数组而不是 `null` ：

```java
public String[] readLinesFromFile(String file) {
    if (getFileSize(file) == 0) {
        // 返回空数组而不是null:
        return new String[0];
    }
    // ...
}
```

这样可以使得调用方无需检查结果是否为 `null`。

如果调用方一定要根据 `null` 判断，比如返回 `null` 表示文件不存在，那么考虑返回 `Optional<T>`：

```java
public Optional<String> readFromFile(String file) {
    if (!fileExist(file)) {
        return Optional.empty();
    }
    // ...
}
```

这样调用方必须通过 `Optional.isPresent()` 判断是否有结果。

### 定位 NullPointerException

如果产生了 `NullPointerException`，例如，调用 `a.b.c.x()` 时产生了 `NullPointerException`，原因可能是：

+ `a` 是 `null`；
+ `a.b` 是 `null`；
+ `a.b.c` 是 `null`；

确定到底是哪个对象是 `null` 以前只能打印这样的日志：

```java
System.out.println(a);
System.out.println(a.b);
System.out.println(a.b.c);
```

从 Java 14 开始，如果产生了 `NullPointerException`，JVM 可以给出详细的信息告诉我们 `null` 对象到底是谁。我们来看例子：

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Person();
        System.out.println(p.address.city.toLowerCase());
    }
}

class Person {
    String[] name = new String[2];
    Address address = new Address();
}

class Address {
    String city;
    String street;
    String zipcode;
}
```

```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.toLowerCase()" because "p.address.city" is null
	at Main.main(Main.java:4)
```

可以在 `NullPointerException` 的详细信息中看到类似 `... because "<local1>.address.city" is null`，意思是 `city` 字段为 `null`，这样我们就能快速定位问题所在。

这种增强的 `NullPointerException` 详细信息是 Java 14 新增的功能，但默认是关闭的，我们可以给 JVM 添加一个 `-XX:+ShowCodeDetailsInExceptionMessages` 参数启用它，在 JDK 15 及以上默认是开启的。

## 使用断言

断言（Assertion）是一种调试程序的方式。在 Java 中，使用 `assert` 关键字来实现断言。我们先看一个例子：

```java
public static void main(String[] args) {
    double x = Math.abs(-123.45);
    assert x >= 0;
    System.out.println(x);
}
```

语句 `assert x >= 0;` 即为断言，断言条件 `x >= 0` 预期为 `true`。如果计算结果为 `false`，则断言失败，抛出 `AssertionError`。

使用 `assert` 语句时，还可以添加一个可选的断言消息：

```java
assert x >= 0 : "x must >= 0";
```

这样，断言失败的时候，`AssertionError` 会带上消息 `x must >= 0`，更加便于调试。

Java 断言的特点是：断言失败时会抛出 `AssertionError`，导致程序结束退出。因此，断言不能用于可恢复的程序错误，只应该用于开发和测试阶段。

对于可恢复的程序错误，不应该使用断言。例如：

```java
void sort(int[] arr) {
    assert arr != null;
}
```

应该抛出异常并在上层捕获：

```java
void sort(int[] arr) {
    if (x == null) {
        throw new IllegalArgumentException("array cannot be null");
    }
}
```

当我们在程序中使用 `assert` 时，例如，一个简单的断言：

```java
public class Main {
    public static void main(String[] args) {
        int x = -1;
        assert x > 0;
        System.out.println(x);
    }
}
```

断言 `x` 必须大于 0，实际上 `x` 为 -1，断言肯定失败。执行上述代码，发现程序并未抛出 `AssertionError`，而是正常打印了 `x` 的值。为什么 `assert` 语句不起作用？

这是因为 JVM 默认关闭断言指令，即遇到 `assert` 语句就自动忽略了，不执行。

要执行 `assert` 语句，必须给 Java 虚拟机传递 `-enableassertions`（可简写为 `-ea` ）参数启用断言。所以，上述程序必须在命令行下运行才有效果。

还可以有选择地对特定地类启用断言，命令行参数是：`-ea:com.halo.test.Main` ，表示只对 `com.halo.test.Main` 这个类启用断言。或者对特定地包启用断言，命令行参数是：`-ea:com.halo.test...`（注意结尾有 3 个 `.` ），表示对 `com.halo.test` 这个包启动断言。

实际开发中，很少使用断言。更好的方法是编写单元测试，例如 JUnit。

## 使用JDK Logging

在编写程序的过程中，发现程序运行结果与预期不符，怎么办？当然是用 `System.out.println()` 打印出执行过程中的某些变量，观察每一步的结果与代码逻辑是否符合，然后有针对性地修改代码。

代码改好了怎么办？当然是删除没有用的 `System.out.println()` 语句了。

如果改代码又改出问题怎么办？再加上 `System.out.println()`。

反复这么搞几次，很快大家就发现使用 `System.out.println()` 非常麻烦。

怎么办？解决方法是使用日志（Logging）。它的目的是为了取代 `System.out.println()`。

输出日志，而不是用 `System.out.println()`，有以下几个好处：

1. 可以设置输出样式，避免自己每次都写 `"ERROR: " + var`
2. 可以设置输出级别，禁止某些级别输出。例如，只输出错误日志
3. 可以被重定向到文件，这样可以在程序运行结束后查看日志
4. 可以按包名控制日志级别，只输出某些包打的日志
5. ……

因为 Java 标准库内置了日志包 `java.util.logging`，我们可以直接用。先看一个简单的例子：

```java
public class Hello {
    public static void main(String[] args) {
        Logger logger = Logger.getGlobal();
        logger.info("start process...");
        logger.warning("memory is running out...");
        logger.fine("ignored.");
        logger.severe("process will be terminated...");
    }
}
```

运行上述代码，得到类似如下的输出：

```
May 20, 2021 4:42:58 PM Hello main
INFO: start process...
May 20, 2021 4:42:58 PM Hello main
WARNING: memory is running out...
May 20, 2021 4:42:58 PM Hello main
SEVERE: process will be terminated...
```

对比可见，使用日志最大的好处是，它自动打印了时间、调用类、调用方法等很多有用的信息。再仔细观察发现，4 条日志，只打印了 3 条，`logger.fine()` 没有打印。这是因为，日志的输出可以设定级别。JDK 的 Logging 定义了 7 个日志级别，从严重到普通：SEVERE、WARNING、INFO、CONFIG、FINE、FINER、FINEST。

因为默认级别是 INFO，因此，INFO级别以下的日志，不会被打印出来。使用日志级别的好处在于，调整级别，就可以屏蔽掉很多调试相关的日志输出。

使用 Java 标准库内置的 Logging 有以下局限：

Logging 系统在 JVM 启动时读取配置文件并完成初始化，一旦开始运行 main() 方法，就无法修改配置；

配置不太方便，需要在 JVM 启动时传递参数 `-Djava.util.logging.config.file=<config-file-name>`。

因此，Java 标准库内置的 Logging 使用并不是非常广泛。

## 使用 Commons Logging

和 Java 标准库提供的日志不同，Commons Logging 是一个第三方日志库，它是由 Apache 创建的日志模块。

Commons Logging 的特色是，它可以挂接不同的日志系统，并通过配置文件指定挂接的日志系统。默认情况下，Commons Loggin 自动搜索并使用 Log4j（ Log4j 是另一个流行的日志系统），如果没有找到 Log4j，再使用 JDK Logging。

使用 Commons Logging 只需要和两个类打交道，并且只有两步：

+ 第一步，通过LogFactory获取Log类的实例
+ 第二步，使用Log实例的方法打日志。

示例代码如下：

```java
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;

public class Main {
    public static void main(String[] args) {
        Log log = LogFactory.getLog(Main.class);
        log.info("start...");
        log.warn("end.");
    }
}
```

Maven 项目 pom.xml 中添加如下依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```

运行结果如下：

```java
May 20, 2021 4:56:02 PM com.halo.logging.Main main
INFO: start...
May 20, 2021 4:56:02 PM com.halo.logging.Main main
WARNING: end.
```

Commons Logging 定义了 6 个日志级别：FATAL、ERROR、WARNING、INFO、DEBUG、TRACE。默认级别是 INFO。

使用 Commons Logging 时，如果在静态方法中引用 Log，通常直接定义一个静态类型变量：

```java
// 在静态方法中引用 Log:
public class Main {
    static final Log log = LogFactory.getLog(Main.class);

    static void foo() {
        log.info("foo");
    }
}
```

在实例方法中引用 `Log`，通常定义一个实例变量：

```java
// 在实例方法中引用Log:
public class Person {
    protected final Log log = LogFactory.getLog(getClass());

    void foo() {
        log.info("foo");
    }
}
```

注意到实例变量 `log` 的获取方式是 `LogFactory.getLog(getClass())`，虽然也可以用 `LogFactory.getLog(Person.class)`，但是前一种方式有个非常大的好处，就是子类可以直接使用该 `log` 实例。例如：

```java
// 在子类中使用父类实例化的log:
public class Student extends Person {
    void bar() {
        log.info("bar");
    }
}
```

由于 Java 类的动态特性，子类获取的 `log` 字段实际上相当于 `LogFactory.getLog(Student.class)`，但却是从父类继承而来，并且无需改动代码。

此外，Commons Logging 的日志方法，例如 `info()`，除了标准的 `info(String)` 外，还提供了一个非常有用的重载方法：`info(String, Throwable)`，这使得记录异常更加简单：

```java
try {
   // ...
} catch (Exception e) {
    log.error("got exception!", e);
}
```

## 使用 Log4j

前面介绍了 Commons Logging ，可以作为“日志接口”来使用。而真正的“日志实现”可以使用 Log4j。

Log4j 是一种非常流行的日志框架。是一个组件化设计的日志系统，它的架构大致如下：

```
log.info("User signed in.");
 │
 │   ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
 ├──>│ Appender │───>│  Filter  │───>│  Layout  │───>│ Console  │
 │   └──────────┘    └──────────┘    └──────────┘    └──────────┘
 │
 │   ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
 ├──>│ Appender │───>│  Filter  │───>│  Layout  │───>│   File   │
 │   └──────────┘    └──────────┘    └──────────┘    └──────────┘
 │
 │   ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
 └──>│ Appender │───>│  Filter  │───>│  Layout  │───>│  Socket  │
     └──────────┘    └──────────┘    └──────────┘    └──────────┘
```

当我们使用 Log4j 输出一条日志时，Log4j 自动通过不同的 Appender 把同一条日志输出到不同的目的地。例如：

+ console：输出到屏幕；
+ file：输出到文件；
+ socket：通过网络输出到远程计算机；
+ jdbc：输出到数据库。

在输出日志的过程中，通过 `Filter` 来过滤哪些 log 需要被输出，哪些 log 不需要被输出。例如，仅输出 ERROR 级别的日志。

最后，通过 Layout 来格式化日志信息，例如，自动添加日期、时间、方法名称等信息。

上述结构虽然复杂，但我们在实际使用的时候，并不需要关心 Log4j 的 API，而是通过配置文件来配置它。

以 Maven项目 的 XML 配置为例，使用 Log4j 的时候，我们把一个 log4j2.xml 的文件放到 main/resourse 下就可以让 Log4j 读取配置文件并按照我们的配置来输出日志。下面是一个配置文件的例子：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Properties>
        <!-- 定义日志格式 -->
        <Property name="log.pattern">%d{MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36}%n%msg%n%n</Property>
        <!-- 定义文件名变量 -->
        <Property name="file.err.filename">log/err.log</Property>
        <Property name="file.err.pattern">log/err.%i.log.gz</Property>
    </Properties>
    <!-- 定义Appender，即目的地 -->
    <Appenders>
        <!-- 定义输出到屏幕 -->
        <Console name="console" target="SYSTEM_OUT">
            <!-- 日志格式引用上面定义的log.pattern -->
            <PatternLayout pattern="${log.pattern}" />
        </Console>
        <!-- 定义输出到文件,文件名引用上面定义的file.err.filename -->
        <RollingFile name="err" bufferedIO="true" fileName="${file.err.filename}" filePattern="${file.err.pattern}">
            <PatternLayout pattern="${log.pattern}" />
            <Policies>
                <!-- 根据文件大小自动切割日志 -->
                <SizeBasedTriggeringPolicy size="1 MB" />
            </Policies>
            <!-- 保留最近10份 -->
            <DefaultRolloverStrategy max="10" />
        </RollingFile>
    </Appenders>
    <Loggers>
        <Root level="info">
            <!-- 对info级别的日志，输出到console -->
            <AppenderRef ref="console" level="info" />
            <!-- 对error级别的日志，输出到err，即上面定义的RollingFile -->
            <AppenderRef ref="err" level="error" />
        </Root>
    </Loggers>
</Configuration>
```

虽然配置 Log4j 比较繁琐，但一旦配置完成，使用起来就非常方便。对上面的配置文件，凡是 INFO 级别的日志，会自动输出到屏幕，而 ERROR 级别的日志，不但会输出到屏幕，还会同时输出到文件。并且，一旦日志文件达到指定大小（1MB），Log4j 就会自动切割新的日志文件，并最多保留 10 份。

在 Maven 项目 pom.xml 中添加

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.14.1</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-jcl -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-jcl</artifactId>
        <version>2.14.1</version>
    </dependency>
</dependencies>
```

要打印日志，只需要按 Commons Logging 的写法写，不需要改动任何代码，就可以得到 Log4j 的日志输出，类似：

```
05-20 17:13:44.186 [main] INFO  com.halo.logging.Main
start...

05-20 17:13:44.191 [main] WARN  com.halo.logging.Main
end.
```

## 使用 SLF4J 和 Logback

前面介绍了 Commons Logging 和 Log4j 这一对，它们一个负责充当日志 API，一个负责实现日志底层，搭配使用非常便于开发。

其实 SLF4J 类似于Commons Logging，也是一个日志接口，而 Logback 类似于 Log4j，是一个日志的实现。

为什么有了 Commons Logging 和 Log4j，又会蹦出来 SLF4J 和 Logback？这是因为 Java 有着非常悠久的开源历史，不但 OpenJDK 本身是开源的，而且我们用到的第三方库，几乎全部都是开源的。开源生态丰富的一个特定就是，同一个功能，可以找到若干种互相竞争的开源库。

因为对 Commons Logging 的接口不满意，有人就搞了 SLF4J。因为对 Log4j 的性能不满意，有人就搞了 Logback。

我们先来看看 SLF4J 对 Commons Logging 的接口有何改进。在 Commons Logging 中，我们要打印日志，有时候得这么写：

```java
int score = 99;
p.setScore(score);
log.info("Set score " + score + " for Person " + p.getName() + " ok.");
```

拼字符串是一个非常麻烦的事情，所以 SLF4J 的日志接口改进成这样了：

```java
int score = 99;
p.setScore(score);
logger.info("Set score {} for Person {} ok.", score, p.getName());
```

我们靠猜也能猜出来，SLF4J 的日志接口传入的是一个带占位符的字符串，用后面的变量自动替换占位符，所以看起来更加自然。

如何使用 SLF4J ？它的接口实际上和 Commons Logging 几乎一模一样：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

class Main {
    final Logger logger = LoggerFactory.getLogger(getClass());
}
```

对比一下 Commons Logging 和 SLF4J 的接口，不同之处就是 `Log` 变成了 `Logger`，`LogFactory` 变成了 `LoggerFactory`。

在 Maven 项目中添加相关依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
</dependencies>
```

`logback-classic` 依赖包括了 `slf4j-api` 所以无需单独引用

在 Maven 项目中添加相关配置文件 logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <file>log/output.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
            <fileNamePattern>log/output.log.%i</fileNamePattern>
        </rollingPolicy>
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>1MB</MaxFileSize>
        </triggeringPolicy>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

运行即可获得类似如下的输出：

```
17:28:08.829 [main] INFO  com.halo.slf4j.Test - start...
17:28:08.832 [main] WARN  com.halo.slf4j.Test - end.
```

从目前的趋势来看，越来越多的开源项目从 Commons Logging 加 Log4j 转向了 SLF4J 加 Logback。