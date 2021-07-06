---
title: Sass
date: 2021-07-01 08:29:50
tags:
- Sass
categories:
- Web
---

+ Sass 是一个 CSS 预处理器。
+ Sass 是 CSS 扩展语言，可以帮助我们减少 CSS 重复的代码，节省开发时间。
+ Sass 完全兼容所有版本的 CSS。
+ Sass 扩展了 CSS3，增加了规则、变量、混入、选择器、继承、内置函数等等特性。
+ Sass 生成良好格式化的 CSS 代码，易于组织和维护。
+ Sass 文件后缀为 `.scss`。

## 快速入门

```scss
/* 定义变量与值 */
$bgcolor: lightblue;
$textcolor: darkblue;
$fontsize: 18px;

/* 使用变量 */
body {
  background-color: $bgcolor;
  color: $textcolor;
  font-size: $fontsize;
}
```

### Sass 变量

变量用于存储一些信息，它可以重复使用。

Sass 变量可以存储以下信息：

+ 字符串
+ 数字
+ 颜色值
+ 布尔值
+ 列表
+ null 值

Sass 变量使用 `$` 符号，变量声明后我们就可以在代码中使用它：

```scss
$myFont: Helvetica, sans-serif;
$myColor: red;
$myFontSize: 16px;
$myWidth: 680px;

body {
  font-family: $myFont;
  font-size: $myFontSize;
  color: $myColor;
}

#container {
  width: $myWidth;
}
```

将以上代码转换为 CSS 代码，如下所示：

```css
body {
  font-family: Helvetica, sans-serif;
  font-size: 16px;
  color: red;
}

#container {
  width: 680px;
}
```

### Sass 作用域

Sass 变量的作用域只能在当前的层级上有效果，如下所示 h1 的样式为它内部定义的 green，p 标签则是为 red。

```scss
$myColor: red;

h1 {
  $myColor: green;   // 只在 h1 里头有用，局部作用域
  color: $myColor;
}

p {
  color: $myColor;
}
```

将以上代码转换为 CSS 代码，如下所示：

```css
h1 {
  color: green;
}

p {
  color: red;
}
```

当然 Sass 中我们可以使用 `!global` 关键词来设置变量是全局的：

```scss
$myColor: red;

h1 {
  $myColor: green !global;  // 全局作用域
  color: $myColor;
}

p {
  color: $myColor;
}
```

现在 p 标签的样式就会变成 green。

将以上代码转换为 CSS 代码，如下所示：

```css
h1 {
  color: green;
}

p {
  color: green;
}

```

注意：所有的全局变量我们一般定义在同一个文件，如：_globals.scss，然后我们使用 [`@include`](https://www.runoob.com/sass/sass-mixin-include.html) 来包含该文件。

## Sass 嵌套规则与属性

### Sass 嵌套规则

Sass 嵌套 CSS 选择器类似于 HTML 的嵌套规则。

如下我们嵌套一个导航栏的样式：

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }
  li {
    display: inline-block;
  }
  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

实例中，ul, li, 和 a 选择器都嵌套在 nav 选择器中

将以上代码转换为 CSS 代码，如下所示：

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}

nav li {
  display: inline-block;
}

nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```

### Sass 嵌套属性

很多 CSS 属性都有同样的前缀，例如：font-family, font-size 和 font-weight ， text-align, text-transform 和 text-overflow。

在 Sass 中，我们可以使用嵌套属性来编写它们：

```scss
body{
    font: {
        family: Helvetica, sans-serif;
        size: 18px;
        weight: bold;
    }
      
    text: {
        align: center;
        transform: lowercase;
        overflow: hidden;
    }
}
```

将以上代码转换为 CSS 代码，如下所示：

```css
body {
  font-family: Helvetica, sans-serif;
  font-size: 18px;
  font-weight: bold;
  text-align: center;
  text-transform: lowercase;
  text-overflow: hidden;
}
```

## Sass @import

类似 CSS，Sass 支持 @import 指令。

@import 指令可以让我们导入其他文件等内容。

CSS @import 指令在每次调用时，都会创建一个额外的 HTTP 请求。但，Sass @import 指令将文件包含在 CSS 中，不需要额外的 HTTP 请求。

Sass @import 指令语法如下：

注意：包含文件时不需要指定文件后缀，Sass 会自动添加后缀 .scss。此外，也可以导入 CSS 文件。

导入后我们就可以在主文件中使用导入文件等变量。以下实例，导入 variables.scss、colors.scss 和 reset.scss 文件。

```scss
@import "variables";
@import "colors";
@import "reset";
```

如果你不希望将一个 Sass 的代码文件编译到一个 CSS 文件，你可以在文件名的开头添加一个下划线。这将告诉 Sass 不要将其编译到 CSS 文件。

但是，在导入语句中我们不需要添加下划线。

Sass Partials 语法格式：`_filename;`

以下实例创建一个 _colors.scss 的文件，但是不会编译成 _colors.css 文件

```scss
$myPink: #EE82EE;
$myBlue: #4169E1;
$myGreen: #8FBC8F;
```

如果要导入该文件，则不需要使用下划线：

```scss
@import "colors";

body {
  font-family: Helvetica, sans-serif;
  font-size: 18px;
  color: $myBlue;
}
```

> 注意：请不要将带下划线与不带下划线的同名文件放置在同一个目录下，比如，_colors.scss 和 colors.scss 不能同时存在于同一个目录下，否则带下划线的文件将会被忽略。

## Sass @mixin 与 @include

+ `@mixin` 指令允许我们定义一个可以在整个样式表中重复使用的样式。
+ `@include` 指令可以将混入（mixin）引入到文档中。

### 定义 mixin

混入（mixin）通过 `@mixin` 指令来定义。 `@mixin name { property: value; property: value; ... }`
以下实例创建一个名为 "important-text" 的混入：

```scss
@mixin important-text {
  color: red;
  font-size: 25px;
  font-weight: bold;
  border: 1px solid blue;
}
```

> 注意：Sass 的连接符号 `-` 与下划线符号 `_` 是相同的，也就是 `@mixin important-text { }` 与 `@mixin important_text { }` 是一样的混入。

### 使用 mixin

@include 指令可用于包含一混入：

```scss
selector {
  @include mixin-name;
}
```

因此，包含 important-text 混入代码如下：

```scss
.danger {
  @include important-text;
  background-color: green;
}
```

将以上代码转换为 CSS 代码，如下所示：

```scss
.danger {
  color: red;
  font-size: 25px;
  font-weight: bold;
  border: 1px solid blue;
  background-color: green;
}
```

混入中也可以包含混入，如下所示：

```scss
@mixin special-text {
  @include important-text;
  @include link;
  @include special-border;
}
```

### 向混入传递变量

混入可以接收参数。我们可以向混入传递变量。

定义可以接收参数的混入：

```scss
/* 混入接收两个参数 */
@mixin bordered($color, $width) {
  border: $width solid $color;
}

.myArticle {
  @include bordered(blue, 1px);  // 调用混入，并传递两个参数
}

.myNotes {
  @include bordered(red, 2px); // 调用混入，并传递两个参数
}
```

以上实例的混入参数为设置边框的属性 (color 和 width) 。

将以上代码转换为 CSS 代码，如下所示：

```css
.myArticle {
  border: 1px solid blue;
}

.myNotes {
  border: 2px solid red;
}
```

混入的参数也可以定义默认值，语法格式如下：

```scss
@mixin bordered($color: blue, $width: 1px) {
  border: $width solid $color;
}
```

在包含混入时，你只需要传递需要的变量名及其值：

```scss
@mixin sexy-border($color, $width: 1in) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}
p { @include sexy-border(blue); }
h1 { @include sexy-border(blue, 2in); }
```

将以上代码转换为 CSS 代码，如下所示：

```css
p {
  border-color: blue;
  border-width: 1in;
  border-style: dashed;
}

h1 {
  border-color: blue;
  border-width: 2in;
  border-style: dashed;
}
```

有时，不能确定一个混入（mixin）或者一个函数（function）使用多少个参数，这时我们就可以使用 `...` 来设置可变参数。

例如，用于创建盒子阴影（box-shadow）的一个混入（mixin）可以采取任何数量的 box-shadow 作为参数。

```scss
@mixin box-shadow($shadows...) {
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
}

.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}
```

将以上代码转换为 CSS 代码，如下所示：

```css
.shadows {
  -moz-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  -webkit-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
}
```

### 浏览器前缀使用混入

浏览器前缀使用混入也是非常方便的，如下实例：

```scss
@mixin transform($property) {
  -webkit-transform: $property;
  -ms-transform: $property;
  transform: $property;
}

.myBox {
  @include transform(rotate(20deg));
}
```

将以上代码转换为 CSS 代码，如下所示：

```css
.myBox {
  -webkit-transform: rotate(20deg);
  -ms-transform: rotate(20deg);
  transform: rotate(20deg);
}
```

## Sass @extend 与 继承

@extend 指令告诉 Sass 一个选择器的样式从另一选择器继承。

如果一个样式与另外一个样式几乎相同，只有少量的区别，则使用 @extend 就显得很有用。

以下 Sass 实例中，我们创建了一个基本的按钮样式 `.button-basic`，接着我们定义了两个按钮样式 `.button-report` 与 `.button-submit`，它们都继承了 `.button-basic` ，它们主要区别在于背景颜色与字体颜色，其他的样式都是一样的。

```scss
.button-basic  {
  border: none;
  padding: 15px 30px;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
}

.button-report  {
  @extend .button-basic;
  background-color: red;
}

.button-submit  {
  @extend .button-basic;
  background-color: green;
  color: white;
}
```

将以上代码转换为 CSS 代码，如下所示：

```css
.button-basic, .button-report, .button-submit {
  border: none;
  padding: 15px 30px;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
}

.button-report  {
  background-color: red;
}

.button-submit  {
  background-color: green;
  color: white;
}
```

使用 @extend 后，我们在 HTML 按钮标签中就不需要指定多个类 class="button-basic button-report" ，只需要设置 class="button-report" 类就好了。

@extend 很好的体现了代码的复用。

## Sass 函数

Sass 定义了各种类型的函数，这些函数我们可以通过 CSS 语句直接调用。

### Sass 字符串相关函数

Sass String(字符串) 函数用于处理字符串并获取相关信息。

Sass 字符串的起始索引值从 1 开始，记住不是 0。

Sass 的字符串函数：

+ `quote(string)`：给字符串添加引号。
+ `unquote(string)`：移除字符串的引号
+ `str-index(string, substring)`：返回 substring 子字符串第一次在 string 中出现的位置。如果没有匹配到子字符串，则返回 null。
+ `str-insert(string, insert, index)`：在字符串 string 中 index 位置插入 insert。
+ `str-length(string)`：返回字符串的长度。
+ `str-slice(string, start, end)`：从 string 中截取子字符串，通过 start-at 和 end-at 设置始末位置，未指定结束索引值则默认截取到字符串末尾。
+ `to-lower-case(string)`：将字符串转成小写
+ `to-upper-case(string)`：将字符串转成大写
+ `unique-id()`：返回一个无引号的随机字符串作为 id。不过也只能保证在单次的 Sass 编译中确保这个 id 的唯一性。

Sass 数字函数：

+ `abs(number)`：返回一个数值的绝对值。
+ `ceil(number)`：向上取整
+ `comparable(num1, num2)`：返回一个布尔值，判断 num1 与 num2 是否可以进行比较，单位是否相同或可以转换。
+ `floor(number)`：向下取整
+ `max(number...)`：返回最大值
+ `min(number...)`：返回最小值
+ `percentage(number)`：将数字转化为百分比的表达形式。
+ `random()`：返回 0-1 区间内的小数
+ `random(number)`：返回 1 至 number 之间的整数，包括 1 和 number。
+ `round(number)`：返回最接近该数的一个整数，四舍五入。

Sass 列表函数：

+ `append(list, value, [separator])`：将单个值 value 添加到列表尾部。separator 是分隔符，默认会自动侦测，或者指定为逗号（comma）或空格（space）。
+ `index(list, value)`：返回元素 value 在列表中的索引位置。
+ `is-bracketed(list)`：判断列表中是否有中括号
+ `join(list1, list2, [separator, bracketed])`：合并两列表，将列表 list2 添加到列表 list1 的末尾。separator 是分隔符，默认会自动侦测，或者指定为逗号或空格。 bracketed 默认会自动侦测是否有中括号，可以设置为 true 或 false。
+ `length(list)`：返回列表的长度
+ `list-separator(list)`：返回一列表的分隔符类型。可以是空格（"space"）或逗号（"comma"）。
+ `nth(list, n)`：获取第 n 项的值。
+ `set-nth(list, n, value)`：设置列表第 n 项的值为 value。
+ `zip(lists)`：将多个列表按照以相同索引值为一组，重新组成一个新的多维度列表。


Sass [Map 函数](https://www.runoob.com/sass/sass-map-func.html):

+ `map-get(map, key)`：	返回 Map 中 key 所对应的 value。如没有对应的 key，则返回 null 值。
+ `map-has-key(map, key)`：判断 map 是否有对应的 key，存在返回 true，否则返回 false。
+ `map-keys(map)`：	返回 map 中所有的 key 组成的队列。
+ `map-merge(map1, map2)`：合并两个 map 形成一个新的 map 类型，即将 map2 添加到 map1 的尾部
+ `map-remove(map, keys...)`：移除 map 中的 keys，多个 key 使用逗号隔开。
+ `map-values(map)`：返回 map 中所有的 value 并生成一个队列。

Sass [选择器函数](https://www.runoob.com/sass/sass-selector-func.html)：

+ `is-superselector(super, sub)`：比较两个选择器匹配的范围，即判断 super 选择器是否包含了 sub 选择器所匹配的范围，是的话返回 true，否则返回 false。
+ `selector-append(selectors)`：将第二个 (也可以有多个) 添加到第一个选择器的后面。

Sass [Introspection 函数](https://www.runoob.com/sass/sass-introspection-func.html)

Sass [颜色函数](https://www.runoob.com/sass/sass-color-func.html)