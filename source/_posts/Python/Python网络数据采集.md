---
title: Python网络数据采集
tags:
  - Python
  - 爬虫
categories:
  - [Python]
date: 2020-03-24 16:40:49
---


Python网络数据采集笔记

---

## 网络数据采集的基本原理

对网络爬虫通常的想法：

+ 通过网站域名获取 HTML 数据
+ 根据目标信息解析数据
+ 存储目标信息
+ 如果有必要，移动到另一个网页重复这个过程

## Python实现网络连接

使用python获取网页，代码实现

```python
from urllib.request import urlopen
html = urlopen("http://www.baidu.com")
print(html.read())
```

### urllib

urllib 是 Python 的标准库，包含了从网络请求数据，处理cookie，甚至改变像请求头和用户代理这些元数据的函数。

urlopen 用来打开并读取一个从网络获取的远程对象。

> Python 2.x 里的的是 urllib2 库，在 Python 3.x 里，urllib2 改名为 urllib

### BeautifulSoup

BeautifulSoup通过定位 HTML 标签来 格式化和组织复杂的网络信息，用简单易用的 Python 对象为我们展现 XML 结构信息。

Pycharm可以自动导入其他安装可参考该[文档](https://beautifulsoup.readthedocs.io/zh_CN/v4.4.0/#id8)，有关Python虚拟环境可参考该[文章](https://lanqilu.github.io/2019/10/12/Python/配置虚拟环境venv/)

```powershell
pip install beautifulsoup4
```

使用BeautifulSoup

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
html = urlopen("https://lanqilu.github.io/")
bsObj = BeautifulSoup(html.read())
print(bsObj.h1)
```

### 网络连接异常

```python
html = urlopen("https://lanqilu.github.io/")
```

可能发生的异常有两种

+ 网页在服务器上不存在（或者获取页面的时候出现错误）
+ 服务器不存在

第一种异常发生时，程序会返回 HTTP 错误。可以查看[HTTP状态码](https://lanqilu.github.io/2020/03/21/Web/HTTP状态码/)。所有类似情形，urlopen 函数都会抛出“HTTPError”异常

处理方式

```python
from urllib.error import HTTPError
from urllib.request import urlopen

try:
    html = urlopen("https://lanqilu.github.io/2018") # 异常网站 
except HTTPError as e:
    print(e)
    # 返回空值，中断程序，或者执行另一个方案
else:
    print("程序继续")
# 注意：如果你已经在上面异常捕捉那一段代码里返回或中断（break）
# 那么就不需要使用else语句了，这段代码也不会执行
```

如果服务器不存在（URL链接打不开），urlopen 会返回一个 None 对象。

要调用的标签不存在，BeautifulSoup 就会返回 None 对象

```python
from urllib.request import urlopen
from urllib.error import HTTPError
from bs4 import BeautifulSoup

def getTitle(url):
    try:
        html = urlopen(url)
    except HTTPError as e:
        return None
    try:
        bsObj = BeautifulSoup(html.read(), "lxml")
        title = bsObj.body.h1
    except AttributeError as e:
        return None
    return title

title = getTitle("https://www.runoob.com/")
if title is None:
    print("Title could not be found")
else:
    print(title)
```

## HTML解析

属性查找标签的方法，标签组的使用，以及标签解析树的导航过程

网络爬虫可以通过 class 属性的值，轻松地区分出两种不同的标签。

例如：

```html
"<span class="red">Heavens! what a virulent attack!</span>" replied <span class=
"green">the prince</span>, not in the least disconcerted by this reception.
```

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
#%%
# 爬取页面
html = urlopen("http://www.pythonscraping.com/pages/warandpeace.html")
bsObj = BeautifulSoup(html, features="lxml") # features 选择解析器
# 以用 findAll 函数抽取只包含在 <span class="green"></span> 标签里的文字
nameList = bsObj.findAll("span", {"class": "green"})
# 打印输出
for name in nameList:
    print(name.get_text())
```

> `get_text()`
>
> `.get_text() `会把正在处理的 HTML 文档中所有的标签都清除，然后返回 一个只包含文字的字符串。假如你正在处理一个包含许多超链接、段落和标签的大段源代码，那么` .get_text()` 会把这些超链接、段落和标签都清除掉， 只剩下一串不带标签的文字。

### BeautifulSoup的`find()`和`findAll()`

`findAll`函数通过标签的名称和属性来查找标签

```python
findAll(tag, attributes, recursive, text, limit, keywords)
find(tag, attributes, recursive, text, keywords)
```

`tag`标签参数，可以传一个标签的名称或多个标签名称组成的 Python 列表做标签参数

```python
.findAll({"h1","h2","h3","h4","h5","h6"})
```

`attributes`属性参数，是用一个 Python 字典封装一个标签的若干属性和对应的属性值

```python
.findAll("span", {"class":{"green", "red"}})
```

`recursive`递归参数，是一个布尔变量，设置为True，查找标签参数的所有子标签，以及子标签的子标签；设置为 False，就只查找文档的一级标签。（默认值是 True）

`text`文本参数，是用标签的文本内容去匹配，而不是用标签的属性。

```python
#%%
html = urlopen("http://www.pythonscraping.com/pages/warandpeace.html")
bsObj = BeautifulSoup(html, features="lxml")
nameList = bsObj.findAll(text="the prince")
print(len(nameList))  # 7
```

`limit`范围限制参数，只用于`findAll`方法，获得的前几项结果是按照网页上的顺序排序

> `find`等价于`findAll`的`limit`等于1时的情形

`keyword`关键词参数，用于选择具有指定属性的标签

```python
#%%
html = urlopen("http://www.pythonscraping.com/pages/warandpeace.html")
bsObj = BeautifulSoup(html, features="lxml")
allText = bsObj.findAll(id="text")
print(allText[0].get_text())
```

> `class`保留词问题
>
> `bsObj.findAll(class="green")`语法错误，解决方案`bsObj.findAll(class_="green")`或`bsObj.findAll("", {"class":"green"})`

### 其他BeautifulSoup对象

+ `NavigableString`对象：用来表示标签里的文字，不是标签
+ `Comment`对象：用来查找 HTML 文档的注释标签

