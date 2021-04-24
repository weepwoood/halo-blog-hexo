---
title: '二级Python基础'
date: 2019-09-03 13:17:39
tags:
- Pyhton
- 计算机二级
categories:
---

参考[Xanxus-5/NCRE-Python](https://github.com/Xanxus-5/NCRE-Python)

---

![](https://puui.qpic.cn/fans_admin/0/3_1409075683_1572058823439/0)

## 程序设计基本方法

### 程序设计语言概述

+ 程序设计语言是计算机能够理解和识别用户操作意图的一种交互体系，它按照特定规则组织计算机指令，使计算机能够自动进行各种运算处理。
+ 按照程序设计语言规则组织起来的一组计算机指令称为计算机程序.
+ 高级编程语言根据执行机制不同可分成两类：**静态语言**和**脚本语言**，静态语言采用编译方式执行，脚本语言采用解释方式执行。例如，C语言是静 态语言，Python语言是脚本语言。
+ 采用**编译执行的编程语言是静态语言**，如C语言、Java语言；**采用解释执行的编程语言是脚本语言**，如JavaScript语言、PHP语言
  + **编译**是将源代码转换成目标代码的过程，通常，源代码是高级语言代码，目标代码是机器语言代码，执行编译的计算机程序称为编译器
  
    > 对于相同源代码，编译所产生的目标代码执行速度更快。
    >
    > 目标代码不需要编译器就可以运行，在同类型操作系统上使用灵活。
  + **解释**是将源代码逐条转换成目标代码同时逐条运行目标代码的过程。执行解释的计算机程序称为解释器。
  
    > 解释执行需要保留源代码，程序纠错和维护十分方便。
    >
    > 只要存在解释器，源代码可以在任何操作系统上运行，可移植性好

### 计算机编程

计算思维是区别于以数学为代表的逻辑思维和以物理为代表的实证思维的第三种思维模式。

编程是一个求解问题的过程

+ 首先需要分析问题，抽象内容之间的交互关系
+ 设计利用计算机求解问题的确定性方法，
+ 进而通过编写和调试代码解决问题

这是从抽象问题到解决问题的完整过程。

### 程序的基本编写方法

IPO程序编写方法

+ 输入数据

  输入（Input）是一个程序的开始。程序要处理的数据有多种来源，形成了多种输入方式，包括：文件输入、网络输入、控制台输入、交互界面输出、随机数据输入、内部参数输入等。

+ 处理数据

  处理（Process）是程序对输入数据进行计算产生输出结果的过程。计算问题的处理方法统称为“算法”，它是程序最重要的组成部分。可以说，算法是一个程序的灵魂。

+ 输出数据

  输出（Output）是程序展示运算成果的方式。程序的输出方式包括：控制台输出、图形输出、文件输出、网络输出、操作系统内部变量输出等。

### Python语言概述

Python重要特点：

1. 具有通用性；

   Python语言可以用于几乎任何与程序设计相关应用的开发，不仅适合训练变成思维，更适合诸如数据分析、机器学习、人工智能、Web开发等具体的技术领域。

2. 语法简洁；

   只有33个保留字

3. 生态高产；

   Python解释器提供了几百个内置类和函数库，此外，世界各地程序员通过开源社区贡献了十几万个第三方函数库，几乎覆盖了计算机技术的各个领域，编写Python程序可以大量利用已有内置或第三方代码，具备良好的编程生态。

具体特点：

1. 平台无关；

   Python程序可以在任何安装解释器的计算机环境中执行，因此，可以不经修改地实现跨操作系统运行。

2. 强制可续；

   Python通过强制缩进（类似文章段落的首行空格）来体现语句间的逻辑关系，显著提高了程序的可读性，进而增强了Python程序的可维护性。

3. 支持中文；

4. 格式多样；

5. 类库便捷；

6. 开源理念；

### 实例

```python
#斐波那契数列(F(n) = F(n-2) + F(n-1)）, n >= 2)
a, b = 0, 1
while a < 1000 :
    print(a, end = ',')
a, b = b, a + b
```

```python
#计算圆半径
r = 25
area = 3.1415 * r * r
print(area)
print('{:.2f}'.format(area)) #两位小数
```

```python
#绘制五角星
from turtle import *
color('red', 'red')
begin_fill()
for i in range(5):
    fd(200)
    rt(144)
end_fill()
done()
```

```python
#程序运行计时
import time
limit = 10 * 1000 * 1000
start = time.perf_counter()
while True:
    limit -= 1
    if limit <= 0:
        break
delta = time.perf.counter() - start
print('时间是:{}秒'.format(delta))
```

```python
#绘制七种圆圈
import turtle
colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'purple']
for i in range(7):
    c = color[i]
    turtle.color(c.c)
    turtle.begin_fill()
    turtle.rt(360/7)
    turtle.circle(50)
    turtle.end_fill()
turtle.done()
```

## Python基本语法元素

### 语法元素的名称

**缩进**：表示程序的逻辑。指每行语句开始前的空白局域，用来表示Python程序之间的**包含和层次关系**。1个缩进 = 4个空格。缩进是Python语言中**表明程序框架的唯一手段**。

**注释**：注释是代码中的辅助性文字，**会被编译或解释器略去，不被计算机执行**，一般用于程序员对代码的说明。Python语言采用#表示一行注释的开始，多行注释需要在每行开始都使用#。

**变量**：可以随时命名、赋值、使用。

**命名**：Python语言允许采用大写字母、小写字母、数字、下划线(_)和汉字等字符及其组合给变量命名，但名字的首字符不能是数字，中间不能出现空格，长度没有限制，标识符对大小写敏感。

保留字（keyword）：

|   and   |  as   | assert | break  |  class   | continue |
| :-----: | :---: | :----: | :----: | :------: | :------: |
|   def   |  del  |  elif  |  else  |  except  |  False   |
| finally |  for  |  from  | global |    if    |  import  |
|   in    |  is   | lambda |  None  | nonlocal |   not    |
|   or    | pass  | return |  True  |   try    |  while   |
|  with   | yield |        |        |          |          |

### 数据类型

Python语言支持多种数据类型，最简单的包括数字类型、字符串类型，略微复杂的包括元组类型、集合类型、列表类型、字典类型等。

**数字类型**：整数、浮点数、复数

一个整数值可以表示为十进制、十六进制、八进制和二进制等不同进制形式。

+ 二进制： 0b001111110010
+ 八进制： 0o1762
+ 十进制： 1010
+ 十六进制：0x3F2

一个浮点数可以表示为带有小数点的一般形式，也可以采用科学计数法表示。例如：浮点数123.456，两种表示方式如下：

+ 一般形式： 123.456
+ 科学计数法： 1.23456e2

复数类型与数学中的复数相一致，采用a+bj的形式表示，存在实部和虚部。

**字符串**：双引号与单引号作用相同

**切片**：[N : M]格式获取字符串的子串（从N到M但不包含M）。

![1568883436400](计算机二级Python基础/1568883436400.png)

**如果字符串长度为L，正向递增需要以最左侧字符序号为0，向右依次递增，最右侧字符序号为L-1；**

**反向递减序号以最右侧字符序号为-1，向左依次递减，最左侧字符序号为-L。**

可以通过Python默认提供的`len()`函数获取字符串的长度，一个中文字符和西文字符的长度都记为1

### 程序的语句元素

**表达式**:产生或计算新数据值的代码片段称为表达式。

**赋值语句**：<变量> = <表达式>

```python
# 变量x和y交换
t = x
x = y
y = t
# 等价于
x, y = y, x
```

**引用**：

python程序会经常使用当前程序之外已有的功能代码，这个过程叫“引用”。

```python
import <库名称>
# 调用具体功能
<库名称>.<函数名称>()
```

### 基本输入输出函数

`input()`:获得用户输入

`<变量> = input("<提示性文字>")`

```python
a = input('请输入：')
# >请输入: X
print(a)
# >X
```

`eval()`：去掉字符串最外侧的引号。

以Python表达式的方式解析并执行字符串，将返回结果输出

`<变量> = eval("<字符串>")`

`print()`：用于输出运算结果

1. 仅输出：`print('<字符串>')`；

2. 仅输出一个或多个变量：`print(<变量1>, <变量2>, ..., <变量n>)`；

3. 混合：`print(<输出字符串模板>.format(<变量1>, <变量2>..., <变量n>)`。例：

   ```python
   a, b = 3, 66
   print('数字{}和数字{}的和是{}'.format(a, b, a+b))
   # >数字3和数字66的和是69
   ```

4. 默认会在最后增加一个换行，或用：`print('<内容>', end = '<结尾>')`

### 实例解析--倒背如流

```python
# 将输入字符反向输出
# 方法一
# 采用正向递增序号，利用len()函数将i设为最后一个字符的索引序号，然后逐次输出至第一个字符。
s = input("请输入一段文本：")
i = len(s) - 1
while i >= 0 :
    print(s[i], end="")
    i = i - 1
```

```python
# 方法二
# 采用反向递减序号，将i首先设置为-1，直接索引最后一个字符，然后逐次输出至第一个字符。
s = input("请输入一段文本：")
i = -1
while i >= -1 * len(s):
    print(s[i], end="")
    i = i - 1
```

## ⭐基本数据类型

### ⭐整数类型

|   种类   | 引导符号 |                         描述                        |
|  :------:| :------: | :-------------------------------------------- ---: |
|  十进制  |    无    |               默认情况，例：1010,-1010               |
|  二进制  | 0b 或 0B |          由字符0和1组成，例：0b1010，0B1010           |
|  八进制  | 0o 或 0O |          由字符0到7组成，例：0o1010，0O1010           |
| 十六进制 | 0x 或 0X |由字符0到9、a 到 f 或 A 到 F 组成，例：0x1010，0X1010  |

不同进制的整数可以**直接运算**

### 浮点数类型

必须带有小数部分，小数可以是0。

除十进制外，浮点数没有其他进制表示形式

科学计算法使用字母 e 或 E 作为幂的符号，以10为基数

不确定尾数：两个浮点数运算，有一定概率在运算结果后增加一些不确定尾数

`round()`函数：对字符串四舍五入。

`round(1.2345, 2) = 1.23`

### 复数类型

基本单位元素 j 或 J ,叫做“虚数单位”。含有虚数单位的数被称为复数

复数可以看作是二元有序实数对（a, b），表示 a + bj，a 是实数部分，b 是虚数部分。

**当 b 是1时，1不能省略，即'1j'表示复数**而单独j则表示Python程序中的一个变量。

对于复数 z，可以用 `z.real` 和 `z.imag`分别获得它的实部和虚部

```python
print((1.23e4 + 5.67e4j).real ) # 12300.0
print((1.23e4 + 5.67e4j).imag)  # 56700.0
print(1.23e4 + 5.67e4j.imag)    #先获得5.67e4j的虚部，再与1.23e4进行求和计算 69000.0
```

### 数字类型的运算

#### 运算操作符

| 操作符 |                   描述                   |
| :----: | :--------------------------------------: |
| x + y  |                    和                    |
| x - y  |                    差                    |
| x * y  |                    积                    |
| x / y  |           商，产生结果为浮点数             |
| x // y | 整数商，即：不大于 x 与 y 之商的最打整数    |
| x % y  |           商的余数，称为模运算             |
|   -x   |            负值，即 x * (-1)              |
|   +x   |                  x本身                   |
| x ** y |               x 的 y 次幂                |

基本规则：

+ 整数和浮点数混合运算，输出结果是浮点数；

+ 整数之间运算，产生结果类型与操作符相关，`/` 的结果是浮点数；

+ 整数或浮点数与复数运算，输出结果是复数；

**所有**二元运算操作符都可以与赋值符号（=）相连，形成**增强赋值操作符**（+=，-=等）。用 'op' 表示运算操作符，增强赋值操作符的用法如下：

x op= y 等价于 x = x op y，op 与 = 之间不用加空格

#### 数值运算函数

|          函数     |                        描述                      |
| :--------------------: | :-----------------------------------------: |
|        `abs(x)`        |                      x的绝对值               |
|     `divmod(x, y)`     |(x // y, x % y)，输出为二元组形式（也称为元组类型）|
|    `pow(x, y[, z])`    |          x**y[(x **y) % z]，幂运算     |
|    `round(x[, d])`     |            对 x 四舍五入，保留 d 位小数        |
| `max(a, b, c, d, ...)` |                       最大值                 |
| `min(a, b, c, d, ...)` |                       最小值                 |

### 字符串类型及格式化

单行字符串可以由一对单引号或双引号作为边界表示
多行字符串可以由一对三单引号或三双引号作为边界表示

反斜杠字符（`\`）表示“转义”，即该字符与后面相邻的一个字符共同组成了新的含义。

+ `\n`：换行；
+ `\\`：反斜杠
+ `\'`表示单引号
+ `\"`表示双引号
+ `\t`：制表符

**`\` 的额外作用：续行**

#### `format()`方法的基本使用

字符串`format()`方法的基本使用格式是：`<模板字符串>.format(<逗号分隔的参数>)`

模板字符串是一个由字符串和槽组成的字符，用来控制字符串和变量的显示效果

`{<参数符号>:<格式控制标记>}`

`:`引导符号

格式控制标记包括：`<填充>`、`<对齐>`、`<宽度>`、`<.(精度)>`、`英文逗号`、`<类型>`6个字段，这些字段都是可选的，可以组合使用

`<填充>`、`<对齐>`和`<宽度>`主要用于对显示格式的规范。

+ `<填充>`:可以修改默认填充字符，填充字符只能有一个。
+ `<对齐>`:`<` 左对齐；`>`右对齐；`^` 居中
+ `<宽度>`:槽的设定输出宽度
  宽度指当前槽的设定输出字符宽度，如果该槽参数实际值比宽度设定值大，则使用参数实际长度。如果该值的实际位数小于指定宽度，则按照对齐指定方式在宽度内对齐，默认以空格字符补充。

+ `英文逗号`:数字的千位分隔符，适用于整数和浮点数
  `<.精度>` `<类型>`主要用于对数值本身的规范
+ `<.(精度)>`:浮点数小数部分的精度或字符串的最大输出长度
+ `<类型>`:整数类型：b, c, d, o, x, X；浮点数类型：e, E, f, %

整数类型：

+ b：输出整数的二进制方式；
+ c：输出整数对应的Unicode字符；
+ d：输出整数的十进制方式；
+ o：输出整数的八进制方式；
+ x：输出整数的小写十六进制方式；
+ X：输出整数的大写十六进制方式

浮点类型：

+ e：输出浮点数对应的小写字母e的指数形式；
+ E：输出浮点数对应的大写字母E的指数形式
+ f：输出浮点数的标准浮点形式；
+ %：输出浮点数的百分形式

```python
print('{:.2f}'.format(3.14159)) #输出小数后2位
# '3.14'
print('{:X}'.format(1010)) #输出十六进制形式
# '3F2'
print('{:.5}'.format('这是一个很长的字符串')) #输出前5位
# '这是一个很'
print('{:-^10}'.format('PYTHON')) #居中并以 - 填充
# '--PYTHON--'
```

### 字符串类型的操作

#### 字符串操作符

|     操作符     |                   描述                     |
| :------------: | :---------------------------------------: |
|     x + y      |           连接两个字符串 x 与 y            |
| x \*n 或 n\* x |             复制 n 次字符串 x              |
|     x in s     | 如果 x 是 s 的字符串，返回True，否则False   |

#### 字符串处理函数

|   函数   |                        描述                        |
| :------: | :----------------------------------------------:  |
| `len(x)` | 返回 x 的长度，也可以返回其他组合数据类型的元素个数   |
| `str(x)` |             返回任意类型 x 的字符串形式             |
| `chr(x)` |          返回 Unicode 编码 x 对应的单字符           |
| `ord(x)` |           返回单字符 x 表示的 Unicod 编码           |
| `hex(x)` |       返回整数 x 对应十六进制的小写形式字符串        |
| `oct(x)` |        返回整数 x 对应八进制的小写形式字符串         |

#### 字符串处理方法

|             方法              |                            描述                        |
| :---------------------------: | :---------------------------------------------------------:|
|        `str.lower(x)`         |                      返回 str 全部小写                      |
|        `str.upper(x)`         |                      返回 str 全部大写                      |
|    `str.split(sep = None)`    |    返回由 str 根据 sep 被分割构成的列表，默认以空格分割     |
|       `str.count(sub)`        |                     返回 sub 出现的次数                     |
|    `str.replace(old, new)`    |                返回 old 被替换为 new 的 str                 |
| `str.center(width, fillchar)` | 字符串居中函数，fillchar 可选，两侧新增字符采用fillchar填充 |
|      `str.strip(chars)`       |           从 str 中去掉其左右 chars 中列出的字符            |
|       `str.join(iter)`        |           将 iter 变量的每一个元素后增加一个 str            |

例：

```python
print('Python is an excellent language.'.replace('a', '#'))
# 'Python is #n excellent l#ngu#ge.'
print('Python'.center(20, '='))
# '===Python==='
print('Python'.center(2, '='))
# 'Python' #当 width 小于 str 长度，返回 str
print('   ==Python==   '.strip(' =n'))
# 'Pytho'
print(','.join('12345'))
# '1,2,3,4,5'
```

```python
def main():
    str1 = 'hello, world!'
    str2 = 'HeLLo, wOrld!'
    # 通过len函数计算字符串的长度
    print(len(str1))  # 13
    # 获得字符串首字母大写的copy
    print(str1.capitalize())  # Hello, world!
    # 获得字符串变大写后的copy
    print(str1.title())  # Hello, World!
    print(str2.title())  # Hello, World!
    # 获得以首字母大写的方式显示每个单词后的copy
    print(str1.upper())  # HELLO, WORLD!
    print(str2.lower())  # hello, world!

if __name__ == '__main__':
    main()
```

```python
def main():
    str1 = 'hello, world!'
    # 从字符串中查找子串所在位置
    print(str1.find('or'))  # 8
    print(str1.find('shit'))  # -1
    # 与find类似但找不到子串时会引发异常
    print(str1.index('or')) # 8
    print(str1.index('shit')) # ValueError: substring not found
    # 检查字符串是否以指定的字符串开头
    print(str1.startswith('He'))  # False
    print(str1.startswith('hel'))  # True
    # 检查字符串是否以指定的字符串结尾
    print(str1.endswith('!'))  # True

if __name__ == '__main__':
    main()
```

```python
def main():
    str1 = 'hello, world!'
    # 将字符串以指定的宽度居中并在两侧填充指定的字符
    print(str1.center(50, '*'))
    # 将字符串以指定的宽度靠右放置左侧填充指定的字符
    print(str1.rjust(50, ' '))
    str3 = 'abc123456'
    # 从字符串中取出指定位置的字符(下标运算)
    print(str3[2])  # c
    # 字符串切片(从指定的开始索引到指定的结束索引)
    print(str3[2:5])  # c12
    print(str3[2:])  # c123456
    print(str3[2::2])  # c246
    print(str3[::2])  # ac246
    print(str3[::-1])  # 654321cba
    print(str3[-3:-1])  # 45
    # 检查字符串是否由数字构成
    print(str3.isdigit())  # False
    # 检查字符串是否以字母构成
    print(str3.isalpha())  # False
    # 检查字符串是否以数字和字母构成
    print(str3.isalnum())  # True
    str4 = '  hello, worl d   '
    print(str4)
    # 获得字符串修剪空格的拷贝
    print(str4.strip())  # 'hello, worl d'
    print(str4.rstrip()) # '  hello, worl d   '
    print(str4.lstrip()) # 'hello, worl d   '


if __name__ == '__main__':
    main()
```

#### 类型判断和类型间转换

`type(x)`：对 x 进行类型判断，适用于任何数据类型

```python
n = eval(input('请输入一个数字：'))
if type(n) == type(123):
    print('输入的是整数')
elif type(n) == type(11.3):
    print('输入的是浮点数')
else :
    print('无法判断')
```

`int(x)`：将 x 转为整数

`float(x)`：将 x 转为浮点数

`str(x)`：将 x 转为字符串

### 实例解析--凯撒密码

恺撒撒密码是古罗马恺撒大帝用来对军事情报进行加密的算法，它采用了替换方法对信息中的每一个英文字符循环替换为字母表序列该字符后面第三个字符：
原文：A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
密文：D E F G H I J K L M N O P Q R S T U V W X Y Z A B C

原文字符P，其密文字符C满足如下条件：C = ( P + 3 ) mod 26

解密方法反之，满足：P = ( C – 3 ) mod 26

```python
# 恺撒密码-加密
ptxt = input("请输入明文文本: ")
for p in ptxt:
    if "a" <= p <= "z":
        print(chr(ord("a")+(ord(p)-ord("a")+3)%26), end='')
    elif "A" <= p <= "Z":
            print(chr(ord("A")+(ord(p)-ord("A")+3)%26), end='')
    else:
            print(p, end='')
```

```python
# 恺撒密码-解密
etxt = input("请输入加密后文本: ")
for p in etxt:
    if "a" <= p <= "z":
        print(chr(ord("a")+(ord(p)-ord("a")-3)%26), end='')
    elif "A" <= p <= "Z":
            print(chr(ord("A")+(ord(p)-ord("A")-3)%26), end='')
    else:
            print(p, end='')
```

## 程序的控制结构

### 程序流程图

描述一个计算问题的程序过程有多种方式：IPO、流程图、伪代码和程序代码。

+ 起止框：表示程序逻辑的开始或结束；
+ 判断框：表示一个判断条件，并根据判断结果选择不同的执行路径；
+ 处理框：表示一组处理过程，对应于顺序执行的程序逻辑；
+ 输入输出框：表示程序中的数据输入或结果输出；
+ 注释框：表示程序的注释；
+ 流向线：表示程序的控制流，以带箭头直线或曲线表达程序的执行路径；
+ 连接点：表示多个流程图的连接方式，常用于将多个较小流程图组织成较大流程图。

### 程序控制结构

三种基本结构：

1. 顺序结构：按照线性顺序依次执行；
2. 分支结构：根据条件判断结果而选择不同向前执行路径；
3. 循环结构：根据条件判断结果向后执行的一种运行方式。

任何程序都由这三种基本结构组合而成

### 单分支结构: if语句

```python
if <条件> :
    <语句块>
```

<条件>可一个或多个，用 and 与 or 连接多个条件。

#### 二分支结构: if-else语句

```python
if <条件> :
    <语句块1>
else :
    <语句块2>
```

更简洁的表达方式：适合<语句块1>和<语句块2>都只包含简单表达式的情况。

`<表达式1> if <条件> else <表达式2>`

```python
s = eval(input('请输入一个整数：'))
token = '' if s % 3 == 0 and s % 5 == 0 else "不"
print('这个数字{}能够同时被3和5整除。'.format(token))
```

#### 多分支结构: if-elif-else语句

```python
if <条件1> :
    <语句1>
elif <条件2> :
    <语句2>
    ...
else :
    <语句N>
```

按照多分支结构的代码顺序依次评估判断条件，寻找并执行第一个结果为 True 条件对应的语句块，然后跳过整个 if - elif -else 结构。（只有条件为 False 才会进入 elif）

```python
score = eval(input('输入成绩：'))
if score >= 90 :
    grade = 'A'
elif score >= 80 :
    grade = 'B'
elif score >= 70 :
    grade = 'C'
elif score >= 60 :
    grade = 'D'
else :
    grade = 'E'
print('对应的等级是：{}'.format(grade))
```

### 判断条件及组合

判断条件可以使用任何能够产生 True 或 False 的语句或函数

| 操作符  | 数学符号 |   含义   |
| :----: | :------: | :------:|
|   <    |    <     |   小于   |
|   <=   |  $\leq$  | 小于等于 |
|   >=   |  $\geq$  | 大于等于 |
|   >    |    >     |   大于   |
|   ==   |    =     |   等于   |
|   !=   |  $\neq$  |  不等于  |

Python 中，任何非零的数值、非空的数据类型都等价于 True，反之可以可直接用作判断条件。

```python
print(0 == False)
# True
print('' == True)
# False
```

Python语言使用保留字not、and和or对条件进行逻辑运算或组合

not : 否；and : 与；or : 或

### 程序的循环结构

Python语言的循环结构包括两种：遍历循环和无限循环。

+ 遍历循环使用保留字`for`依次提取遍历结构各元素进行处理；
+ 无限循环使用保留字`while`根据判断条件执行程序。

#### 遍历循环: for

```python
for <循环变量> in <遍历结构> :
    <语句>
```

遍历结构可以是字符串、文件、`range()`函数或组合数据类型等。

字符串：

```python
for <循环变量> in <字符串> :
    <语句块>
```

`range()`函数：可以制定语句块的循环次数。

```python
for <循环变量> in range(<循环次数>) :
    <语句块>
```

扩展模式：

```python
for <变量> in <结构> :
    <语句1>
else :
    <语句2>
```

当 for 循环正常结束后，程序会执行 else 语句。

else语句只在循环正常执行之后才执行并结束，因此，可以在<语句块2>中放置判断循环执行情况的语句。

#### 无限循环: while

```python
while <条件> :
    <语句块>
```

<条件>与 if 一样，结果为 True 或 False。

当判断条件为 True，执行循环体语句，结束后再次判断条件；当 False，循环终止，执行与 while 同级别缩进的后续语句。

else 扩展：

```python
while <条件> :
    <语句1>
else :
    <语句2>
```

#### 循环控制: break和continue

`break` : 用来跳出**最内层** for 或 while 循环，脱离后从循环后的代码继续执行。

```python
while True :
    s = input('请输入名字(按Q退出)：')
    if s == 'Q' :
        break
    print('输入的是：', s)
print('程序退出')
```

`continue` : 结束当前当次循环，跳出循环体下面尚未执行的语句，但不跳出整个循环。

```python
for s in 'PYTHON' :
    if s == 'Y' :
        continue
    print(s, end = '')
```

`continue`语句和`break`语句的区别是：`continue`语句只结束本次循环，不终止整个循环的执行，而`break`具备结束循环的能力。

### 程序的异常处理

#### try  except

Python 用 try 和 except 进行异常处理。

```python
try :
    <语句1>
except :
    <语句2>
```

语句1是正常执行的程序内容，当执行这个语句发生异常时，则执行 except 后的语句2。

```python
try :
    n = eval(input('请输入一个数字：'))
    print('输入数字的3次方的值为：', n ** 3)
except :
    print('输入错误，请输入一个数字！')
```

### 实例解析——猜数字游戏

编写一个“猜数字游戏”的程序，在1到1000之间随机产生一个数，然后请用户循环猜测这个数字，对于每个答案只回答“猜大了”或“猜小了”，直到猜测准确为止，输出用户的猜测次数。

```python
import random
target = random.randint(1, 1000)
count = 0
while True :
    try :
        guess = eval(input('请输入一个猜测的整数（1至1000）：'))
    except :
        print('输入有误，请重试。')
        continue
    count = count + 1
    if guess > target :
        print('大了')
    elif guess < target :
        print('小了')
    else :
        print('猜对了')
        break
print('此轮的猜测次数是：', count)
```

## 函数和代码复用

### 函数的定义：def

```python
def <函数名>(<参数列表>) :
    <函数体>
    return <返回值列表>
```

```python
def fact(n) :
    s = 1
    for i in range(1, n+1):
        s *= i
    return s
print (fact(100))
```

使用步骤：

1. 函数定义；
2. 函数调用；
3. 函数执行；
4. 函数返回

### 函数的参数传递

#### 可选参数传递

```python
def <函数名>(<非可选参数>, <可选参数> = <默认值>) :
    <函数体>
    return
```

```python
def multiply(x, y = 10) :
    print(x * y)
mulitply(99)
# 990
multiply(99, 2)
# 198
```

#### 参数名称传递

```python
def multiply(x, y = 10) :
    print(x * y)
multiply(x = 99)
# 990
multiply(y = 2, x = 99)
# 198
```

**不需要保持参数传递的顺序，参数之间的顺序可以任意调整。**

### 函数的返回值

**⭐return 语句用来结束函数并将程序返回到函数被调用的位置继续执行。**

return语句可以出现在函数中的任何部分，同时可以将0个、1个或多个函数运算的结果返回给函数被调用处的变量。

### 变量的作用域

#### 局部变量

在函数内部定义的变量，仅在函数内部有效，当函数退出时变量将不再存在。

```python
 def mul(x, y = 10) :
    z = x * y #z是局部变量
    return z
s = mul(99, 2)
print(s)
# 198
print(z)
# error : ...
```

#### 全局变量

在函数之外定义的变量，在程序执行全过程有效。全局变量在函数内部使用时，需提前使用 global 声明。`global <全局变量>`

```python
n = 2 #n是全局变量
def mul(x, y = 10) :
    global n
    return x * y * n
s = mul (99, 2)
print(s)
396
```

### 代码复用

好处：

+ 避免相同功能代码在被调用处重复编写；
+ 当更新函数功能时，所有被调用处的功能都被更新。

模块化设计：指通过函数的封装功能将程序划分为主程序、子程序和子程序间关系的表达。

模块化设计基本要求：

+ 紧耦合：尽可能合理划分功能块，功能块内部耦合紧密；
+ 松耦合：模块间关系尽可能简单，功能块之间耦合度低。

耦合性：指程序结构中各模块之间相互关联的程度，它取决于各模块间接口的复杂程度和调用方式。

+ 紧耦合：模块或系统间关系紧密，存在较多或复杂的相互调用。缺点：更新一个模块可能导致其他模块变化，复用较困难。
+ 松耦合：一般基于消息或协议实现，系统间交互简单。

### 实例解析--软文的诗词风

软文的诗词风将原有文章根据标点符号重新切分成短句并居中排版，对小屏幕阅读十分有利。使用程序将普通文章变成软文的诗词风十分有趣

```python
txt = '''
人生得意须尽欢，莫使金樽空对月。
天生我材必有用，千金散尽还复来。
'''
linewidth = 30  # 预定的输出宽度

def lineSplit(line):
    plist = [',', '!', '?', '，', '。', '！', '？']
    for p in plist:
        line = line.replace(p, '\n')
    return line.split('\n')

def linePrint(line):
    global linewidth
    print(line.center(linewidth, chr(12288)))

newlines = lineSplit(txt)
for newline in newlines:
    linePrint(newline)
```

## 组合数据类型

### 组合数据类型基本概念

能够表示多个数据的类型成为组合数据类型

+ 集合类型：元素集合，元素之间无序，相同元素在集合中唯一存在；
+ 序列类型：元素向量，元素之间存在先后关系，通过序号访问，元素之间不排他；
+ 映射类型：“键—值”数据项的组合，每个元素是一个键值对，表示为(key, value)。

#### 集合类型概述

包含0个或多个数据的无序组合，用大括号({})表示，集合中的元素可以动态增加或删除。

元素类型只能是不可变数据类型：整数、浮点数、字符串、元组等。

使用集合类型能够**过滤重复元素**

```python
T = {1010, '1010', 12.3, 1010, 1010}
print(T)
# {1010, '1010', 12.3}
```

| 操作符及运算 | 描述                                                       |
| :----------: | :--------------------------------------------------------- |
|    S - T     | 差集，返回一个新集合，包括在集合 S 中但不在集合 T 中的元素 |
|    S & T     | 交集，返回一个新集合，包括同时在集合 S 和 T 中的元素       |
|    S ^ T     | 补集，返回一个新集合，包括集合 S 和 T 中非共同元素         |
|    S \| T    | 并集，返回一个新集合，包括集合 S 和 T 中所有元素           |

|  函数或方法   | 描述                                                 |
| :-----------: | ---------------------------------------------------- |
|  `S.add(x)`   | 如果数据项 x 不在集合 S 中，将 x 增加到 S            |
| `S.remove(x)` | 如果 x 在集合 S 中，移除 x；不在则产生 KeyError 异常 |
|  `S.clear()`  | 移除 S 中所有数据项                                  |
|   `len(S)`    | 返回集合 S 元素个数                                  |
|   `x in S`    | 如果 x 是 S 的元素，返回 True；否则 False            |
| `x not in S`  | 如果 x 不是 S 的元素，返回 True；否则 False          |

set(x) 函数将其他的组合数据类型变成集合类型，也可以生成空集合变量。

```python
S = set('知之为知之不知为不知')
print(S)
# {'不', '为', '之', '知'}
for i in S :
    print(i, end = '')
# 不为之知
```

#### 序列类型概述

序列类型是一维元素向量，元素之间存在先后关系，通过序号访问。

|      操作符      | 描述                                                     |
| :--------------: | -------------------------------------------------------- |
|     `x in s`     | 如果 x 是 s 的元素，返回 True，否则 False                |
|   `x not in s`   | 如果 x 不是 s 的元素，返回 True，否则 False              |
|     `s + t`      | 连接 s 和 t                                              |
| `s * n 或 n * s` | 将序列 s 复制 n 次                                       |
|      `s[i]`      | 索引，返回 s 的第 i 个元素                               |
|    `s[i : j]`    | 切片，返回包含 s 第 i 到 j 个元素的子序列（不包含 j）    |
|  `s[i : j : k]`  | 步骤切片，返回包含 s 第 i 到 j 个元素以 k 为步数的子序列 |
|     `len(s)`     | s 的元素个数（长度）                                     |
|     `min(s)`     | s 中的最小元素                                           |
|     `max(s)`     | s 中的最大元素                                           |
|   `s.index(x)`   | s 中第一次出现元素 x 的位置                              |
|   `s.count(x)`   | s 中出现 x 的总次数                                      |

#### 映射类型概述

映射类型是“键-值”数据项的组合，每个元素是一个键值对，即元素是(key, value)，元素之间是无序的。键值对是一种二元关系，源于属性和值的映射关系

### 列表类型

#### 定义

+ 列表是包含0个或多个元组的有序序列，属于序列类型。
+ 可进行元素的增加、删除、替换、查找。
+ 没有长度限制，元素类型可以不同，不需要预定长度。
+ 列表类型用中括号(`[]`)，也可以通过`list()` 函数将集合或字符串类型转换成列表类型。

列表属于序列类型，所以列表类型支持序列类型对应的操作

```python
list('举个栗子')
# ['举', '个', '栗', '子']
```

#### 索引

沿用序列类型的索引方式，即正向递增序号或反负递减序号，用中括号作为索引操作符，不得超过列表的元素范围，否则 IndexError。

```python
ls = [1010, '1010', [1010, '1010'], 1010]
ls = [3]
# 1010
ls[-2]
# [1010, '1010']
```

可以用**遍历循环**进行操作

```python
for <循环变量> in <列表变量> :
    <语句块>
```

```python
ls = [1010, '1010', [1010, '1010'], 1010]
for i in ls :
    print(i * 2)
# 2020
# 10101010
# [1010, '1010', 1010, '1010']
# 2020
```

#### 切片

切片后的结果也是列表类型

```python
<列表或列表变量>[N : M]
或
<列表或列表变量>[N : M : K]
```

在 [ ] 中表示区间需要用冒号（:），表示枚举使用英文逗号

一般要求 N 小于 M，当 N 大于 M 时，返回空列表

### 列表类型的操作

#### 操作函数

|   函数    | 描述                       |
| :-------: | -------------------------- |
| `len(ls)` | 列表 ls 的元素个数（长度） |
| `min(ls)` | ls 中的最小元素            |
| `max(ls)` | ls 中的最大元素            |
| `list(x)` | 将 x 转变成列表类型        |

#### 列表的操作方法

`<列表变量>.<方法名称>(<方法参数>)`

|       方法        | 描述                                   |
| :---------------: | -------------------------------------- |
|  `ls.append(x)`   | 在 ls 最后增加 x                       |
| `ls.insert(i, x)` | 在 ls 第 i 位置增加 x                  |
|   `ls.clear()`    | 删除 ls 中所有元素                     |
|    `ls.pop(i)`    | 将 ls 中第 i 项元素取出并从 ls  中删除 |
|  `ls.remove(x)`   | 将 ls 中出现的第一个 x 删除            |
|  `ls.reverse()`   | ls 中元素反转                          |
|    `ls.copy()`    | 生成新列表，复制 ls                    |

**del**对列表元素或片段进行删除，使用方法如下：

```python
del <列表变量>[<索引序号>] 或
del <列表变量>[<索引起始>: <索引结束>]
```

```python
ls = [1, 2, 3, 4]
del ls[1]
print(ls)
# [1, 3, 4]
```

**ls.copy()** 生成的新列表不受旧列表影响

```python
ls = [1, 2, 3, 4]
lsn = ls.copy()
ls.clear()
print(lsn)
# [1, 2, 3, 4]

lt = [1, 2, 3, 4]
ls = lt
lt.clear()
print(ls)
# []
```

```python
def main():
    list1 = list(range(1, 8, 2))
    list1.append(100)
    print(list1)  # [1, 3, 5, 7, 100]
    list2 = ['hello'] * 5  
    print(list2)  # ['hello', 'hello', 'hello', 'hello', 'hello']
    # 计算列表长度(元素个数)
    print(len(list1))  # 5
    # 下标(索引)运算，访问列表
    print(list1[0])  # 1
    print(list1[4])  # 100
    print(list1[5])  # IndexError: list index out of range
    print(list1[-1])  # 100
    print(list1[-3])  # 5
    # 替换元素
    list1[2] = 300
    print(list1)  # [1, 3, 300, 7, 100]
    # 添加元素
    list1.append(200)  # 添加到尾部
    list1.insert(1, 400)  # 插入到对应索引号
    list1 += [1000, 2000]  # 添加到尾部
    print(list1)
    print(len(list1))
    # 删除元素
    del list1[0]  # 删除对应索引号的元素
    print(list1)
    print(list1.pop())  # 弹出末尾元素，括号内输入索引值便可弹出对应元素
    print(list1)
    list1.remove(3)  # 根据值删除元素，只删除第一个指定的值
    if 1234 in list1:  # 进行优化
        list1.remove(1234)
    del list1[0]
    print(list1)
    # 清空列表元素
    # list1 = []
    list2 = list1
    list1.clear()  # 采用clear函数清理关联列表
    print(list1)
    print(list2)


if __name__ == '__main__':
    main()
```

```python
def main():
    list1 = ['orange', 'apple', 'zoo', 'internationalization', 'blueberry']
    # sorted函数返回列表排序后的拷贝不会修改传入的列表
    list2 = sorted(list1)
    # 函数的设计就应该像sorted函数一样尽可能不产生副作用
    list3 = sorted(list1, reverse=True)
    # 通过key关键字参数指定根据字符串长度进行排序而不是默认的字母表顺序
    list4 = sorted(list1, key=len)
    print(list1)
    print(list2)
    print(list3)
    print(list4)

    # 给列表对象发出排序消息直接在列表对象上进行排序
    list1.sort(reverse=True)  # 参数reverse=True按字母反向排序
    list1.reverse()  # 反转列表
    # list1[::-1]
    print(list1)


if __name__ == '__main__':
    main()
```

### 字典的索引

`<值> = <字典变量>[<键>]`

```python
d = {'20101':'小明', '20102':'小红', '20103':'小白'}
print(d['20102'])
# 小红
```

大括号 { } 可以创建字典，索引和赋值可以增加元素。

```python
t = {}
t['20104':'小新']
print(t)
# {'20104':'小新'}
```

字典是存储可变数量键值对的数据结构，**键和值可以是任意数据类型**。

**键必须是不可变的**，如字符串，数字或元组。

### 字典的操作

#### 相关操作函数

|   函数   | 描述                      |
| :------: | ------------------------- |
| `len(d)` | 字典 d 的元素个数（长度） |
| `min(d)` | 字典 d 中键的最小值       |
| `max(d)` | 字典 d 中键的最大值       |
| `dict()` | 生成一个空字典            |

#### 操作方法

`<字典变量>.<方法名称>(<方法参数>)`

|         方法          | 描述                                                         |
| :-------------------: | ------------------------------------------------------------ |
|      `d.keys()`       | 返回所有的键信息                                             |
|     `d.values()`      | 返回所有的值信息                                             |
|      `d.items()`      | 返回所有的键值对                                             |
| `d.get(key, default)` | 键存在则返回相应值，否则返回默认值                           |
| `d.pop(key, default)` | 键存在则返回并删除值对，否则返回默认值                       |
|     `d.popitem()`     | 随机从字典中去取出一个键值对，以元组(key, value)形式返回，并从字典中删除 |
|      `d.clear()`      | 删除所有的键值对                                             |

```python
d = {'1':'A', '2':'B', '3':'C'}
d.items()
# dict_items([('1','A'), ('2', 'B'), ('3', 'C')])

d.get('4')

d.get('4', '不存在')
# '不存在'

del d['1']
print(d)
# {'2':'B', '3':'C'}

'1' in d
# True

'4' in d
# False

for k in d:
    print('The key and value are {} and {}'.format(k, d.get(k)))
# The key and value are 1 and A
# The key and value are 2 and B
# The key and value are 3 and C
```

```python
def main():
    scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
    # 通过键可以获取字典中对应的值
    print(scores['骆昊'])
    print(scores['狄仁杰'])
    # 对字典进行遍历(遍历的其实是键再通过键取对应的值)
    for elem in scores:
        print('%s\t--->\t%d' % (elem, scores[elem]))
    # 更新字典中的元素
    scores['白元芳'] = 65
    scores['诸葛王朗'] = 71
    scores.update(冷面=67, 方启鹤=85)
    print(scores)
    if '武则天' in scores:
        print(scores['武则天'])
    print(scores.get('武则天'))
    # get方法也是通过键获取对应的值但是可以设置默认值
    print(scores.get('武则天', 60))
    # 删除字典中的元素
    print(scores.popitem())
    print(scores.popitem())
    print(scores.pop('骆昊', 100))
    # 清空字典
    scores.clear()
    print(scores)


if __name__ == '__main__':
    main()
```

### 实例解析——文本词频统计

在很多情况下，会遇到这样的问题：对于一篇给定文章，希望统计其中多次出现的词语，进而概要分析文章的内容。这个问题的解决可用于对网络信息进行自动检索和归档。

```python
'''
第一步：分解并提取英文文章的单词通过txt.lower()函数将字母变成小写，排除原文大小写差异对词频统计的干扰。为统一分隔方式，可以将各种特殊字符和标点符号使用txt.replace()方法替换成空格，再提取单词。
'''
def getText():
    txt = open('hamlet.txt', 'r').read()
    txt = txt.lower()
    for ch in '!"#$&%()*+,-./:;<=>?@[\\]^_{|}~':
        txt = txt.replace(ch, '')
    return txt

hamletTxt = getText()
words = hamletTxt.split()
counts = {}
'''
第二步：对每个单词进行计数
或者，这个处理逻辑可以更简洁的表示为如下代码：
counts[word] = counts.get(word,0) + 1
'''
for word in words:
    counts[word] = count.get(word, 0) + 1
'''
对单词的统计值从高到低进行排序由于字典类型没有顺序，
需要将其转换为有顺序的列表类型，
再使用sort()方法和lambda函数配合实现根据单词次数对元素进行排序。
'''
items = list(counts.items()) # 将字典转换为记录列表
items.sort(key = lambda x:x[1], reverse = True) # 以第2列排序

for i in range(10):
    word, count = items[i]
    print('{0:<10}{1:>5}'.format(word, count))
```

## 文件和数据格式化

### 文件的使用

两种类型：文本文件、二进制文件

存储在辅助存储器上的一组数据序列。

#### 文件的类型

文本文件：由单一特定编码的字符组成；

二进制文件：直接由比特0和比特1组成，没有统一的字符编码，文件内部数据的组织格式与文件用途有关。

#### 打开和关闭

文件的存储状态是默认状态，打开后变成占用状态，关闭后再次回到存储状态。

`open()`函数：打开一个文件，并返回一个操作这个文件的变量

`<变量名> = open(<文件路径及文件名>, <打开模式>)`

| 打开模式 | 含义                                                     |
| :------: | -------------------------------------------------------- |
|  `'r'`   | 只读，如果文件不存在返回异常 FileNotFoundError，默认值   |
|  `'w'`   | 覆盖写，文件不存在则创建，存在则完全覆盖原文件           |
|  `'x'`   | 创建写，文件不存在则创建，存在则返回异常 FileExistsError |
|  `'a'`   | 追加写，文件不存在则创建，存在则在源文件最后追加内容     |
|  `'b'`   | 二进制文件模式                                           |
|  `'t'`   | 文本文件模式，默认值                                     |
|  `'+'`   | 与 r w x d 一同使用，在原功能上增加读写功能              |

<变量名>.close() 关闭文件，释放文件的使用授权。

**表示路径时，使用 `\\\` 或 / 代替  `\\` **

#### 文件的读写

|         读取方法          | 含义                                                         |
| :-----------------------: | ------------------------------------------------------------ |
|    `f.read(size = -1)`    | 从文件中读入整个文件内容。参数可选，读入前size长度的字符串或字节流 |
|  `f.readline(size = -1)`  | 从文件中读入一行内容。参数可选，读入该行前size长度的字符串或字节流 |
| `f.readlines(hint  = -1)` | 从文件中读入所有行，以每行为元素形成一个列表。参数可选，读入 hint 行 |
|     `f.seek(offset)`      | 改变当前文件操作指针的位置。offset : 0为文件开头；2为文件结尾 |

|     写入方法      | 含义                                 |
| :---------------: | ------------------------------------ |
|   `f.write(s)`    | 向文件写入一个字符串或字节流         |
| `f.writelines(s)` | 将一个元素为字符串的列表整体写入文件 |

f.write(s) 写入字符串 s，每次写入后，将会记录一个**写入指针**

```python
f = open('d:/c.txt', 'w')
f.write('123\n')
f.write('456\n')
f.close()
```

要显式地**使用`\n`进行分行**

```python
ls = ['123', '456', '789']
f = open('d:/d.txt', 'w')
f.writelinese(ls)
f.close()
```

### 数据组织的维度

#### 一维数据

由对等关系的有序或无序数据构成，采用线性方式组织。

任何表现为序列或集合的内容都可以看作是一维数据。

#### 二维数据

也称表格数据，由关联系数构成，采用二维表格方式组成。

#### 高维数据

由键值对类型的数据组成，采用对象方式组织，可以多层嵌套。

高维数据在Web系统中十分常用，作为当今Internet组织内容的主要方式，高位数据衍生出HTML、XML、JSON等具体数据组织的语法结构。

### 一维数据处理

#### 表示

由于是线性结构，因此主要采用列表形式表示。

#### 存储

4种方法：

1. 采用空格分隔元素：A 12 数据
2. 采用逗号分隔元素：A, 12, 数据
3. 采用续行分隔元素：A
                                     12
                                     数据
4. 其他特殊符号分隔，如分号：A; 12; 数据

逗号分隔的存储格式为 CSV 格式（Comma_Separated Values），它是**一种通用的、相对简单的文件格式**

```python
ls = ['北京', '上海', '广州']
f = open('city.csv', 'w')
f.write(','.join(ls) + '\n')
f.close()
```

#### 处理

首先需要从 CSV 文件读入一维数据，并将其表示为列表对象。

默认包含了一个换行符 `\n`。**采用 `.strip()` 去掉**

```python
f = open('city.csv', 'r')
ls = f.read().strip('\n').split(',')
f.close()
print(ls)
```

### 二维数据的处理

#### 表示方法

二维数据可以采用二维列表来表示。

二维数据一般采用相同的数据类型存储

#### 存储方式

用 csv 文件存储

```python
# ls 代表二维列表
f = open('cpi.csv', 'w')
for row in ls:
    f.wrtie(','.join(row) + '\n')
f.close()
```

#### 处理代码

```python
f = open('cpi.csv', 'r')
ls = []
for line in f :
    ls.append(line.strip('\n').split(','))
f.close()
print(ls)
```

与一维列表不同，二维列表一般需要借助**循环遍历**实现对每个数据的处理

```python
for row in ls :
    for item in row:
对第 row 行第 item 列元素进行处理
```

对二维数据进行格式化输出，打印成表格形状：

```python
#此出省去从 CSV 获得的 ls
for row in ls:
    line = ''
    for item in row:
        line += '{:10}\t'.format(item) #\t 横行制表符
    print(line)
```

#### `zip()`函数

用来获取两个组合数据类，并将它的元素交织返回

```python
x = [1, 2, 3]
y = [4, 5, 6]
print(zip(x, y))
# [(1, 4), (2, 5), (3, 6)]
```

### 实例解析--国家财政数据趋势演算

由个别数据预测规律属于数值分析的内容，可以通过线性回归方程建立简单模型，

线性回归方程的公式为： $\hat y$ = b$\hat x$ + a

X代表年份，Y代表各年份对应的数值。Python实现的国家财政数据趋势演算，根据上述三个数值计算出更多年份的可能数据。

```python
def means(data):
    return sum(data) / len(data)

def linearRegression(xlist, ylist):
    xmeans, ymeans = means(xlist), means(ylist)
    bNumerator = -len(xlist) * xmeans * ymeans
    bDenominator = -len(xlist) * xmeans ** 2
    for x, y in zip(xlist, ylist):
        bNumerator += x * y
        bDenominator += x ** 2
    b = bNumerator / bDenominator
    a = ymeas - b * xmeans
    return a, b
```

```python
def parseCSV(filename):
    dataNames, data = [], []
    f = open(filename, 'r', encoding='utf-8')
    for line in f:
        splitedLine = line.strip().split(',')
        if '指标' in splitedLine[0]:
            years = [int(x[:-1]) for x in splitedLine[1:]]
        else:
            dataNames.append('{:10}'.format(splitedLine[0]))
            data.append([float(x) for x in splitedLine[1:]])
    f.close()
    return years, dataNames, data
# 线性回归代码
def means(data):
    return sum(data) / len(data)

def linearRegression(xlist, ylist):
    xmeans, ymeans = means(xlist), means(ylist)
    bNumerator = - len(xlist) * xmeans * ymeans
    bDenominator =  - len(xlist) * xmeans ** 2
    for x, y in zip(xlist, ylist):
        bNumerator += x * y
        bDenominator += x ** 2
    b = bNumerator / bDenominator
    a = ymeans - b * xmeans
    return a, b

def calNewData(newyears, a, b):
    return [(a + b * x) for x in newyears]

def showResults(years, dataNames, newDatas):
    print('{:^60}'.format('国家财政收支线性估计'))
    header = '指标       '
    for year in years:
        header += '{:10}'.format(year)
    print(header)
    for name, lineData in zip(dataNames, newDatas):
        line = name
        for data in lineData:
            line += '{:>10.1f}'.format(data)
        print(line)

def main():
    newyears = [x+2010 for x in range(7)]
    newDatas = []
    years, dataNames, datas = parseCSV('finance.csv')
    for data in datas:
        a, b = linearRegression(years, data)
        newDatas.append(calNewData(newyears, a, b))
    showResults(newyears, dataNames, newDatas)

main()
```

## Python 计算生态

### 计算思维

程序设计是实践计算思维的重要手段

本质：抽象和自动化

算法（Algorithm）：**解决问题的清晰指令**

### 程序设计方法论

一个解决复杂问题行之有效的方法被称作自顶而下的设计方法，其基本思想是以一个总问题开始，试图把它表达为很多小问题组成的解决方案。再用同样的技术依次攻破每个小问题，最终问题变得非常小，以至于可以很容易解决。然后只需把所有的碎片组合起来，就可以得到一个程序。

#### 自顶向下设计：最重要是顶层设计

是一种开发复杂程序最具价值的设计理念和工具，设计过程自然且简单，自顶向下设计通过封装实现抽象，利用了模块化设计的思想。

#### 自底向上执行

开展测试的更好办法也是将程序分成小部分逐个测试

执行中等规模程序的最好方法是从结构图最底层开始，而不是从顶部开始，然后逐步上升。或者说，先运行和测试每一个基本函数，再测试由基础函数组成的整体函数，这样有助于定位错误

### 计算生态

Python官方网站提供了第三方库索引功能[PyPI](https://pypi.python.org/pypi)

函数库并非都采用 Python 编写，很多采用 C 等语言编写的库可以通过简单的接口封装供 Python 程序调用。“胶水语言”。

Python 第三方程序包：

+ 库 library；
+ 模块 module；
+ 类 class；
+ 程序包 package

### 实例解析--Web页面元素提取

Web页面，一般是HTML页面，是Internet组织信息的基础元素。Web页面元素提取是一类常见问题，在网络爬虫、浏览器等程序中有着不可或缺的重要作用。

HTML指超文本标记语言，严格来说，HTML不是一种编程语言，而是一种对信息的标记语言，对Web的内容、格式进行描述。

自动地从一个链接获取HTML页面是网络爬虫的功能，本实例功能可以整体分成如下4个步骤：

+ 步骤1: 读取保存在本地的html文件；
+ 步骤2：解析并提取其中的图片链接；
+ 步骤3：输出提取结果到屏幕；
+ 步骤4：保存提取结果为文件。

```python
'''
根据上述步骤，可以写出主程序如下。
其中设置了4个函数
getHTMLlines()、extractImageUrls()、showResults()和saveResults()分别对应上述4个步骤。
'''
def getHTMLlines(htmlpath):
    '''
    getHTMLlines()函数读取HTML文件并内容，
    并将结果转换为一个分行列表，
    为了兼容不同编码，建议在open()函数中增加encoding字段，
    设置采用UTF-8编码打开文件。
    '''
    f = open(htmlpath, "r", encoding='utf-8')
    ls = f.readlines()
    f.close()
    return ls

def extractImageUrls(htmllist):
    '''
    extractImageUrls()是程序的核心，用于解析文件并提取图像的URL。
    '''
    urls = []
    for line in htmllist:
        if 'img' in line:
            url = line.split('src=')[-1].split('"')[1]
            if 'http' in url:
                urls.append(url)
    return urls

def showResults(urls):
    '''
    showResults()函数将获取的链接输出到屏幕上，
    增加一个计数变量提供更好用户体验
    '''
    count = 0
    for url in urls:
        print('第{:2}个URL:{}'.format(count, url))
        count += 1

def saveResults(filepath, urls): # 保存结果到文件
    f = open(filepath, "w")
    for url in urls:
        f.write(url+"\n")
    f.close()

def main():
    '''
    定义main()函数的目的是为了让代码更加清晰，
    作为主程序，也可以不采用函数形式而直接编写。
    main()前两行分别制定了拟获取HTML文件的路径和结果输出路径。
    '''
    inputfile  = 'nationalgeographic.html'
    outputfile = 'nationalgeographic-urls.txt'
    htmlLines = getHTMLlines(inputfile)
    imageUrls = extractImageUrls(htmlLines)
    showResults(imageUrls)
    saveResults(outputfile, imageUrls)

main()
```

## Python 标准库概览

有一部分Python计算生态随Python安装包一起发布，用户可以随时使用，被称为Python标准库。

### turtle库概述

turtle 是 Python 重要的标准库之一，它能够进行基本的图形绘制。概念诞生于1969年，成功应用于 LOGO 编程语言。

基本框架：一个龟在坐标系中爬行，其爬行轨迹形成了绘制图形。

刚开始时，位于正中央，前进方向为水平右方。

三种引用方式：

1. ```python
   import turtle
   turtle.<函数名>()
   ```

2. ```python
   from turtle import *
   <函数名>()
   ```

3. ```python
   import turtle as t(也可以是其他别名)
   t.<函数名>()
   ```

### turtle库与基本绘图

turtle库包含100多个功能函数，主要包括窗体函数、画笔状态函数、画笔运动函数等三类。

#### 窗体函数：turtle.setup()

`turtle.setup(width, height, startx, starty)`

作用：设置主窗体的大小和位置。

width：窗口宽度。整数：像素值；小数：窗口宽度与屏幕的比例。

height：窗口高度。

startx：窗口左侧与屏幕左侧的像素距离。None：水平中央。

starty：窗口顶部与屏幕顶部的像素距离。None：垂直中央。

#### 画笔状态函数

|           函数            | 作用                                        |
| :-----------------------: | ------------------------------------------- |
|        `pendown()`        | 放下画笔                                    |
|         `penup()`         | 提起画笔，与 pendown() 配对使用             |
|        `pensize()`        | 设置画笔线条的粗细                          |
|       `pencolor()`        | 设置颜色                                    |
|      `begin_fill()`       | 填充前，调用                                |
|       `end_fill()`        | 填充结束                                    |
|        `filling()`        | 返回填充的状态，True 为填充，False 为未填充 |
|         `clear()`         | 清空当前窗口，但不改变当前画笔位置          |
|         `reset()`         | 清空并重置                                  |
|      `screensize()`       | 设置画布的长与宽                            |
|      `showturtle()`       | 显示画笔的 turtle 形状                      |
|      `hideturtle()`       | 隐藏画笔的 turtle 形状                      |
|       `isvisible()`       | 如果 turtle 可见，则返回 True               |
| `write(str, font = None)` | 输出 font 字体的 str                        |

#### 画笔运动函数

|        函数         | 作用                               |
| :-----------------: | ---------------------------------- |
|     `forward()`     | 前进指定距离                       |
|    `backward()`     | 后退指定距离                       |
|   `right(angle)`    | 向右旋转 angle 角度                |
|    `left(angle)`    | 向左旋转 angle 角度                |
|    `goto(x, y)`     | 移动到（x，y）处                   |
|      `setx()`       | 将当前 x 轴移动到指定位置          |
|      `sety()`       | 将当前 y 轴移动到指定位置          |
| `setheading(angle)` | 设置当前朝向为 angle 角度          |
|      `home()`       | 设置当前位置为原点，朝向东         |
| `circle(radius, e)` | 绘制一个半径 r 和角度 e 的园或弧形 |
|   `dot(r, color)`   | 绘制一个半径 r 和颜色的圆点        |
|      `undo()`       | 撤销画笔最后一个动作               |
|      `speed()`      | 设置绘制速度，参数为0~10           |

### random 库概述

用于产生各种分布的伪随机数序列。采用梅森旋转算法（Mersenne twiste）生成伪随机数序列，可用于除随机性要求更高的加密算法外大多数工程应用。

最基本函数：`random.random()`，它生成 [0.0, 1.0)之间的小数

### random 库与随机数应用

|             函数              | 作用                                                |
| :---------------------------: | --------------------------------------------------- |
|        `seed(a = None)`       | 初始化随机数种子，默认值为当前系统时间              |
|           `random()`          | 生成 [0.0, 1.0] 之间的小数                          |
|         `randint(a, b)`         | 生成一个 [a, b] 之间的整数                          |
|        `getrandbits(k)`       | 生成一个 k 比特长度的随机整数                       |
| `randrange(start, stop [step])` | 生成一个 [start, stop) 之间以 step 为步数的随机整数 |
|       `uniform(a, b)`      | 生成一个 [a, b] 之间的随机小数                      |
|          `choice(seq)`          | 从序列类型（如列表）中随机返回一个元素              |
|         `shuffle(seq)`        | 将序列类型中元素随机排列，返回序列                  |
|        `sample(pop, k)`       | 从 pop 类型中随机选取 k 个元素，以列表类型返回      |

### time 库概述

Python 提供的处理时间标准库。提供系统级精确计时器的计时功能，可以用来分析程序性能，也可以让程序暂停运行时间。

3方面主要功能：

1. 时间处理：`time.time()`、`time.gmtime()`、`time.localtime()`、`time.ctime()`
2. 时间格式化：`time.mktime()`、`time.strftime()`、`time.strptime()`
3. 计时：`time.sleep()`、`time.monotonic()`、`time.perf_counter()`

|         函数          | 作用                                                         |
| :-------------------: | ------------------------------------------------------------ |
|     `time.time()`     | 获取当前的时间戳                                             |
|    `time.gmtime()`    | 获取当前时间戳对应的 struct_time 对象                        |
|  `time.localtime()`   | 获取当前时间戳对应的本地时间的 struct_time 对象              |
|    `time.ctime()`     | 获取当前时间戳对应的易读字符串表示，内部会调用 time.localtime() |
|    `time.mktime()`    | 将 srtuct_time 转换为时间戳                                  |
|   `time.strftime()`   | 时间格式化最有效的方法，几乎可以以任何通用格式输出时间       |
|   `time.strptime()`   | 提取字符串中的时间来生成 struct_time                         |
| `time.perf_counter()` | 返回计时器的精准时间（系统的运行时间），包含整个系统的睡眠时间。由于返回值的基准点是未定义的，所以，只有连续调用的结果之间的差才是有效的。<br />调用一次 `perf_counter()`，从计算机系统里随机选一个时间点A，计算其距离当前时间点B1有多少秒。当第二次调用该函数时，默认从第一次调用的时间点A算起，距离当前时间点B2有多少秒。两个函数取差，即实现从时间点B1到B2的计时功能。 |

`struct_time` 元素

| 下标 |   属性   | 值                           |
| :--: | :------: | ---------------------------- |
|  0   | tm_year  | 年份，整数                   |
|  1   |  tm_mon  | 月份 [1, 12]                 |
|  2   | tm_mday  | 日期 [1, 31]                 |
|  3   | tm_hour  | 小时 [0, 23]                 |
|  4   |  tm_min  | 分钟 [0, 59]                 |
|  5   |  tm_sec  | 秒 [0, 61]                   |
|  6   | tm_wday  | 星期 [0, 6] （0 表示星期一） |
|  7   | tm_yday  | 该年第几天 [1, 366]          |
|  8   | tm_isdst | 是否夏令时，0否，1是，-1未知 |

`time.strftime()`:

`time.strftime('<参数>', time)`

| 参数符号 |  日期/时间  |       值范围       |
| :------: | :---------: | :----------------: |
|    %Y    |    年份     |    0001 - 9999     |
|    %m    |    月份     |      01 - 12       |
|    %B    |    月名     | January - December |
|    %b    |  月名缩写   |    Jan. - Dec.     |
|    %d    |    日期     |      01 - 31       |
|    %A    |    星期     |  Monday - Sunday   |
|    %a    |  星期缩写   |    Mon. - Sun.     |
|    %H    | 小时（24h） |      00 - 23       |
|    %I    |     12h     |      01 - 12       |
|    %p    |   上/下午   |       AM, PM       |
|    %M    |    分钟     |       00 -59       |
|    %S    |     秒      |      00 - 59       |

### time 库与程序计时

三要素：

+ 程序开始/结束时间
+ 程序运行时间
+ 程序各核心模块运行时间

`time.sleep(t)`：推迟 t 秒执行

`time.perf_counter()`：计时器，每次调用记录当前执行时间

### 实例解析--雪景艺术绘图

turtle图形艺术，指利用turtle库画笔创造性绘制绚丽多彩艺术图形的过程

turtle图形艺术效果中隐含着很多随机元素，如随机颜色、尺寸、位置和数量等。在图形艺术绘制中需要引入随机函数库random。常用randint()函数，生成指定范围内的随机数

绘制分为三个步骤:

1. 构建图的背景
2. 绘制雪花效果
3. 绘制雪地效果

```python
from turtle import *
from random import *
# 第二步，绘制雪花效果。
def drawSnow():
    hideturtle()
    pensize(2)
    for i in range(100):
        r, g, b = random(), random(), random()
        pencolor(r,g,b)
        penup()
        setx(randint(-350,350))
        sety(randint(1,270))
        pendown()
        dens = randint(8,12)
        snowsize = randint(10,14)
        for j in range(dens):
            forward(snowsize)
            backward(snowsize)
            right(360/dens)
# 第三步，绘制雪地效果。
def drawGround():
    hideturtle()
    for i in range(400):
        pensize(randint(5,10))
        x = randint(-400,350)
        y = randint(-280,-1)
        r, g, b = -y/280, -y/280, -y/280
        pencolor((r,g,b))
        penup()
        goto(x,y)
        pendown()
        forward(randint(40,100))
# 第一步，构建图的背景
setup(800,600,200,200)
tracer(False)
bgcolor("black")
drawSnow()
drawGround()
done()
```

## Python 第三方库概览

### 获取和安装

#### pip 工具

是 Python 官方提供并维护的在线第三方库安装工具。

`pip install <库名>`

#### 自定义安装

一般适用于在 pip 中尚无登记或安装失败的第三方库

美国加州大学尔湾分校提供了一个页面，帮助Python用户获得Windows可直
接安装的第三方库文件，[链接地址](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

这里以scipy为例说明，首先在上述页面中找到scipy库对应的内容。选择其中的.whl文件下载，这里选择适用于Python 3.5版本解释器和32位系统的对应文件：scipy-0 . 1 7 . 1 - c p 3 5 - c p 3 5 m - w i n 3 2 . w h l ， 下载该文件到
D:\pycodes目录。

然后，采用pip命令安装该文件。

`pip install D:\pycodes\scipy-0.17.1-cp35-cp35m-win32.whl`

#### pip 工具使用

```powershell
pip install <库名> #安装
pip uninstall <库名> #卸载
pip list #列出当前系统以安装的第三方库
pip show <库名> #列出某个以安装库的详细信息
pip download <库名> #下载第三方库的安装包，但不安装
pip search <关键字> #联网搜索库名或摘要中的关键字
pip -h  #列出pip常用的子命令
```

### PyInstaller 库

将 Python 源文件（.py）打包，变成直接可运行的可执行文件。

使用PyInstaller库对Python源文件打包十分简单，使用方法如下：

`PyInstaller <程序文件名>`

生成 dist 和 build 文件夹。build 是存储临时文件的目录。最终的打包程序在dist内部与源文件同名的目录中。

注意问题：

+ 文件路径中不能出现空格和英文句号（.）
+ 源文件必须是 UTF-8 编码

|    常用参数    | 描述                                 |
| :------------: | ------------------------------------ |
|   -h, --help   | 查看帮助                             |
|    --clean     | 清理打包过程中的临时文件             |
|  -D, --onedir  | 默认值，生成 dist 目录               |
| -F, --onefile  | 在 dist 文件夹中只生成独立的打包文件 |
| -i, <图标.ico> | 指定打包程序使用的图标文件           |

### jieba 库

重要的第三方中文分词函数库

原理：是利用一个中文词库，将待分词的内容与分词词库进行比对，通过图结构和动态规划方法找到最大概率的词组。除了分词，jieba还提供增加自定义中文单词的功能。

三模式：

1. 精确模式：最精确地切开，适合文本分析；
2. 全模式：把句子中所有可以成词的词语都扫描出来，但是不能解歧义；
3. 搜索引擎模式：在精确模式的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词

|           函数            | 作用               |
| :-----------------------: | ------------------ |
|         `lcut(s)`         | 精确模式，返回列表 |
| `lcut(s, cut_all = True)` | 全模式             |
|   `lcut_for_search(s)`    | 搜索模式           |
|       `add_word(w)`       | 向词典中添加新词 w |

### wordcloud 库

“关键词云层”、“关键词渲染”

|      常用参数      | 功能                                              |
| :----------------: | ------------------------------------------------- |
|    `font_path`     | 指定字体文件的完整路径，默认 None                 |
|      `width`       | 生成图片宽度，默认400像素                         |
|      `height`      | 生成图片高度，默认200像素                         |
|       `mask`       | 词云形状，默认 None，方形图                       |
|  `min_font_size`   | 词云中最小的字体字号，默认4号                     |
|    `font_step`     | 字号步进间隔，默认1                               |
|    `stopwords`     | 被排除词列表，排除词不再词云中显示                |
| `background_color` | 背景颜色，默认黑色                                |
|    `max_words`     | 词云中最大词数，默认200                           |
|  `max_font_size`   | 词云中最大的字体字号，默认 None，根据高度自动调整 |

|      常用方法      | 功能     |
| :----------------: | -------- |
|  `generate(text)`  | 生成词云 |
| `tofile(filename)` | 保存     |

图像词云：

```python
from scipy.misc import imread
mask = imread('***.png')
```

### 实例解析--《红楼梦》人物出场词云

```python
# 先输出排序前15的单词
import jieba
f = open("红楼梦.txt", "r", encoding="utf-8")
txt = f.read()
f.close()
words  = jieba.lcut(txt)
counts = {}
for word in words:
    if len(word) == 1:  #排除单个字符的分词结果
        continue
    else:
        counts[word] = counts.get(word,0) + 1
items = list(counts.items())
items.sort(key=lambda x:x[1], reverse=True)
for i in range(15):
    word, count = items[i]
    print ("{0:<10}{1:>5}".format(word, count))
```

```python
# 需要排除一些人名无关词汇，如“什么”、“一个”等
import jieba
excludes = {"什么","一个","我们","那里","你们","如今", \
            "说道","知道","老太太","起来","姑娘","这里", \
            "出来","他们","众人","自己","一面","太太", \
            "只见","怎么","奶奶","两个","没有","不是", \
            "不知","这个","听见"}
f = open("红楼梦.txt", "r", encoding="utf-8")
txt = f.read()
f.close()
words  = jieba.lcut(txt)
counts = {}
for word in words:
    if len(word) == 1:  #排除单个字符的分词结果
        continue
    else:
        counts[word] = counts.get(word,0) + 1
for word in excludes:
    del(counts[word])
items = list(counts.items())
items.sort(key=lambda x:x[1], reverse=True)
for i in range(5):
    word, count = items[i]
    print ("{0:<10}{1:>5}".format(word, count))
```

```python
# 结合已经将结果的词云效果，利用wordcloud库，将人物出场统计以词云的方式展现出来
import jieba
from wordcloud import WordCloud

excludes = {"什么","一个","我们","那里","你们","如今", \
            "说道","知道","老太太","起来","姑娘","这里", \
            "出来","他们","众人","自己","一面","太太", \
            "只见","怎么","奶奶","两个","没有","不是", \
            "不知","这个","听见"}
f = open("红楼梦.txt", "r", encoding="utf-8")
txt = f.read()
f.close()
words  = jieba.lcut(txt)
newtxt = ' '.join(words)
wordcloud = WordCloud(background_color="white", \
                          width=800, \
                          height=600, \
                          font_path="msyh.ttc", \
                          max_words=200, \
                          max_font_size=80, \
                          stopwords = excludes, \
                          ).generate(newtxt)
wordcloud.to_file('红楼梦基本词云.png')
```

## Python 第三方库纵览

### 网络爬虫方向

自动进行 HTTP 访问并捕获 HTML 页面的程序。

#### requests

简洁且简单

|    函数     | 功能                     |
| :---------: | ------------------------ |
| `request()` | 构建一个请求             |
|   `get()`   | 获取 HTML 网页的主要方法 |
|  `head()`   | 获取 HTML 网页头信息     |
|  `post()`   | 提交 POST 请求           |
|  `patch()`  | 提交局部修改请求         |
| `delete()`  | 提交删除请求             |
|   `put()`   | 提交 PUT 请求            |

#### scrapy

快速的、高层次的 web 获取框架

### 数据分析方向

#### numpy

开源数值计算扩展第三方库，用于处理数据类型相同的多维数据（ndarray），“数组”。

#### scipy

在 numpy 库的基础沈阳增加了众多的科学、数学以及工程计算中常用的库函数。包括统计、优化、整合、线性代数、傅里叶变换、信号分析、图像处理、常微分方程求解等众多模块。

#### pandas

基于 numpy 扩展。为解决数据分析任务。

### 文本处理方向

#### pdfminer

一个可以从 PDF 文档中提取各类信息的第三方库。

#### openpyxl

一个处理 Excel 文档的 Python 第三方库。

#### python_docx

一个处理 Word 文档的第三方库。

#### beautifulsoup4

用于解析和处理 HTML 和 XML。

### 数据可视化方向

指根据数据特点将其展示为易于理解的图形的过程。

#### matplotlib

主要进行二维图标数据展示，广泛用于科学计算的数据可视化。

#### TVTK

专业可编程的三维可视化工具。

#### mayavi

基于 VTK 开发，完全用 Python 编写。

### 用户图形界面方向

#### PyQt5

最成熟的商业级 GUI 第三方库。

#### wxPython

wxPython是Python语言的一套优秀的GUI图形库，它是跨平台GUI库wxWidgets的Python封装，可以使Python程序员能够轻松地创建健壮可靠、功能强大的图形用户界面的程序。

#### PyGTK

### 机器学习方向

#### scikit-learn

一个简单且高效的数据挖掘和数据分析工具。

#### TensorFlow

Google 基于 DistBelief 进行研发的第二代人工智能学习系统。

#### Theano

为执行深度学习中大规模神经网络算法而设计，擅长处理多维数组。

### Web 开发方向

#### Django

最流行的开源 Web 应用框架。

#### Pyramid

相对小巧、快速、灵活的开源 Python Web 框架。

#### Flask

轻量级 Web 应用框架。

### 8.游戏开发方向

#### Pygame

在 SDL 库基础上进行封装的、面向游戏开发入门的 Python 第三方库。

#### Panda3D

一个开源、跨平台的3D渲染和游戏开发库。

#### cocos2d

一个构建2D游戏和图形界面交互式应用的框架。

### 更多

#### PIL

在图像处理方面的重要第三方库。

+ 图像归档：
  1. 对图像进行批处理；
  2. 生成图像预览；
  3. 图像格式转换等。
+ 图像处理：
  1. 基本处理；
  2. 像素处理；
  3. 颜色处理等。

#### SymPY

一个支持符号计算的第三方库。一个全功能的计算机代数系统。

#### NLTK

自然语言处理第三方库。

语料处理、文本统计、内容理解、情感分析等多种应用。

#### WeRoBot

一个微信公众号开发框架，也成为微信机器人框架。

#### MyQR

一个能够产生基本二维码、艺术二维码和动态二维码的第三方库。

## 附录

### 常用 Unicode 编码表

|      名称      |       范围       |
| :------------: | :--------------: |
|    基础汉字    | [0x4e00, 0x9fa5] |
|      数字      | [0x0030, 0x0039] |
|    小写字母    | [0x0061, 0x007a] |
|    大写字母    | [0x0041, 0x005a] |
|      箭头      | [0x2190, 0x21ff] |
|   数字运算符   | [0x2200, 0x22ff] |
| 封闭式字母数字 | [0x2460, 0x24ff] |
|     制表符     | [0x2500, 0x257f] |
|    方块元素    | [0x2580, 0x259f] |
|    几何图形    | [0x25A0, 0x25ff] |
|  一般标点符号  | [0x2000, 0x206f] |
|      韩文      | [0xAC00, 0xD7A3] |
|      货币      | [0x20a0, 0x20cf] |
|      泰文      | [0x0e00, 0x07f]  |
|   中日韩符号   | [0x3000, 0x303f] |
| 中日韩括号数字 | [0x3200, 0x32ff] |

### 转义字符

| 符号 | 作用               |
| :--: | ------------------ |
|  `\` | （在行尾时）续行符 |
|  `\\` | 反斜杠符号         |
|  `'`  | 单引号             |
|  `"`  | 双引号             |
|  `\a`  | 响铃               |
|  `\b`  | 退格（Backspace）  |
|  `\e`  | 转义               |
| `\000` | 空                 |
|  `\n`  | 换行               |
|  `\v`  | 纵向制表符         |
|  `\t`  | 横向制表符         |
|  `\r`  | 回车               |
|  `\f`  | 换页               |

### 基本的Python内置函数

Python解释器提供了68个内置函数

| 函数名称          | 函数说明                                                     |
| ----------------- | ------------------------------------------------------------ |
| `abs(x)`          | x的绝对值，如果x是复数，返回复数的模                         |
| `all(x)`          | 组合类型变量x中所有元素都为真时返回True，否则返回False；若x为空，返回True |
| `any(x)`          | 组合类型变量x中任一元素都为真时返回True，否则返回False；若x为空，返回False |
| `bin(x)`          | 将整数x转换为等值的二进制字符串<br />`bin(1010)`的结果是'0b1111110010' |
| `bool(x)`         | 将x转换为Boolean类型，即True或False<br />`bool('')`的结果是False |
| `chr(i)`          | 返回Unicode为i的字符<br/>`chr(9996)`的结果是'✌ '             |
| `complex(r,i)`    | 创建一个复数 r + i*1j，其中i可以省略<br/>`complex(10,10)`的结果是10+10j |
| `dict()`          | 创建字典类型<br/>`dict()`的结果是一个空字典{}                |
| `divmod(a,b)`     | 返回a和b的商及余数<br/>`divmod(10,3)`结果是一个(3,1)         |
| `eval(s)`         | 计算字符串s作为Python表达式的值<br/>`eval('1+99')`的结果是100 |
| `exec(s)`         | 计算字符串s作为Python语句的值<br />`exec('a = 1+999')`运行后，变量a的值为1000 |
| `float(x)`        | 将x转换成浮点数<br/>`float(1010)`的结果是1010.0              |
| `hex(x)`          | 将整数转换为16进制字符串<br/>`hex(1010)`的结果是'0x3f2       |
| `input(s)`       | 获取用户输入，其中s是字符串，作为提示信息可选                |
| `int(x)`         | 将x转换成整数<br/>int(9.9)的结果是9                          |
| `list(x)`         | 创建或将变量x转换成一个列表类型<br/>`list({10,9,8})`的结果是`[8,9,10]` |
| `max(a1,a2,…)`   | 返回参数的最大值                                             |
| `min(a1,a2,…)`   | 返回参数的最小值                                             |
| `oct(x)`          | 将整数x转换成等值的八进制字符串形式<br />`oct(1010)`的结果是'0o1762' |
| `open(fname, m)` | 打开文件，包括文本方式和二进制方式等<br/>其中，m部分可以省略，默认是以文本可读形式打开 |
| `ord(c)`          | 返回一个字符的Unicode编码值<br/>`ord('字')`的结果是23383     |
| `pow(x,y`       | 返回x的y次幂<br/>`pow(2,pow(2,2))`的结果是16                 |
| `print(x)`        | 打印变量或字符串x<br/>`print()`的end参数用来表示输出的结尾字符 |
| `range(a,b,s)`    | 从a到b(不含)以s为步长产生一个序列<br />`list(range(1,10,3))`的结果是[1, 4, 7] |
| `reversed(r)`    | 返回组合类型r的逆序迭代形式<br/>`for i in reversed([1,2,3])`将逆序遍历列表 |
| `round(n)`        | 四舍五入方式计算n<br/>`round(10.6)`的结果是11                |
| `set(x)`          | 将组合数据类型x转换成集合类型<br/>`set([1,1,1,1])`的结果是{1} |
| `sorted(x)`       | 对组合数据类型x进行排序，默认从小到大<br/>`sorted([1,3,5,2,4])`的结果是`[1,2,3,4,5]` |
| `str(x)`          | 将x转换为等值的字符串类型<br/>`str(0x1010)`的结果是'4112'    |
| `sum(x)`         | 对组合数据类型x计算求和结果<br/>`sum([1,3,5,2,4])`的结果是15 |
| `type(x)`         | 返回变量x的数据类型<br/>`type({1:2})`的结果是`<class 'dict'>`  |
