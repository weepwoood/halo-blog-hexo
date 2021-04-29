---
title: HTML基本内容
date: 2019-08-09 13:32:50
tags:
- HTML
categories:
- Web
---

HTML是名词，CSS是形容词，JavaScript是动词

---

## HTML 简介

HTML 是用来描述网页的一种语言。

+ HTML 指的是超文本标记语言 (**H**yper **T**ext **M**arkup **L**anguage)
+ HTML 不是一种编程语言，而是一种**标记语言** (markup language)
+ 标记语言是一套**标记标签** (markup tag)
+ HTML 使用**标记标签**来描述网页

#### HTML 标签

+ HTML 标签是由**尖括号**包围的关键词，比如 `<html>`
+ HTML 标签通常是**成对出现**的，比如`<b>`和 `</b>`
+ 标签对中的第一个标签是**开始标签**，第二个标签是**结束标签**
+ 开始和结束标签也被称为**开放标签**和**闭合标签**

#### HTML 文档 = 网页

+ HTML 文档**描述网页**
+ HTML 文档**包含 HTML 标签**和纯文本
+ HTML 文档也被称为**网页**

## HTML基础

#### HTML 标题

HTML 标题（Heading）是通过`<h1>` - `<h6>` 等标签进行定义的。

```HTML
<h1>This is a heading</h1>
<h2>This is a heading</h2>
<h3>This is a heading</h3>
```

#### HTML 段落

HTML 段落是通过 `<p>` 标签进行定义的。

```html
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
```

#### HTML 链接

HTML 链接是通过 `<a> `标签进行定义的。

```html
<a href="http://www.w3school.com.cn">This is a link</a>
```

#### HTML 图像

HTML 图像是通过 `<img>` 标签进行定义的。

```html
<img src="xxxx.jpg" width="104" height="142" />
```



##  HTML 元素

**HTML 文档是由 HTML 元素定义的。**

HTML 元素指的是从开始标签（start tag）到结束标签（end tag）的所有代码。

> 开始标签常被称为开放标签（opening tag），结束标签常称为闭合标签（closing tag）。

**HTML 元素语法**

+ HTML 元素以**开始标签**起始
+ HTML 元素以**结束标签**终止
+ 元素的内容是开始标签与结束标签之间的内容
+ 某些 HTML 元素具有**空内容**（empty content）
+ 空元素**在开始标签中进行关闭**（以开始标签的结束而结束）
+ 大多数 HTML 元素可拥有**属性**
+ 大多数 HTML 元素可以**嵌套**（可以包含其他 HTML 元素）。



**`<p>` 元素：**

```html
<p>This is my first paragraph.</p>
```

这个 `<p>` 元素定义了 HTML 文档中的一个段落。

这个元素拥有一个开始标签 `<p>`，以及一个结束标签 `</p>`。

元素内容是：This is my first paragraph。

**`<body>` 元素：**

```html
<body>
<p>This is my first paragraph.</p>
</body>
```

`<body>` 元素定义了 HTML 文档的主体。

这个元素拥有一个开始标签 `<body>`，以及一个结束标签 `</body>`。

元素内容是另一个 HTML 元素（`<p>`元素）。

**`<html>` 元素：**

```html
<html>

<body>
<p>This is my first paragraph.</p>
</body>

</html>
```

`<html>` 元素定义了整个 HTML 文档。

这个元素拥有一个开始标签 `<html>`，以及一个结束标签 `</html>`。

元素内容是另一个 HTML 元素（body 元素）。

**空的 HTML 元素**

没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的。

`<br>` 就是没有关闭标签的空元素（`<br>` 标签定义换行）。在 XHTML、XML 以及未来版本的 HTML 中，所有元素都必须被关闭。

在开始标签中添加斜杠，比如 `<br />`，是关闭空元素的正确方法，HTML、XHTML 和 XML 都接受这种方式。

即使 `<br>` 在所有浏览器中都是有效的，但使用 `<br />` 其实是更长远的保障。



## HTML 属性

+ HTML 标签可以拥有**属性**。属性提供了有关 HTML 元素的**更多的信息**。
+ 属性总是以名称/值对的形式出现，比如：**name="value"**。
+ 属性总是在 HTML 元素的**开始标签**中规定。
+ 属性值应该始终被包括在引号内。双引号是最常用的，不过使用单引号也没有问题。（在某些个别的情况下，比如属性值本身就含有双引号，那么必须使用单引号）



[HTML 标准属性参考手册](https://www.w3school.com.cn/tags/html_ref_standardattributes.asp)

[完整的 HTML 参考手册](https://www.w3school.com.cn/tags/html_ref_byfunc.asp)

## HTML标题

标题（Heading）是通过 `<h1>` - `<h6> `等标签进行定义的。

`<h1>` 定义最大的标题。`<h6>` 定义最小的标题。

> 浏览器会自动地在标题的前后添加空行。
>
> 默认情况下，HTML 会自动地在块级元素前后添加一个额外的空行，比如段落、标题元素前后。

**标题很重要**

请确保将 HTML heading 标签只用于标题。不要仅仅是为了产生粗体或大号的文本而使用标题。

搜索引擎使用标题为您的网页的结构和内容编制索引。

因为用户可以通过标题来快速浏览您的网页，所以用标题来呈现文档结构是很重要的。

应该将 h1 用作主标题（最重要的），其后是 h2（次重要的），再其次是 h3，以此类推。



### HTML 水平线

`<hr/> `标签在 HTML 页面中创建水平线。可用于分隔内容。

```html
<p>This is a paragraph</p>
<hr/>
<p>This is a paragraph</p>
<hr/>
<p>This is a paragraph</p>
```

> 使用水平线 (`<hr>` 标签) 来分隔文章中的小节是一个办法（但并不是唯一的办法）。

### HTML 注释

可以将注释插入 HTML 代码中，这样可以提高其可读性，使代码更易被人理解。浏览器会忽略注释，也不会显示它们。

**注释标签**

```html
<!-- This is a comment -->
```

> 开始括号之后（左边的括号）需要紧跟一个叹号，结束括号之前（右边的括号）不需要。
>
> 合理地使用注释可以对未来的代码编辑工作产生帮助

**条件注释**

```html
<!--[if IE 8]>
    .... some HTML here ....
<![endif]-->
```
> 条件注释定义只有 Internet Explorer 执行的 HTML 标签。

## HTML 段落

段落是通过 `<p>` 标签定义的。

> 浏览器会自动地在段落的前后添加空行。（`<p>` 是块级元素）
>
> 使用空的段落标记` <p>``</p> `去插入一个空行是个坏习惯。用 `<br/>` 标签代替它！
>
> 不要用 `<br/>` 标签去创建列表

### HTML 折行

不产生一个新段落的情况下进行换行（新行），请使用 `<br/>` 标签

```HTML
<p>This is<br />a para<br />graph with line breaks</p>
```

> `<br/>` 元素是一个空的 HTML 元素。由于关闭标签没有任何意义，因此它没有结束标签。
>
> 无法通过在 HTML 代码中添加额外的空格或换行来改变输出的效果
>
> 所有连续的空格或空行都会被算作一个空格

## HTML 样式

`style` 属性用于改变 HTML 元素的样式。

## 不赞成使用的标签和属性

在 HTML 4 中，有若干的标签和属性是被废弃的。被废弃（Deprecated）的意思是在未来版本的 HTML 和 XHTML 中将不支持这些标签和属性。

这里传达的信息很明确：请避免使用这些被废弃的标签和属性！

### 应该避免使用下面这些标签和属性：

| 标签                     | 描述               |
| :----------------------- | :----------------- |
| `<center>`               | 定义居中的内容。   |
| `<font>` 和` <basefont>` | 定义 HTML 字体。   |
| `<s>` 和 `<strike>`      | 定义删除线文本     |
| `<u>`                    | 定义下划线文本     |
| **属性**                 | **描述**           |
| align                    | 定义文本的对齐方式 |
| bgcolor                  | 定义背景颜色       |
| color                    | 定义文本颜色       |

对于以上这些标签和属性：请使用样式代替！



### 背景颜色

`background-color` 属性为元素定义了背景颜色

```html
<html>

<body style="background-color:black">
<h2 style="background-color:red">This is a heading</h2>
<p style="background-color:green">This is a paragraph.</p>
</body>

</html>
```

### 字体、颜色和尺寸

`font-family`、`color` 以及` font-size` 属性分别定义元素中文本的字体系列、颜色和字体尺寸

```html
<html>

<body>
<h1 style="font-family:verdana">A heading</h1>
<p style="font-family:arial;color:red;font-size:20px;">A paragraph.</p>
</body>

</html>
```

### 文本对齐

`text-align `属性规定了元素中文本的水平对齐方式

```html
<html>

<body>
<h1 style="text-align:center">This is a heading</h1>
<p>The heading above is aligned to the center of this page.</p>
</body>

</html>
```

## HTML文本格式化

### 文本格式化标签

| 标签   | 描述    |
| :------------| :------------------------------------ |
| `<b>`   | 定义粗体文本。  |
| `<big>`    | 定义大号字。     |
| `<em>`   | 定义着重文字。  |
| `<i>`  | 定义斜体字。    |
| `<small>` | 定义小号字。 |
| `<strong>` | 定义加重语气。 |
| `<sub>` | 定义下标字。 |
| `<sup>` | 定义上标字。 |
| `<ins>` | 定义插入字。 |
| `<del>` | 定义删除字。 |

### “计算机输出”标签

| 标签     | 描述               |
| :------- | :----------------- |
| `<code>` | 定义计算机代码。   |
| `<kdd>`  | 定义键盘码。       |
| `<samp>` | 定义计算机代码样本 |
| `<tt>`   | 定义打字机代码。   |
| `<var>`  | 定义变量。         |
| `<pre>`  | 定义预格式文本。   |

### 引用、引用和术语定义

| 标签           | 描述               |
| :------------- | :----------------- |
| `<abbr>`       | 定义缩写。         |
| `<acronym>`    | 定义首字母缩写。   |
| `<address>`    | 定义地址。         |
| `<bdo>`        | 定义文字方向。     |
| `<blockquote>` | 定义长的引用。     |
| `<q>`          | 定义短的引用语     |
| `<cite>`       | 定义引用、引证。   |
| `<dfn>`        | 定义一个定义项目。 |

## [HTML 引用](https://www.w3school.com.cn/html/html_quotation_elements.asp)

## [HTML 计算机代码](https://www.w3school.com.cn/html/html_computercode_elements.asp)

## HTML CSS

### 外部样式表

当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观

### 内部样式表

当单个文件需要特别样式时，就可以使用内部样式表。你可以在 head 部分通过 `<style>` 标签定义内部样式表。

### 内联样式

当特殊的样式需要应用到个别元素时，就可以使用内联样式。 使用内联样式的方法是在相关的标签中使用样式属性。样式属性可以包含任何 CSS 属性。以下实例显示出如何改变段落的颜色和左外边距。

## HTML 链接

### 超链接

超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。

当您把鼠标指针移动到网页中的某个链接上时，箭头会变为一只小手。

我们通过使用 `<a> `标签在 HTML 中创建链接。

有两种使用 `<a> `标签的方式：

+ 通过使用 `href `属性 - 创建指向另一个文档的链接
+ 通过使用 `name` 属性 - 创建文档内的书签

### 链接语法

```html
<a href="http://www.w3school.com.cn/">Visit W3School</a>
```

### target 属性

使用 Target 属性，你可以定义被链接的文档在何处显示。

```html
<a href="http://www.w3school.com.cn/" target="_blank">Visit W3School!</a>
<!--如果把链接的 target 属性设置为 "_blank"，该链接会在新窗口中打开。 -->
```

### name 属性

name 属性规定锚（anchor）的名称

```html
<a name="label">锚（显示在页面上的文本）</a>
```

<a href="http://www.w3school.com.cn/html/html_links.asp#tips">实例-锚</a>

> 假如浏览器找不到已定义的命名锚，那么就会定位到文档的顶端。不会有错误发生。

## [HTML 图像](https://www.w3school.com.cn/html/html_images.asp)

### 图像标签（`<img>`）和源属性（`Src`）

```html
<img src="url" />
```

### 替换文本属性（`Alt`）

```html
<img src="boat.gif" alt="Big Boat">
```

在浏览器无法载入图像时，替换文本属性告诉读者她们失去的信息

## [HTML 表格](https://www.w3school.com.cn/html/html_tables.asp)



---

```graphLR
A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
```

    <table>
        <tr>
            <th rowspan="2">值班人员</th>
            <th>星期一</th>
            <th>星期二</th>
            <th>星期三</th>
        </tr>
        <tr>
            <td>李强</td>
            <td>张明</td>
            <td>王平</td>
        </tr>
    </table>

<table>
    <tr>
        <th rowspan="2">值班人员</th>
        <th>星期一</th>
        <th>星期二</th>
        <th>星期三</th>
    </tr>
    <tr>
        <td>李强</td>
        <td>张明</td>
        <td>王平</td>
    </tr>
</table>

```
<table>
    <tr>
        <th rowspan="2">值班人员</th>
        <th>星期一</th>
        <th>星期二</th>
        <th>星期三</th>
    </tr>
    <tr>
        <td>李强</td>
        <td>张明</td>
        <td>王平</td>
    </tr>
</table>
```

****

二个注脚[^footnote2]的样例。



[^footnote2]:123

## [HTML 列表](https://www.w3school.com.cn/html/html_lists.asp)

## [HTML块](https://www.w3school.com.cn/html/html_blocks.asp)

### HTML 块元素

块级元素在浏览器显示时，通常会以新行来开始（和结束）。

### HTML` <div> `元素

HTML `<div> `元素是块级元素，它是可用于组合其他 HTML 元素的容器。

`<div> `元素没有特定的含义。除此之外，由于它属于块级元素，浏览器会在其前后显示折行。

如果与 CSS 一同使用，`<div>` 元素可用于对大的内容块设置样式属性。

`<div>` 元素的另一个常见的用途是文档布局。它取代了使用表格定义布局的老式方法。使用 `<table>` 元素进行文档布局不是表格的正确用法。`<table>` 元素的作用是显示表格化的数据。

### HTML 内联元素

内联元素在显示时通常不会以新行开始。

HTML `<span>` 元素是内联元素，可用作文本的容器。

`<span>` 元素也没有特定的含义。

当与 CSS 一同使用时，`<span> `元素可用于为部分文本设置样式属性。

## [HTML类](https://www.w3school.com.cn/html/html_classes.asp)

对 HTML 进行分类（设置类），使我们能够为元素的类定义 CSS 样式。

为相同的类设置相同的样式，或者为不同的类设置不同的样式。

### 分类块级元素

HTML `<div>` 元素是*块级元素*。它能够用作其他 HTML 元素的容器。

设置 `<div>` 元素的类，使我们能够为相同的` <div> `元素设置相同的类：

```html
<!DOCTYPE html>
<html>
<head>
<style>
.cities {
    background-color:black;
    color:white;
    margin:20px;
    padding:20px;
} 
</style>
</head>

<body>

<div class="cities">
<h2>London</h2>
<p>London is the capital city of England. 
It is the most populous city in the United Kingdom, 
with a metropolitan area of over 13 million inhabitants.</p>
</div>

<div class="cities">
<h2>Paris</h2>
<p>Paris is the capital and most populous city of France.</p>
</div>

<div class="cities">
<h2>Tokyo</h2>
<p>Tokyo is the capital of Japan, the center of the Greater Tokyo Area,
and the most populous metropolitan area in the world.</p>
</div>

</body>
</html>
```

### 分类行内元素

HTML `<span>` 元素是行内元素，能够用作文本的容器。

设置` <span>` 元素的类，能够为相同的` <span> `元素设置相同的样式。

## [HTML 布局](https://www.w3school.com.cn/html/html_layout.asp)

### 使用 `<div> `元素的 HTML 布局

### 使用 HTML5 的网站布局

| HTML5 | 语义元素 |
| ------- | -------------------------|
| header  | 定义文档或节的页眉          |
| nav     | 定义导航链接的容器          |
| section | 定义文档中的节             |
| article | 定义独立的自包含文章        |
| aside   | 定义内容之外的内容（比如侧栏）|
| footer  | 定义文档或节的页脚          |
| details | 定义额外的细节             |
| summary | 定义 details 元素的标题    |




