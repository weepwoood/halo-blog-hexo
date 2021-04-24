---
title: C语言学习笔记
tags:
  - C语言
categories:
  - [C语言]
date: 2020-05-25 23:06:36
---


参考《C Primer Plus》 第六版

---

## C语言概述

简单的C程序示例

```c
#include <stdio.h>
int main(void){
    int num;
    num = 1;
    
    printf("Hello World!\n");
    printf("My favorite number is %d because it is first.\n",num);
    
    return 0;
}
```

![image-20200525231404657]( http://img.whl123456.top/image/image-20200525231404657.png)

---

### `#include` 指令和头文件
```c
#include<stdio.h> //包含另一个文件
```

该行告诉编译器把 `stdio.h` 中的内容包含在当前程序中。

`stdio.h` 是 C 编译器软件包的标准部分，它提供键盘**输入**和屏幕**输出**的支持。

> stdio的全称是standard input output，h是head的缩写，.h是头文件的文件格式
>
> 在C++中`#include<stdio.h>`等价写法`#include<cstdio>`

作用相当于把 `stdio.h` 文件中的所有内容都输入该行所在的位置。

`#include` 这行代码是一条 C **预处理器指令**

### `main()`函数

```c
int main(void)  //主函数
```

C 程序包含一个或多个函数，它们是 C 程序的基本模块。

主函数是一个程序的入口位置，整个程序从主函数开始执行。一个程序最多只能有一个主函数。

圆括号表明 main()是一个函数名。int 表明 main()函数返回一个整数，void 表明 main()不带任何参数。

main 是一个极其普通的名称，但是这是唯一的选择

> C 程序一定从 main()函数开始执行（目前不必考虑例外的情况）。
>
> 除了 main()函数，你可以任意命名其他函数，而且 main()函数必须是开始的函数。

### 注释

在程序中，被`/*  */`两个符号括起来的部分是程序的注释。

C99 新增了另一种风格的注释，普遍用于 C++和 Java。这种新风格使用`//`符号创建注释，仅限于单行。

### 花括号、函数体和块

一般而言，所有的 C 函数都使用花括号`{}`标记函数体的开始和结束。

花括号还可用于把函数中的多条语句合并为一个单元或块。

### 声明

```c
int num;  //声明
```

以前的 C 语言，还要求把变量声明在块的顶部，其他语句不能在任何声明的前面。

C99 和 C11 遵循 C++的惯例，可以把声明放在块中的任何位置。尽管如此，首次使用变量之前一定要先声明它。

> 命名
>
> 可以用小写字母、大写字母、数字和下划线（_）来命名。
> 而且，名称的第 1 个字符必须是字符或下划线，不能是数字。
>
> 操作系统和 C 库经常使用以一个或两个下划线字符开始的标识符（如，_kcab），因此最好避免在自己的程序中使用这种名称。
>
> C 语言的名称区分大小写，即把一个字母的大写和小写视为两个不同的字符。

声明变量的 4 个理由：

+ 把所有的变量放在一处，方便读者查找和理解程序的用途。
+ 声明变量会促使你在编写程序之前做一些计划。
+ 声明变量有助于发现隐藏在程序中的小错误，如变量名拼写错误。
+ 如果事先未声明变量，C 程序将无法通过编译。

### 赋值

```c
num = 1;
```

在执行`int num;`声明时，编译器在计算机内存中为变量`num`预留了空间，然后在执行这行赋值表达式语句时，把值储存在之前预留的位置。

赋值表达式语句从右侧把值赋到左侧

### `printf()`函数

```c
printf("My favorite number is %d because it is first.\n", num);
```

圆括号表明 `printf` 是一个函数名。

实际参数（简称实参）是传递给函数的特定值，形式参数（简称形参）是函数中用于储存值的变量

`\n `组合代表一个换行符，换行符是一个转义序列（用于代表难以表示或无法输入的字符）

参数中的`%d `相当于是一个占位符，其作用是指明输出 `num` 值的位置，`%`提醒程序，要在该处打印一个变量，`d `表明把变量作为十进制整数打印

`printf()`函数名中的 `f` 提醒用户，这是一种格式化打印函数。

### `return` 语句

有返回值的 C 函数要有 `return` 语句

如果遗漏 `main()`函数中的 `return` 语句，程序在运行至最外面的右花括号（`}`）时会返回 `0`。

## 数据

有些数据类型在程序使用之前已经预先设定好了，在整个程序的运行过程中没有变化，这些称为常量（constant）

其他数据类型在程序运行期间可能会改变或被赋值，这些称为变量（variable）

![C语言数据类型关键字]( http://img.whl123456.top/image/image-20200526211617340.png)

+ 用 `int` 关键字来表示基本的整数类型

+ `long`、`short` 和 `unsigned`和 C90 新增的 `signed` 用于提供基本整数类型的变式，例如 `unsigned short int` 和 `long long int`。

+ `char` 关键字用于指定字母和其他字符（如，`#`、`$`、`%`和`*`）,`char` 类型也可以表示较小的整数

+ `float`、`double` 和 `long double` 表示带小数点的数
+ `_Bool `类型表示布尔值（`true` 或 `false`）
+ `_complex` 和`_Imaginary` 分别表示复数和虚数

> **位、字节和字**
>
> 最小的存储单元是位（bit），可以储存 0 或 1
>
> 字节（byte）是常用的计算机存储单位。对于几乎所有的机器，1 字节均为 8 位。
>
> 字（word）是设计计算机时给定的自然存储单位。对于 8 位的微型计算机（如，最初的苹果机），1 个字长只有 8 位。从那以后，个人计算机字长增至 16 位、32 位，直到目前的 64 位。

### 整数和浮点数

整数和数学的概念一样，在 C 语言中，整数是没有小数部分的数

浮点数与数学中实数的概念差不多。2.75、3.16E7、7.00 和 2e-8 都是浮点数。3.16E7 表示 3.16×10<sup>7</sup>

+ 整数没有小数部分，浮点数有小数部分。
+ 浮点数可以表示的范围比整数大。
+ 对于一些算术运算（如，两个很大的数相减），浮点数损失的精度更多。
+ 因为在任何区间内（如，1.0 到 2.0 之间）都存在无穷多个实数，所以计算机的浮点数不能表示区间内所有的值。浮点数通常只是实际值的近似值。例如，7.0 可能被储存为浮点值 6.99999。
+ 过去，浮点运算比整数运算慢。不过，现在许多 CPU 都包含浮点处理器，缩小了速度上的差距。

### C 语言基本数据类型

#### `int` 类型

`int` 类型是有符号整型，即 `int` 类型的值必须是整数，可以是正整数、负整数或零。

一般而言，储存一个 `int` 要占用一个机器字长

ISO C 规定 `int` 的取值范围最小为-32768～32767

一般而言，系统用一个特殊位的值表示有符号整数的正负号

##### 声明 `int` 变量

要声明多个变量，可以单独声明每个变量，也可在 int 后面列出多个变量名，变量名之间用逗号分隔。

```c
int erns; 
int hogs, cows, goats;
```

提供值方法：1. 赋值；2. 通过函数，如`scanf()`获得值；3. 初始化变量

初始化（initialize）变量就是为变量赋一个初始值。

```c
int hogs = 21; 
int cows = 32, goats = 14; 
int dogs, cats = 94; /* 有效，但是这种格式很糟糕 */
```

以上示例的最后一行，只初始化了 cats，并未初始化 dogs。这种写法很容易让人误认为 dogs 也被初始化为 94，所以最好不要把初始化的变量和未初始化的变量放在同一条声明中。

##### 打印 `int` 值

可以使用 `printf()`函数打印 `int` 类型的值

`%d`称为**转换说明**，它指定了 `printf()`应使用什么格式来显示一个值。格式化字符串中的每个`%d` 都与待打印变量列表中相应的 `int` 值匹配。

##### 八进制和十六进制

在 C 语言中，用特定的前缀表示使用哪种进制。

0x 或 0X 前缀表示十六进制值，所以十进制数 16 表示成十六进制是 0x10 或 0X10。

与此类似，0 前缀表示八进制。例如，十进制数 16 表示成八进制是 020。

以十进制显示数字，使用`%d`；以八进制显示数字，使用`%o`；以十六进制显示数字，使用`%x`。

另外，要显示各进制数的前缀0、0x 和 0X，必须分别使用`%#o`、`%#x`、`%#X`

##### 其他整数类型

C 语言提供 3 个附属关键字修饰基本整数类型：`short`、`long` 和 `unsigned`

+ `short int` 类型（或者简写为 `short`）占用的存储空间可能比 `int` 类型少，常用于较小数值的场合以节省空间。与 `int` 类似，`short` 是有符号类型。

+ `long int` 或 `long` 占用的存储空间可能比 `int` 多，适用于较大数值的场合。与 `int` 类似，`long`是有符号类型。

+ `long long int` 或 `long long`（C99 标准加入）占用的储存空间可能比 `long` 多，适用于更大数值的场合。该类型**至少占 64 位**。与 `int` 类似，`long long` 是有符号类型。

+ `unsigned int` 或 `unsigned` 只用于**非负值**的场合。这种类型与有符号类型表示的范围不同。例如，16 位 `unsigned int` 允许的取值范围是 0～65535，而不是-32768～32767。用于表示正负号的位现在用于表示另一个二进制位，所以无符号整型可以表示更大的数。

+ 在 C90 标准中，添加了 `unsigned long int` 或 `unsigned long` 和 `unsigned int` 或 `unsigned short` 类型。C99 标准又添加了 `unsigned long long int` 或 `unsigned long long`。

+ 在任何有符号类型前面添加关键字 `signed`，可强调使用有符号类型的意图。例如，`short`、`short int`、`signed short`、`signed short int` 都表示同一种类型。

`int` 类型那么多，应该如何选择？

首先，考虑 `unsigned` 类型。这种类型的数常用于计数，因为计数不用负数。而且，`unsigned` 类型可以表示更大的正数。 

如果一个数超出了 `int` 类型的取值范围，且在 `long` 类型的取值范围内时，使用 `long` 类型。

然而，对于那些 `long` 占用的空间比 `int` 大的系统，使用 `long` 类型会减慢运算速度。因此，如非必要，请不要使用 `long` 类型。另外要注意一点：如果在 `long` 类型和 `int` 类型占用空间相同的机器上编写代码，当确实需要 32 位的整数时，应使用 `long` 类型而不是 `int` 类型，以便把程序移植到 16 位机后仍然可以正常工作。类似地，如果确实需要 64 位的整数，应使用 `long long` 类型。 

如果在 `int` 设置为 32 位的系统中要使用 16 位的值，应使用 `short` 类型以节省存储空间。通常，只有当程序使用相对于系统可用内存较大的整型数组时，才需要重点考虑节省空间的问题。使用 `short` 类型的另一个原因是，计算机中某些组件使用的硬件寄存器是 16 位。

#### `char`类型

`char` 类型用于储存字符（如，字母或标点符号），但是从技术层面看，`char` 是整数类型。

##### 声明 `char` 类型变量

```c
char response; 
char itable, latan;
```

##### 字符常量和初始化

在 C 语言中，用单引号括起来的单个字符被称为字符常量（character constant）

```c
char broiled;  /* 声明一个 char 类型的变量 */ 
broiled = 'T'; /* 为其赋值，正确 */ 
broiled = T;  /* 错误！此时 T 是一个变量 */ 
broiled = "T"; /* 错误！此时"T"是一个字符串 */
char grade = 65; /* 对于 ASCII，这样做没问题，但这是一种不好的编程风格 */
```

##### 符号常量和 const常量

符号常量通俗地讲就是“替换”，即用一个标识符来替代常量，又称为“宏定义”或者“宏替换”。

```c
#define pi 3.14
```

另一种定义常量的方法是使用 `const`

```
const double pi = 3.14;
```

于是在程序中凡是使用pi的地方将在程序执行前全部自动替换为3.14。

```c
#include<stdio.h>
#define pi 3.14
//const double pi = 3.14;
int main() {
	double r = 3;
	printf("%f\n", pi * r * r);
	return 0;
}
```



##### 非打印字符

1. 使用 ASCII 码
2. 用特殊的符号序列表示一些特殊的字符。这些符号序列叫作转义序列（escape sequence）

![转义序列]( http://img.whl123456.top/image/image-20200526215203116.png)

#### `_Bool` 类型

C99 标准添加了`_Bool` 类型，用于表示布尔值，即逻辑值 true 和 false。因为 C 语言用值 1 表示
true，值 0 表示 false，所以`_Bool` 类型实际上也是一种整数类型。但原则上它仅占用 1 位存储空间，
因为对 0 和 1 而言，1 位的存储空间足够了。

#### 可移植类型：`stdint.h` 和 `inttypes.h`

C 语言提供了许多有用的整数类型。但是，某些类型名在不同系统中的功能不一样。C99 新增了两个
头文件 `stdint.h` 和 `inttypes.h`，以确保 C 语言的类型在各系统中的功能相同。

#### `float`、`double` 和 `long double`

C 标准规定，`float` 类型必须至少能表示 6 位有效数字，且取值范围至少是 10<sup>-37</sup> \~10<sup>+37</sup>。

`double` 类型和 `float` 类型的最小取值范围相同，但至少必须能表示 10 位有效数字。一般情况下，`double` 占用 64 位而不是 32 位。

`long double`，以满足比 `double` 类型更高的精度要求。不过，C 只保证`long double` 类型至少与 `double` 类型的精度相同。

#### 类型大小

```c
//* typesize.c -- 打印类型大小 */ 
#include <stdio.h> 
int main(void) 
{ 
     /* C99 为类型大小提供%zd 转换说明 */ 
     printf("Type int has a size of %zd bytes.\n", sizeof(int)); 
     printf("Type char has a size of %zd bytes.\n", sizeof(char)); 
     printf("Type long has a size of %zd bytes.\n", sizeof(long)); 
     printf("Type long long has a size of %zd bytes.\n", 
               sizeof(long long)); 
     printf("Type double has a size of %zd bytes.\n", 
              sizeof(double)); 
     printf("Type long double has a size of %zd bytes.\n", 
              sizeof(long double)); 
     return 0; 
}
```

`sizeof` 是 C 语言的内置运算符，以字节为单位给出指定类型的大小。

C99 和 C11 提供`%zd` 转换说明匹配 `sizeof` 的返回类型1。一些不支持 C99 和 C11 的编译器可用`%u` 或`%lu` 代替`%zd`。

该程序的输出如下： 

```
Type int has a size of 4 bytes. 
Type char has a size of 1 bytes. 
Type long has a size of 8 bytes. 
Type long long has a size of 8 bytes. 
Type double has a size of 8 bytes. 
Type long double has a size of 16 bytes.
```

C 语言定义了 `char` 类型是 1 字节，所以 `char` 类型的大小一定是 1 字节。

而在 `char` 类型为 16 位、`double`类型为 64 位的系统中，`sizeof` 给出的 `double` 是 4 字节。

### 强制类型转换

```c
#include<stdio.h>
int main(){
	double r = 12.56;
	int a = 3, b = 5;
	printf("%d\n", (int)r); //r强制转换成int
	printf("%d\n", a/b);
	printf("%.1f", (double)a / (double)b);//%.1f保留一位小数输出
	return 0;
}
```

## 字符串和格式化输入/输出

### 字符串简介

字符串（character string）是一个或多个字符的序列。

双引号仅告知编译器它括起来的是字符串，正如单引号用于标识单个字符一样。

### `char` 类型数组和 `null` 字符

C 语言没有专门用于储存字符串的变量类型，字符串都被储存在 `char` 类型的数组中。数组由连续的存
储单元组成，字符串中的字符被储存在相邻的存储单元中，每个单元储存一个字符。

数组末尾位置的字符`\0`。这是空字符（null character），C 语言用它标记字符串的结束。空字符不是数字 0，它是非打印字符，其 ASCII 码值是（或等价于）0。

### `strlen()`函数

`strlen()`函数给出字符串中的字符长度。

```c
/* praise2.c */ 
// 如果编译器不识别%zd，尝试换成%u 或%lu。 
#include <stdio.h> 
#include <string.h>      /* 提供 strlen()函数的原型 */ 
#define PRAISE "You are an extraordinary being." 
int main(void) 
{ 
     char name[40]; 
 
     printf("What's your name? "); 
     scanf("%s", name); 
     printf("Hello, %s. %s\n", name, PRAISE); 
     printf("Your name of %zd letters occupies %zd memory cells.\n", 
               strlen(name), sizeof name); 
     printf("The phrase of praise has %zd letters ", 
               strlen(PRAISE)); 
     printf("and occupies %zd memory cells.\n", sizeof PRAISE); 
 
     return 0; 
}
//What's your name? Serendipity Chance 
//Hello, Serendipity. You are an extraordinary being. 
//Your name of 11 letters occupies 40 memory cells. 
//The phrase of praise has 31 letters and occupies 32 memory cells.
```

> 一般而言，C 把函数库中相关的函数归为一类，并为每类函数提供一个头文件。例如，`printf()`和`scanf()`都隶属标准输入和输出函数，使用 `stdio.h` 头文件。`string.h` 头文件中包含了 `strlen()`函数和其他一些与字符串相关的函数（如拷贝字符串的函数和字符串查找函数）。

### 常量和 C 预处理器



### `printf()`和 `scanf()`

![转换说明及其打印的输出结果]( http://img.whl123456.top/image/image-20200528222920536.png)

## 运算符、表达式和语句

### 基本运算符



### 其他运算符



### 表达式和语句



### 类型转换



### 带参数的函数



## 循环

### `while`循环



### `for` 循环



### 出口条件循环：`do while` 



## 分支和跳转 

### `if` 语句



### `if else` 语句



### 逻辑运算符



### 条件运算符



### `continue` 和 `break`



### 多重选择：`switch` 和 `break` 



### `goto` 语句



## 字符输入/输出和输入验证

### 单字符 I/O：`getchar()`和 `putchar()`



### 缓冲区



### 结束键盘输入



### 重定向和文件

## 函数

### 常用math函数

需要在程序开头加上`math.h`头文件

#### `fabs(double x)`

该函数用于对 `double`型变量取绝对值

```c
#include<stdio.h>
#include<math.h>
int main() {
	double db = -12.56;
	printf("%.2f\n", fabs(db)); //输出：12.56
	return 0;
}
```

#### `floor（double x）`和 `ceil（double x）`

分别用于 `double`型变量的向下取整和向上取整

```c
#include<stdio.h>
#include<math.h>
int main() {
	double db1 = -12.56;
	double db2 = 12.56;
	printf("%.0f %.0f\n", floor(db1),ceil(db1)); //输出：-13 -12
	printf("%.0f %.0f\n", floor(db2),ceil(db2)); //输出：12 13
	return 0;
}
```

#### `pow(double r,double p)`

返回r的p次幂的值

#### `sqrt（double x）`

该函数用于返回 double型变量的算术平方根

#### `log（double x）`

该函数用于返回 double型变量的以自然对数为底的对数

#### `sin（ double x）、cos（ double x）和tan（ double x）`

#### `asin（double x）， acos（double x）F atan （double x)`

#### `round（double x）`





## 数组和指针

### 数组



### 多维数组



### 指针和数组



### 函数、数组和指针



### 指针操作



## 字符串和字符串函数





## 枚举

```c
enum color {RED,YELLO,GREEN};//RED = 0,YELLO = 1,GREEN = 3...
// enum 枚举类型名字 {名字0,…,名字n};
// 枚举类型名字可忽略
void f(enum color c);
```

用枚举而不是定义独立的const in变量

常量符号，它们的类型只能是int

### 技巧：自动计数的枚举

```c
#include<stdio.h>
enum COLOR {RED, YELLOW, GREEN, NumCOLORS};

int main(int argc, char const *argv[])
{
    
}
```

枚举中各常量值依次递加，故最后一个值可代表常量个数

### 指定枚举量的值

```c
enum COLOR {RED=1, YELLOW, GREEN=5, NumCOLORS};//YELLOW = 2,NumCOLORS?
```



## 存储类别、链接和内存管理



## 文件输入/输出



## 结构和其他数据形式

表达数据比较复杂，但需要一个整体来表达，使用结构——一个复合的数据类型

### 声明结构类型

```c
#include<stdio.h>

struct date{
    int month;
    int day;
    int year;
};//注意分号

int main(int argc,char const *argv[])
{
    struct data today;
    
    today.month = 06;
    today.day = 01;
    today.year = 2020;
    
    printf("Today's date is %i-%i-%i.\n",
           today.year,today.month,today.day);
    
    return 0;
}
```

另两种声明结构的形式

```c
struct{
    int x;
    int y;
}p1,p2;//p和p2都是一种无名结构，里面有x和y
```

```c
struct ponit{
    int x;
    int y;
}p1,p2;//p和p2都是ponit的结构，里面有x和y
```

### 结构的初始化

两种方式

```c
struct date{
    int month;
    int day;
    int year;
};
struct date today = {07,31,2014};
struct date thismonth = {.month=7, .year=2014};//此时day为0
```

### 结构成员

数组的单元必须是相同类型，结构的成员可以是不同类型

访问成员

```c
#include<stdio.h>
int main(int argc,char const *argv[])
{
    struct date{
        int month;
        int day;
        int year;
    };
    struct date today = {07,31,2014};
    
    printf(today.day);
    
    return 0;
}
```

### 结构运算

要访问整个结构，直接用结构变量的名字

对于整个结构，可以做赋值、取地址，也可以传递给函数参数

```c
p1 = (struct ponit){5,10};//相当于p1.x=5;p1.y=10;
p1=p2;//相当于p1.x=p2.x; p1.y=p2.y
```

### 结构指针

结构变量的名字并不是结构变量的地址，必须使用&运算符

```c
struct date today;
struct date *pDate = &today;
```

### 结构与函数

#### 结构作为函数的参数

```c
int numberOfDays(struct date d){

}
```

+ 整个结构可以作为参数的值传入函数
+ 在函数内**新建**一个结构变量，并**复制**调用者的结构的值
+ 也可以返回一个结构

#### 结构指针作为参数

> If a large structure is to be passed to a function, it is generally more efficient to pass a pointer than to copy the whole structure. ——K&R

```c
struct date {
    int month;
    int day;
    int year;
} myday;

struct date *p = &myday;
(*p).month = 12;//方法一
p->month = 12;//方法二
```

用`->`表示指针所指的结构变量中的成员

#### 结构作为函数的返回值

```c
struct point {
    int x;
    int y;
}

struct point getstruct1(void)
{
    struct ponit p;
    scanf_s("%d",&p.x);
    scanf_s("%d",&p.y);
    return p;
}

struct point* getstruct2(struct ponit *p)
{
    struct ponit p;
    scanf_s("%d",&p->x);
    scanf_s("%d",&p->y);
    return p;
}
```

## 位操作

## C 预处理器和 C 库

## 高级数据表示
