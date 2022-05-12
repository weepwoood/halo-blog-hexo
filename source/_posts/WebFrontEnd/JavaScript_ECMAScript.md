---
title: JavaScript之ECMAScript
date: 2019-08-09 11:05:50
tags:
- JavaScript
categories:
- Web
---

[JavaScript](https://www.liaoxuefeng.com/wiki/1022910821149312) 核心（ECMAScript），参考书籍《JavaScript 高级程序设计》第三版、第四版

---

## 什么是JavaScript

虽然 JavaScript 和 ECMAScript 基本上是同义词，但 JavaScript 远远不限于 ECMA-262 所定义的那样。 完整的 JavaScript 实现包含以下几个部分：

+ 核心（ECMAScript）
+ 文档对象模型（DOM） 
+ 浏览器对象模型（BOM）

### ECMAScript

ECMAScript，即 ECMA-262 定义的语言，并不局限于 Web 浏览器。事实上，这门语言没有输入和 输出之类的方法。ECMA-262 将这门语言作为一个基准来定义，以便在它之上再构建更稳健的脚本语言。 Web 浏览器只是 ECMAScript 实现可能存在的一种宿主环境（host environment）。宿主环境提供 ECMAScript 的基准实现和与环境自身交互必需的扩展。扩展（比如 DOM）使用 ECMAScript 核心类型和语法，提供特定于环境的额外功能。其他宿主环境还有服务器端 JavaScript 平台 Node.js 和被淘汰 的 Adobe Flash。

如果不涉及浏览器的话，ECMA-262 到底定义了什么？在基本的层面，它描述这门语言的如下部分：语法、类型、语句、关键字、保留字、操作符、全局对象

ECMAScript 只是对实现这个规范描述的所有方面的一门语言的称呼。JavaScript 实现了 ECMAScript，而 Adobe ActionScript 同样也实现了 ECMAScript。

### DOM

文档对象模型（DOM，Document Object Model）是一个应用编程接口（API），用于在 HTML 中使用扩展的 XML。DOM 将整个页面抽象为一组分层节点。HTML 或 XML 页面的每个组成部分都是一种节点，包含不同的数据。比如下面的 HTML 页面：

```html
<html> 
    <head> 
        <title>Sample Page</title> 
    </head> 
    <body> 
        <p> Hello World!</p> 
    </body> 
</html> 
```

这些代码通过 DOM 可以表示为一组分层节点，如下所示。

![博客图片](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-f@master/博客图片.6jl0kvy9vj80.svg)

DOM 通过创建表示文档的树，让开发者可以随心所欲地控制网页的内容和结构。使用 DOM API，可以轻松地删除、添加、替换、修改节点。

###  BOM

浏览器对象模型（BOM） API，用于支持访问和操作浏览器的窗口。使用 BOM，开发者可以操控浏览器显示页面之外的部分。而 BOM 真正独一无二的地方，当然也是问题最多的地方，就是它是唯一一个没有相关标准的 JavaScript 实现。HTML5 改变了这个局面，这个版本的 HTML 以正式规范的形式涵盖了尽可能多的 BOM 特性。由于 HTML5 的出现，之前很多与 BOM 有关的问题都迎刃而解了。

总体来说，BOM 主要针对浏览器窗口和子窗口（frame），不过人们通常会把任何特定于浏览器的扩展都归在 BOM 的范畴内。比如，下面就是这样一些扩展：

+ 弹出新浏览器窗口的能力；
+ 移动、缩放和关闭浏览器窗口的能力； 
+ `navigator` 对象，提供关于浏览器的详尽信息； 
+ `location` 对象，提供浏览器加载页面的详尽信息；
+ `screen` 对象，提供关于用户屏幕分辨率的详尽信息； 
+ `performance` 对象，提供浏览器内存占用、导航行为和时间统计的详尽信息；
+ 对 cookie 的支持；
+ 其他自定义对象，如 XMLHttpRequest 和 IE 的 ActiveXObject。

因为在很长时间内都没有标准，所以每个浏览器实现的都是自己的 BOM。有一些所谓的事实标准， 比如对于 `window` 对象和 `navigator` 对象，每个浏览器都会给它们定义自己的属性和方法。现在有了 HTML5，BOM 的实现细节应该会日趋一致。

## HTML 中的 JavaScript

### `<script>` 元素

将 JavaScript 插入 HTML 的主要方法是使用 `<script>` 元素。`<script>` 元素有下列 8 个属性：

+ `src`：可选。表示包含要执行代码的外部文件。
+ `async`：可选。异步，表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或等待加载其他脚本。只对外部脚本文件有效。
+ `defer`：可选。延迟，表示脚本可以延迟到文档完全被解析和显示之后再执行。只对外部脚本文件有效。IE7 及更早版本对嵌入脚本也支持这个属性。（属性设置为`defer="defer"`）
+ `charset`：可选。表示通过 `src` 属性指定的代码的字符集。由于大多数浏览器会忽略它的值，因此这个属性很少使用。
+ `crossorigin`：可选。配置相关请求的 CORS（跨源资源共享）设置。默认不使用 CORS。`crossorigin = "anonymous"` 配置文件请求不必设置凭据标志。`crossorigin = "use-credentials"` 设置凭据标志，意味着出站请求会包含凭据。
+ `integrity`：可选。允许比对接收到的资源和指定的加密签名以验证子资源完整性（SRI，Subresource Integrity）。如果接收到的资源的签名与这个属性指定的签名不匹配，则页面会报错，脚本不会执行。这个属性可以用于确保内容分发网络（CDN，Content Delivery Network）不会提供恶意内容。
+ `language`：已废弃。原来用于表示编写代码使用的脚本语言（如 JavaScript、JavaScript1.2 或 VBScript）。大多数浏览器会忽略这个属性，因此也没有必要再用了。
+ `type`：可选。可以看成是 `language ` 替代属性；表示编写代码使用的脚本语言的内容类型（也称为 MIME 类型）。按照惯例，这个值始终都是 `"text/javascript"`，尽管 `"text/javascript"` 和 `"text/ecmascript"` 都已经废弃了。JavaScript 文件的 MIME 类型通常是 `"application/x-javascript"`，不过给 `type` 属性这个值有可能导致脚本被忽略。在非 IE 的浏览器中有效的其他值还有
   `"application/javascript"` 和 `"application/ecmascript"`。如果这个值是 `module`，则代码会被当成 ES6 模块，而且只有这时候代码中才能出现 `import` 和 `export` 关键字。

使用 `<script>` 的方式有两种：通过它直接在网页中嵌入 JavaScript 代码，以及通过它在网页中包含外部 JavaScript 文件。

方式一：直接在页面中嵌入 JavaScript 代码

```html
<html>
<head>
  <script>
    console.log('Hello, world');
  </script>
</head>
<body>
  Hello,world!
</body>
</html>
```
由 `<script> ... </script>` 包含的代码就是 JavaScript 代码，它将直接被浏览器执行。包含在 `<script>` 内的代码会被从上到下解释。在上面的例子中，被解释的是一个函数定义，并且该函数会被保存在解释器环境中。在 `<script>` 元素中的代码被计算完成之前，页面的其余内容不会被加载，也不会被显示。

在使用行内 JavaScript 代码时，要注意代码中不能出现字符串 `</script>`。比如下面这段代码：

```html
<script> 
    function sayScript() { 
        console.log("</script>"); 
    } 
</script>
```

浏览器解析行内脚本的方式决定了它在看到字符串时，会将其当成结束的标签。想避免这个问题，只需要转义字符 `\` 即可：

```html
<script> 
    function sayScript() { 
        console.log("<\/script>"); 
    } 
</script>
```

---

方式二：把 JavaScript 代码放到一个单独的 .js 文件，然后在 HTML 中通过 `<script src="..."></script>` 引入这个文件

```html
<html>
<head>
  <script src="/static/js/abc.js"></script>
</head>
<body>
  Hello,world!
</body>
</html>
```

这样，`/static/js/abc.js` 就会被浏览器执行。

> 带有 `src` 属性的 `<script>` 元素不应该在其`<script>`和`</script>`标签之间再包含额外的 JavaScript 代码。如果包含了嵌入的代码，则只会下载并执行外部脚本文件，嵌入的代码会被忽略。

与解释行内 JavaScript 一样，在解释外部 JavaScript 文件时，页面也会阻塞。（阻塞时间也包含下载文件的时间）。

在 XHTML 文档中，可以忽略结束标签，比如：

```xhtml
<script src="example.js"/>
```

以上语法不能在 HTML 文件中使用，因为它是无效的 HTML，有些浏览器不能正常处理，比如 IE。

> 按照惯例，外部 JavaScript 文件的扩展名是 .js。这不是必需的，因为浏览器不会检 查所包含 JavaScript 文件的扩展名。这就为使用服务器端脚本语言动态生成 JavaScript 代 码，或者在浏览器中将 JavaScript扩展语言（如 TypeScript，或 React 的 JSX）转译为JavaScript 提供了可能性。不过要注意，服务器经常会根据文件扩展来确定响应的正确 MIME 类型。 如果不打算使用.js 扩展名，一定要确保服务器能返回正确的 MIME 类型。

`<script>` 元素的一个最为强大、同时也备受争议的特性是，它可以包含来自外部域的 JavaScript 文件。跟 `<img>` 元素很像，`<script>` 元素的 `src` 属性可以是一个完整的 URL，而且这个 URL 指向的资源可以跟包含它的 HTML 页面不在同一个域中，比如这个例子：

```html
<script src="http://www.somewhere.com/afile.js"></script> 
```

浏览器在解析这个资源时，会向 `src` 属性指定的路径发送一个 GET 请求，以取得相应资源，假定是一个 JavaScript 文件。这个初始的请求不受浏览器同源策略限制，但返回并被执行的 JavaScript 则受限制。当然，这个请求仍然受父页面 HTTP/HTTPS 协议的限制。

来自外部域的代码会被当成加载它的页面的一部分来加载和解释。这个能力可以让我们通过不同的域分发 JavaScript。不过，引用了放在别人服务器上的 JavaScript 文件时要格外小心，因为恶意的程序员随时可能替换这个文件。在包含外部域的 JavaScript 文件时，要确保该域是自己所有的，或者该域是一个可信的来源。`<script>` 标签的 `integrity` 属性是防范这种问题的一个武器，但这个属性也不是所有浏览器都支持。

不管包含的是什么代码，浏览器都会按照 `<script>` 在页面中出现的顺序依次解释它们，前提是它们没有使用 `defer` 和 `async` 属性。第二个 `<script>` 元素的代码必须在第一个 `<script>` 元素的代码解释完毕才能开始解释，第三个则必须等第二个解释完，以此类推。

#### 标签位置

过去，所有 `<script>` 元素都被放在页面的 `<head>` 标签内，如下面的例子所示：

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <title>Example HTML Page</title> 
    <script src="example1.js"></script> 
    <script src="example2.js"></script> 
  </head> 
  <body> 
    <!-- 这里是页面内容 --> 
  </body> 
</html>
```

这种做法的主要目的是把外部的 CSS 和 JavaScript 文件都集中放到一起。不过，把所有 JavaScript 文件都放在 `<head>` 里，也就意味着必须把所有 JavaScript 代码都下载、解析和解释完成后，才能开始渲染页面（页面在浏览器解析到 `<body>` 的起始标签时开始渲染）。对于需要很多 JavaScript 的页面，这会导致页面渲染的明显延迟，在此期间浏览器窗口完全空白。为解决这个问题，现代 Web 应用程序通常将所有 JavaScript 引用放在 `<body>` 元素中的页面内容后面，如下面的例子所示：

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <title>Example HTML Page</title> 
  </head> 
  <body> 
    <!-- 这里是页面内容 --> 
    <script src="example1.js"></script> 
    <script src="example2.js"></script> 
  </body> 
</html> 
```

这样一来，页面会在处理 JavaScript 代码之前完全渲染页面。用户会感觉页面加载更快了，因为浏览器显示空白页面的时间短了。

#### 推迟执行脚本

HTML 4.01 为 `<script>` 元素定义了一个叫 `defer` 的属性。这个属性表示脚本在执行的时候不会改变页面的结构。也就是说，脚本会被延迟到整个页面都解析完毕后再运行。因此，在 `<script>` 元素上设置 `defer` 属性，相当于告诉浏览器立即下载，但延迟执行。

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <title>Example HTML Page</title> 
    <script defer src="example1.js"></script> 
    <script defer src="example2.js"></script> 
  </head> 
  <body> 
    <!-- 这里是页面内容 --> 
  </body> 
</html> 
```

虽然这个例子中的 `<script>` 元素包含在页面的 `<head>` 中，但它们会在浏览器解析到结束的 `</html>` 标签后才会执行。HTML5 规范要求脚本应该按照它们出现的顺序执行，因此第一个推迟的脚本会在第二个推迟的脚本之前执行，而且两者都会在 `DOMContentLoaded` 事件之前执行。不过在实际当中，推迟执行的脚本不一定总会按顺序执行或者在 `DOMContentLoaded` 事件之前执行，因此最好只包含一个这样的脚本。

如前所述，`defer` 属性只对外部脚本文件才有效。这是 HTML5 中明确规定的，因此支持 HTML5 的浏览器会忽略行内脚本的 defer 属性。IE4~7 展示出的都是旧的行为，IE8 及更高版本则支持 HTML5 定义的行为。

对 `defer` 属性的支持是从 IE4、Firefox 3.5、Safari 5 和 Chrome 7 开始的。其他所有浏览器则会忽略这个属性，按照通常的做法来处理脚本。考虑到这一点，还是把要推迟执行的脚本放在页面底部比较好。

> 对于 XHTML 文档，指定 `defer` 属性时应该写成 `defer="defer"`。

#### 异步执行脚本

HTML5 为 `<script>` 元素定义了 `async` 属性。从改变脚本处理方式上看，`async` 属性与 `defer` 类似。当然，它们两者也都只适用于外部脚本，都会告诉浏览器立即开始下载。不过，与 `defer` 不同的是，标记为 `async` 的脚本并不保证能按照它们出现的次序执行，比如：

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <title>Example HTML Page</title> 
    <script async src="example1.js"></script> 
    <script async src="example2.js"></script> 
  </head> 
  <body> 
    <!-- 这里是页面内容 --> 
  </body> 
</html> 
```

在这个例子中，第二个脚本可能先于第一个脚本执行。因此，重点在于它们之间没有依赖关系。给脚本添加 `async` 属性的目的是告诉浏览器，不必等脚本下载和执行完后再加载页面，同样也不必等到该异步脚本下载和执行后再加载其他脚本。正因为如此，异步脚本不应该在加载期间修改 DOM。

异步脚本保证会在页面的 `load` 事件前执行，但可能会在 `DOMContentLoaded` 之前或之后。Firefox 3.6、Safari 5 和 Chrome 7 支持异步脚本。使用 `async` 也会告诉页面你不会使用 `document.write`，不过好的 Web 开发实践根本就不推荐使用这个方法。

> 对于 XHTML 文档，指定 `async` 属性时应该写成 `async="async"`。

#### 动态加载脚本

除了 `<script>` 标签，还有其他方式可以加载脚本。因为 JavaScript 可以使用 DOM API，所以通过向 DOM 中动态添加 `script` 元素同样可以加载指定的脚本。只要创建一个 `script` 元素并将其添加到 DOM 即可。

```javascript
let script = document.createElement('script'); 
script.src = 'gibberish.js'; 
document.head.appendChild(script); 
```

当然，在把 HTMLElement 元素添加到 DOM 且执行到这段代码之前不会发送请求。默认情况下，以这种方式创建的 `<script>` 元素是以异步方式加载的，相当于添加了 `async` 属性。不过这样做可能会有问题，因为所有浏览器都支持 `createElement()` 方法，但不是所有浏览器都支持 `async` 属性。因此，如果要统一动态脚本的加载行为，可以明确将其设置为同步加载：

```javascript
let script = document.createElement('script'); 
script.src = 'gibberish.js'; 
script.async = false; 
document.head.appendChild(script); 
```

以这种方式获取的资源对浏览器预加载器是不可见的。这会严重影响它们在资源获取队列中的优先级。根据应用程序的工作方式以及怎么使用，这种方式可能会严重影响性能。要想让预加载器知道这些动态请求文件的存在，可以在文档头部显式声明它们：

```html
<link rel="preload" href="gibberish.js">
```

#### XHTML 中的变化

略

#### 废弃的语法

略

### 行内代码与外部文件

虽然可以直接在 HTML 文件中嵌入 JavaScript 代码，但通常认为最佳实践是尽可能将 JavaScript 代码放在外部文件中。不过这个最佳实践并不是明确的强制性规则。推荐使用外部文件的理由如下。

+ 可维护性。JavaScript 代码如果分散到很多 HTML 页面，会导致维护困难。而用一个目录保存所有 JavaScript 文件，则更容易维护，这样开发者就可以独立于使用它们的 HTML 页面来编辑代码。
+ 缓存。浏览器会根据特定的设置缓存所有外部链接的 JavaScript 文件，这意味着如果两个页面都用到同一个文件，则该文件只需下载一次。这最终意味着页面加载更快。
+ 适应未来。通过把 JavaScript 放到外部文件中，就不必考虑用 XHTML 或前面提到的注释黑科技。 包含外部 JavaScript 文件的语法在 HTML 和 XHTML 中是一样的。

在配置浏览器请求外部文件时，要重点考虑的一点是它们会占用多少带宽。在 SPDY/HTTP2 中，预请求的消耗已显著降低，以轻量、独立 JavaScript 组件形式向客户端送达脚本更具优势。

比如，第一个页面包含如下脚本：

```html
<script src="mainA.js"></script> 
<script src="component1.js"></script> 
<script src="component2.js"></script> 
<script src="component3.js"></script>
```

后续页面可能包含如下脚本：

```html
<script src="mainB.js"></script> 
<script src="component3.js"></script> 
<script src="component4.js"></script> 
<script src="component5.js"></script> 
```

在初次请求时，如果浏览器支持 SPDY/HTTP2，就可以从同一个地方取得一批文件，并将它们逐个放到浏览器缓存中。从浏览器角度看，通过 SPDY/HTTP2 获取所有这些独立的资源与获取一个大 JavaScript 文件的延迟差不多。

在第二个页面请求时，由于你已经把应用程序切割成了轻量可缓存的文件，第二个页面也依赖的某些组件此时已经存在于浏览器缓存中了。

当然，这里假设浏览器支持 SPDY/HTTP2，只有比较新的浏览器才满足。如果你还想支持那些比较老的浏览器，可能还是用一个大文件更合适。

### 文档模式

IE5.5 发明了文档模式的概念，即可以使用 `doctype` 切换文档模式。最初的文档模式有两种：混杂模式（quirks mode）和标准模式（standards mode）。前者让 IE 像 IE5 一样（支持一些非标准的特性），后者让 IE 具有兼容标准的行为。虽然这两种模式的主要区别只体现在通过 CSS 渲染的内容方面，但对 JavaScript 也有一些关联影响，或称为副作用。

IE 初次支持文档模式切换以后，其他浏览器也跟着实现了。随着浏览器的普遍实现，又出现了第三种文档模式：准标准模式（almost standards mode）。这种模式下的浏览器支持很多标准的特性，但是没有标准规定得那么严格。主要区别在于如何对待图片元素周围的空白（在表格中使用图片时最明显）。

混杂模式在所有浏览器中都以省略文档开头的 `doctype` 声明作为开关。这种约定并不合理，因为混杂模式在不同浏览器中的差异非常大，不使用黑科技基本上就没有浏览器一致性可言。

标准模式通过下列几种文档类型声明开启：

```html
<!-- HTML 4.01 Strict --> 
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" 
"http://www.w3.org/TR/html4/strict.dtd">
```

```xhtml
<!-- XHTML 1.0 Strict --> 
<!DOCTYPE html PUBLIC 
"-//W3C//DTD XHTML 1.0 Strict//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> 
```

```html
<!-- HTML5 --> 
<!DOCTYPE html> 
```

准标准模式通过过渡性文档类型（Transitional）和框架集文档类型（Frameset）来触发：

```html
<!-- HTML 4.01 Transitional --> 
<!DOCTYPE HTML PUBLIC 
"-//W3C//DTD HTML 4.01 Transitional//EN" 
"http://www.w3.org/TR/html4/loose.dtd"> 
```

```html
<!-- HTML 4.01 Frameset --> 
<!DOCTYPE HTML PUBLIC 
"-//W3C//DTD HTML 4.01 Frameset//EN" 
"http://www.w3.org/TR/html4/frameset.dtd">
```

```xhtml
<!-- XHTML 1.0 Transitional --> 
<!DOCTYPE html PUBLIC 
"-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

```xhtml
<!-- XHTML 1.0 Frameset --> 
<!DOCTYPE html PUBLIC 
"-//W3C//DTD XHTML 1.0 Frameset//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd"> 
```

准标准模式与标准模式非常接近，很少需要区分。人们在说到“标准模式”时，可能指其中任何一个。而对文档模式的检测也不会区分它们。

### `<noscript>` 元素

针对早期浏览器不支持 JavaScript 的问题，需要一个页面优雅降级的处理方案。最终，`<noscript>` 元素出现，被用于给不支持 JavaScript 的浏览器提供替代内容。虽然如今的浏览器已经 100% 支持 JavaScript，但对于禁用 JavaScript 的浏览器来说，这个元素仍然有它的用处。

`<noscript>` 元素可以包含任何可以出现在 `<body>` 中的 HTML 元素，`<script>` 除外。在下列两种情况下，浏览器将显示包含在 `<noscript>` 中的内容：

+ 浏览器不支持脚本；
+ 浏览器对脚本的支持被关闭。

任何一个条件被满足，包含在 `<noscript>` 中的内容就会被渲染。否则，浏览器不会渲染 `<noscript>` 中的内容。

下面是一个例子：

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <title>Example HTML Page</title> 
    <script defer="defer" src="example1.js"></script> 
    <script defer="defer" src="example2.js"></script> 
  </head> 
  <body> 
    <noscript> 
      <p>This page requires a JavaScript-enabled browser.</p> 
    </noscript> 
  </body> 
</html> 
```

这个例子是在脚本不可用时让浏览器显示一段话。如果浏览器支持脚本，则用户永远不会看到它。

### 小结

JavaScript 是通过 `<script>` 元素插入到 HTML 页面中的。这个元素可用于把 JavaScript 代码嵌入到 HTML 页面中，跟其他标记混合在一起，也可用于引入保存在外部文件中的 JavaScript。本章的重点可以总结如下。

+ 要包含外部 JavaScript 文件，必须将 `src` 属性设置为要包含文件的 URL。文件可以跟网页在同一台服务器上，也可以位于完全不同的域。
+ 所有 `<script>` 元素会依照它们在网页中出现的次序被解释。在不使用 `defer` 和 `async` 属性的情况下，包含在 `<script>` 元素中的代码必须严格按次序解释。
+ 对不推迟执行的脚本，浏览器必须解释完位于 `<script>` 元素中的代码，然后才能继续渲染页面的剩余部分。为此，通常应该把 `<script>` 元素放到页面末尾，介于主内容之后及 `</body>` 标签之前。
+ 可以使用 `defer` 属性把脚本推迟到文档渲染完毕后再执行。推迟的脚本原则上按照它们被列出的次序执行。
+ 可以使用 `async` 属性表示脚本不需要等待其他脚本，同时也不阻塞文档渲染，即异步加载。异步脚本不能保证按照它们在页面中出现的次序执行。
+ 通过使用 `<noscript>` 元素，可以指定在浏览器不支持脚本时显示的内容。如果浏览器支持并启用脚本，则 `<noscript>` 元素中的任何内容都不会被渲染。

## 语言基础

本章内容：语法、数据类型、流控制语句、理解函数

任何语言的核心所描述的都是这门语言在最基本的层面上如何工作，涉及语法、操作符、数据类型以及内置功能，在此基础之上才可以构建复杂的解决方案。如前所述，ECMA-262 以一个名为 ECMAScript 的伪语言的形式，定义了 JavaScript 的所有这些方面。

ECMA-262 第 5 版（ES5）定义的 ECMAScript，是目前为止实现得最为广泛（即受浏览器支持最好）的一个版本。第 6 版（ES6）在浏览器中的实现（即受支持）程度次之。到 2017 年底，大多数主流浏览器几乎或全部实现了这一版的规范。为此，本章接下来的内容主要基于 ECMAScript 第 6 版。

### 语法

ECMAScript 的语法很大程度上借鉴了 C 语言和其他类 C 语言，如 Java 和 Perl。熟悉这些语言的开发者，应该很容易理解 ECMAScript 宽松的语法。


#### 区分大小写

首先要知道的是，ECMAScript 中一切都区分大小写。无论是变量、函数名还是操作符，都区分大小写。换句话说，变量 `test` 和变量 `Test` 是两个不同的变量。类似地，`typeof` 不能作为函数名，因为它是一个关键字。但 `Typeof` 是一个完全有效的函数名。

#### 标识符

所谓标识符，就是变量、函数、属性或函数参数的名称。标识符可以由一或多个下列字符组成：

+ 第一个字符必须是一个字母、下划线（`_`）或美元符号（`$`）；
+ 剩下的其他字符可以是字母、下划线、美元符号或数字。

标识符中的字母可以是扩展 ASCII（Extended ASCII）中的字母，也可以是 Unicode 的字母字符，如 À 和 Æ（但不推荐使用）。

按照惯例，ECMAScript 标识符使用驼峰大小写形式，即第一个单词的首字母小写，后面每个单词的首字母大写，如：`firstSecond`、`myCar`、`doSomethingImportant`。

虽然这种写法并不是强制性的，但因为这种形式跟 ECMAScript 内置函数和对象的命名方式一致，所以算是最佳实践。

> 关键字、保留字、`true`、`false` 和 `null` 不能作为标识符。

#### 注释

ECMAScript 采用 C 语言风格的注释，包括单行注释和块注释。单行注释以两个斜杠字符开头，如：

```javascript
// 单行注释
```

块注释以一个斜杠和一个星号（`/*`）开头，以它们的反向组合（`*/`）结尾，如：

```javascript
/* 这是多行
注释 */ 
```

#### 严格模式

ECMAScript 5 增加了严格模式（strict mode）的概念。严格模式是一种不同的 JavaScript 解析和执行模型，ECMAScript 3 的一些不规范写法在这种模式下会被处理，对于不安全的活动将抛出错误。要对整个脚本启用严格模式，在脚本开头加上这一行：

```javascript
"use strict";
```

虽然看起来像个没有赋值给任何变量的字符串，但它其实是一个预处理指令。任何支持的 JavaScript 引擎看到它都会切换到严格模式。选择这种语法形式的目的是不破坏 ECMAScript 3 语法。

也可以单独指定一个函数在严格模式下执行，只要把这个预处理指令放到函数体开头即可：

```javascript
function doSomething() { 
   "use strict"; 
   // 函数体 
} 
```

严格模式会影响 JavaScript 执行的很多方面。并且所有现代浏览器都支持严格模式。

#### 语句

ECMAScript 中的语句以分号结尾。省略分号意味着由解析器确定语句在哪里结尾，如下面的例子所示：

```javascript
let sum = a + b // 没有分号也有效，但不推荐
let diff = a - b; // 加分号有效，推荐
```

即使语句末尾的分号不是必需的，也应该加上。记着加分号有助于防止省略造成的问题，比如可以避免输入内容不完整。此外，加分号也便于开发者通过删除空行来压缩代码（如果没有结尾的分号，只删除空行，则会导致语法错误）。加分号也有助于在某些情况下提升性能，因为解析器会尝试在合适的位置补上分号以纠正语法错误。

多条语句可以合并到一个 C 语言风格的代码块中。代码块由一个左花括号（`{`）标识开始，一个右花括号（`}`）标识结束：

```javascript
if (test) { 
    test = false; 
    console.log(test); 
}
```

if 之类的控制语句只在执行多条语句时要求必须有代码块。不过，最佳实践是始终在控制语句中使用代码块，即使要执行的只有一条语句，如下例所示：

```javascript
// 有效，但容易导致错误，应该避免
if (test) 
    console.log(test); 

// 推荐
if (test) { 
    console.log(test); 
} 
```

在控制语句中使用代码块可以让内容更清晰，在需要修改代码时也可以减少出错的可能性。

### 关键字与保留字

ECMA-262 描述了一组保留的关键字，这些关键字有特殊用途，比如表示控制语句的开始和结束，或者执行特定的操作。按照规定，保留的关键字不能用作标识符或属性名。

ECMA-262 第 6 版规定的所有关键字如下：break、do、 in、typeof、case、else、instanceof、var、catch、export、new、void、class、extends、return、while、const、finally、super、with、continue、for、switch、yield、debugger、function、this、default、if、throw、delete、import、try。 

规范中也描述了一组未来的保留字，同样不能用作标识符或属性名。虽然保留字在语言中没有特定用途，但它们是保留给将来做关键字用的。以下是 ECMA-262 第 6 版为将来保留的所有词汇。始终保留：enum。严格模式下保留：implements、package、public、interface、protected、static、let、private。 模块代码中保留:  await。

这些词汇不能用作标识符，但现在还可以用作对象的属性名。一般来说，最好还是不要使用关键字和保留字作为标识符和属性名，以确保兼容过去和未来的 ECMAScript 版本。

### 变量

ECMAScript 的变量是松散类型的，所谓松散类型就是可以用来保存任何类型的数据。每个变量只不过是一个用于保存任意值的命名占位符。有 3 个关键字可以声明变量：`var`、`const` 和 `let`。其中，`var` 在 ECMAScript 的所有版本中都可以使用，而 `const` 和 `let` 只能在 ECMAScript 6 及更晚的版本中使用。

#### `var` 关键字

定义变量时要使用 `var` 操作符，后跟变量名（即一个标识符）：`var message;`

这行代码定义了一个名为 `message` 的变量，可以用它保存任何类型的值。（不初始化的情况下，变量会保存一个特殊值 `undefined` ）

ECMAScript 实现变量初始化，因此可以同时定义变量并设置它的值：`var message = "hi"; `

这里，`message` 被定义为一个保存字符串值 `hi` 的变量。像这样初始化变量不会将它标识为字符串类型，只是一个简单的赋值而已。随后，不仅可以改变保存的值，也可以改变值的类型：

```javascript
var message = "hi"; 
message = 100; // 合法，但不推荐
```

在这个例子中，变量 `message` 首先被定义为一个保存字符串值 `hi` 的变量，然后又被重写为保存了数值 `100`。虽然不推荐改变变量保存值的类型，但这在 ECMAScript 中是完全有效的。

① `var` 声明作用域

关键的问题在于，使用 `var` 操作符定义的变量会成为包含它的函数的局部变量。比如，使用 `var` 在一个函数内部定义一个变量，就意味着该变量将在函数退出时被销毁：

```javascript
function test() { 
    var message = "hi"; // 局部变量
} 
test(); 
console.log(message); // 出错！
```

这里，`message` 变量是在函数内部使用 `var` 定义的。函数叫 `test()`，调用它会创建这个变量并给它赋值。调用之后变量随即被销毁，因此示例中的最后一行会导致错误。不过，在函数内定义变量时省略 `var` 操作符，可以创建一个全局变量：

```javascript
function test() {
    message = "hi"; // 全局变量
}
test();
console.log(message); // "hi"
```

去掉之前的 `var` 操作符之后，`message` 就变成了全局变量。只要调用一次函数 `test()`，就会定义这个变量，并且可以在函数外部访问到。

> 虽然可以通过省略 `var` 操作符定义全局变量，但不推荐这么做。在局部作用域中定义的全局变量很难维护，也会造成困惑。这是因为不能一下子断定省略 `var` 是不是有意而为之。在严格模式下，如果像这样给未声明的变量赋值，则会导致抛出 `ReferenceError`。

如果需要定义多个变量，可以在一条语句中用逗号分隔每个变量（及可选的初始化）：

```javascript
var message = "hi", 
    found = false, 
    age = 29; 
```

这里定义并初始化了 3 个变量。因为 ECMAScript 是松散类型的，所以使用不同数据类型初始化的变量可以用一条语句来声明。插入换行和空格缩进并不是必需的，但这样有利于阅读理解。

> 在严格模式下，不能定义名为 `eval` 和 `arguments` 的变量，否则会导致语法错误。

② `var` 声明提升

使用 `var` 时，下面的代码不会报错。这是因为使用这个关键字声明的变量会自动提升到函数作用域顶部：

```javascript
function foo() {
    console.log(age);
    var age = 26;
}
foo(); // undefined 
```

之所以不会报错，是因为 ECMAScript 运行时把它看成等价于如下代码：

```javascript
function foo() {
    var age;
    console.log(age);
    age = 26;
}
foo(); // undefined 
```

这就是所谓的“提升”（hoist），也就是把所有变量声明都拉到函数作用域的顶部。此外，反复多次使用 `var` 声明同一个变量也没有问题：

```javascript
function foo() {
    var age = 16;
    var age = 26;
    var age = 36;
    console.log(age);
}
foo(); // 36 
```

#### `let` 声明

`let` 跟 `var` 的作用差不多，但有着非常重要的区别。最明显的区别是，`let` 声明的范围是块作用域，而 `var` 声明的范围是函数作用域。

```javascript
if (true) {
    var name = 'Matt';
    console.log(name); // Matt 
}
console.log(name); // Matt 
```

```javascript
if (true) {
    let age = 26;
    console.log(age); // 26 
}
console.log(age); // ReferenceError: age 没有定义
```

在这里，`age` 变量之所以不能在 if 块外部被引用，是因为它的作用域仅限于该块内部。块作用域是函数作用域的子集，因此适用于 `var` 的作用域限制同样也适用于 `let`。

`let` 也不允许同一个块作用域中出现冗余声明。这样会导致报错：

```javascript
var name; 
var name; 
let age; 
let age; // SyntaxError；标识符 age 已经声明过了
```

当然，JavaScript 引擎会记录用于变量声明的标识符及其所在的块作用域，因此嵌套使用相同的标识符不会报错，而这是因为同一个块中没有重复声明：

```javascript
var name = 'Nicholas';
console.log(name); // 'Nicholas' 
if (true) {
    var name = 'Matt';
    console.log(name); // 'Matt' 
}
let age = 30;
console.log(age); // 30 
if (true) {
    let age = 26;
    console.log(age); // 26 
}
```

对声明冗余报错不会因混用 `let` 和 `var` 而受影响。这两个关键字声明的并不是不同类型的变量，它们只是指出变量在相关作用域如何存在。

```javascript
var name; 
let name; // SyntaxError 
let age; 
var age; // SyntaxError 
```

① 暂时性死区

`let` 与 `var` 的另一个重要的区别，就是 `let` 声明的变量不会在作用域中被提升。

```javascript
// name 会被提升
console.log(name); // undefined 
var name = 'Matt'; 
// age 不会被提升
console.log(age); // ReferenceError：age 没有定义
let age = 26; 
```

在解析代码时，JavaScript 引擎也会注意出现在块后面的 `let` 声明，只不过在此之前不能以任何方式来引用未声明的变量。在 `let` 声明之前的执行瞬间被称为“暂时性死区”（temporal dead zone），在此阶段引用任何后面才声明的变量都会抛出 `ReferenceError`。

② 全局声明

与 `var` 关键字不同，使用 `let` 在全局作用域中声明的变量不会成为 `window` 对象的属性（`var` 声明的变量则会）。

```javascript
var name = 'Matt'; 
console.log(window.name); // 'Matt' 
let age = 26; 
console.log(window.age); // undefined 
```

不过，`let` 声明仍然是在全局作用域中发生的，相应变量会在页面的生命周期内存续。因此，为了避免 ` SyntaxError`，必须确保页面不会重复声明同一个变量。

③ 条件声明

在使用 `var` 声明变量时，由于声明会被提升，JavaScript 引擎会自动将多余的声明在作用域顶部合并为一个声明。因为 `let` 的作用域是块，所以不可能检查前面是否已经使用 `let` 声明过同名变量，同时也就不可能在没有声明的情况下声明它。

```html
<script> 
    var name = 'Nicholas'; 
    let age = 26; 
</script> 
<script> 
    // 假设脚本不确定页面中是否已经声明了同名变量
    // 那它可以假设还没有声明过
    var name = 'Matt'; 
    // 这里没问题，因为可以被作为一个提升声明来处理
    // 不需要检查之前是否声明过同名变量
    let age = 36; 
    // 如果 age 之前声明过，这里会报错
</script> 
```

使用 try/catch 语句或 `typeof` 操作符也不能解决，因为条件块中 `let` 声明的作用域仅限于该块。

```html
<script> 
    let name = 'Nicholas'; 
    let age = 36; 
</script> 
<script> 
    // 假设脚本不确定页面中是否已经声明了同名变量
    // 那它可以假设还没有声明过
    if (typeof name === 'undefined') {
        let name;
    }
    // name 被限制在 if {} 块的作用域内
    // 因此这个赋值形同全局赋值
    name = 'Matt';
    try {
        console.log(age); // 如果 age 没有声明过，则会报错
    }
    catch (error) {
        let age;
    }
    // age 被限制在 catch {}块的作用域内
    // 因此这个赋值形同全局赋值
    age = 26;
</script> 
```

为此，对于 `let` 这个新的 ES6 声明关键字，不能依赖条件声明模式。

> 不能使用 `let` 进行条件式声明是件好事，因为条件声明是一种反模式，它让程序变得更难理解。如果你发现自己在使用这个模式，那一定有更好的替代方式。

④ for 循环中的 `let` 声明

在 `let` 出现之前，for 循环定义的迭代变量会渗透到循环体外部：

```javascript
for (var i = 0; i < 5; ++i) { 
    // 循环逻辑 
} 
console.log(i); // 5
```

改成使用 `let` 之后，这个问题就消失了，因为迭代变量的作用域仅限于 for 循环块内部：

```javascript
for (let i = 0; i < 5; ++i) { 
    // 循环逻辑
} 
console.log(i); // ReferenceError: i 没有定义
```

在使用 `var` 的时候，最常见的问题就是对迭代变量的奇特声明和修改：

```javascript
for (var i = 0; i < 5; ++i) {
    setTimeout(() => console.log(i), 0) 
} 
// 你可能以为会输出 0、1、2、3、4 
// 实际上会输出 5、5、5、5、5
```

之所以会这样，是因为在退出循环时，迭代变量保存的是导致循环退出的值：5。在之后执行超时逻辑时，所有的 `i` 都是同一个变量，因而输出的都是同一个最终值。

而在使用 `let` 声明迭代变量时，JavaScript 引擎在后台会为每个迭代循环声明一个新的迭代变量。每个 `setTimeout` 引用的都是不同的变量实例，所以 `console.log` 输出的是我们期望的值，也就是循环执行过程中每个迭代变量的值。

```javascript
for (let i = 0; i < 5; ++i) { 
    setTimeout(() => console.log(i), 0) 
} 
// 会输出 0、1、2、3、4 
```

这种每次迭代声明一个独立变量实例的行为适用于所有风格的 for 循环，包括 for-in 和 for-of 循环。

#### `const` 声明

`const` 的行为与 `let` 基本相同，唯一一个重要的区别是用它声明变量时必须同时初始化变量，且尝试修改 `const` 声明的变量会导致运行时错误。

```javascript
const age = 26; 
age = 36; // TypeError: 给常量赋值

// const 也不允许重复声明
const name = 'Matt'; 
const name = 'Nicholas'; // SyntaxError 

// const 声明的作用域也是块
const name = 'Matt'; 
if (true) { 
    const name = 'Nicholas'; 
} 
console.log(name); // Matt 
```

`const` 声明的限制只适用于它指向的变量的引用。换句话说，如果 `const` 变量引用的是一个对象，那么修改这个对象内部的属性并不违反 `const` 的限制。

```javascript
const person = {}; 
person.name = 'Matt'; // ok 
```

JavaScript 引擎会为 for 循环中的 `let` 声明分别创建独立的变量实例，虽然 `const` 变量跟 `let` 变量很相似，但是不能用 `const` 来声明迭代变量（因为迭代变量会自增）：

```javascript
for (const i = 0; i < 10; ++i) {} // TypeError：给常量赋值
```

不过，如果你只想用 `const` 声明一个不会被修改的 for 循环变量，那也是可以的。也就是说，每次迭代只是创建一个新变量。这对 for-of 和 for-in 循环特别有意义：

```javascript
let i = 0;
for (const j = 7; i < 5; ++i) {
    console.log(j);
}
// 7, 7, 7, 7, 7 
for (const key in { a: 1, b: 2 }) {
    console.log(key);
}
// a, b 
for (const value of [1, 2, 3, 4, 5]) {
    console.log(value);
}
// 1, 2, 3, 4, 5
```

#### 声明风格及最佳实践

ECMAScript 6 增加 `let` 和 `const` 从客观上为这门语言更精确地声明作用域和语义提供了更好的支持。行为怪异的 `var` 所造成的各种问题，已经让 JavaScript 社区为之苦恼了很多年。随着这两个新关键字的出现，新的有助于提升代码质量的最佳实践也逐渐显现。

① 不使用 `var`

有了 `let` 和 `const`，大多数开发者会发现自己不再需要 `var` 了。限制自己只使用 `let` 和 `const` 有助于提升代码质量，因为变量有了明确的作用域、声明位置，以及不变的值。

② `const` 优先，`let` 次之

使用 `const` 声明可以让浏览器运行时强制保持变量不变，也可以让静态代码分析工具提前发现不合法的赋值操作。因此，很多开发者认为应该优先使用 `const` 来声明变量，只在提前知道未来会有修改时，再使用 `let`。这样可以让开发者更有信心地推断某些变量的值永远不会变，同时也能迅速发现因意外赋值导致的非预期行为。

### 数据类型

ECMAScript 有 6 种简单数据类型（也称为原始类型）：Undefined、Null、Boolean、Number、String 和 Symbol。Symbol（符号）是 ECMAScript 6 新增的。还有一种复杂数据类型叫 Object（对象）。Object 是一种无序名值对的集合。因为在 ECMAScript 中不能定义自己的数据类型，所有值都可以用上述 7 种数据类型之一来表示。只有 7 种数据类型似乎不足以表示全部数据。但 ECMAScript 的数据类型很灵活，一种数据类型可以当作多种数据类型来使用。

#### `typeof` 操作符

因为 ECMAScript 的类型系统是松散的，所以需要一种手段来确定任意变量的数据类型。`typeof` 操作符就是为此而生的。对一个值使用 `typeof` 操作符会返回下列字符串之一：

+ "undefined" 表示这个值未定义；
+ "boolean" 表示这个值是布尔值；
+ "string" 表示这个值是字符串；
+ "number" 表示这个值是数值；
+ "object" 表示这个值是对象或 null；
+ "function" 表示这个值是函数；
+ "symbol" 表示值为符号。

下面是使用 `typeof` 操作符的例子：

```javascript
let message = "some string"; 
console.log(typeof message); // "string" 
console.log(typeof(message)); // "string" 
console.log(typeof 95); // "number" 
```

在这个例子中，我们把一个变量（`message`）和一个数值字面量传给了 `typeof` 操作符。注意，因为 `typeof` 是一个操作符而不是函数，所以不需要参数（但可以使用参数）。

注意 `typeof` 在某些情况下返回的结果可能会让人费解，但技术上讲还是正确的。比如，调用 `typeof null` 返回的是 `"object"`。这是因为特殊值 `null` 被认为是一个对空对象的引用。

> 严格来讲，函数在 ECMAScript 中被认为是对象，并不代表一种数据类型。可是， 函数也有自己特殊的属性。为此，就有必要通过 `typeof` 操作符来区分函数和其他对象。

#### Undefined 类型

Undefined 类型只有一个值，就是特殊值 `undefined`。当使用 `var` 或 `let` 声明了变量但没有初始化时，就相当于给变量赋予了 `undefined` 值：

```javascript
let message; 
console.log(message == undefined); // true 
```

在这个例子中，变量 `message` 在声明的时候并未初始化。而在比较它和 `undefined` 的字面值时，两者是相等的。这个例子等同于如下示例：

```javascript
let message = undefined; 
console.log(message == undefined); // true
```

这里，变量 `message` 显式地以 `undefined` 来初始化。但这是不必要的，因为默认情况下，任何未经初始化的变量都会取得 `undefined` 值。

> 一般来说，永远不用显式地给某个变量设置 `undefined` 值。字面值 `undefined` 主要用于比较，而且在 ECMA-262 第 3 版之前是不存在的。增加这个特殊值的目的就是为了正式明确空对象指针（`null`）和未初始化变量的区别。

注意，包含 `undefined` 值的变量跟未定义变量是有区别的。请看下面的例子：

```javascript
let message; // 这个变量被声明了，只是值为 undefined 
// 确保没有声明过这个变量
// let age 
console.log(message); // "undefined" 
console.log(age); // 报错
```

在上面的例子中，第一个 `console.log` 会指出变量 `message` 的值，即 "undefined"。而第二个 `console.log` 要输出一个未声明的变量 `age` 的值，因此会导致报错。对未声明的变量，只能执行一个有用的操作，就是对它调用 `typeof`。（对未声明的变量调用 `delete` 也不会报错，但这个操作没什么用，实际上在严格模式下会抛出错误。）

在对未初始化的变量调用 `typeof` 时，返回的结果是 "undefined"，但对未声明的变量调用它时，返回的结果还是 "undefined"，这就有点让人看不懂了。比如下面的例子：

```javascript
let message; // 这个变量被声明了，只是值为 undefined 
// 确保没有声明过这个变量
// let age 
console.log(typeof message); // "undefined" 
console.log(typeof age); // "undefined"
```

无论是声明还是未声明，`typeof` 返回的都是字符串 "undefined"。逻辑上讲这是对的，因为虽然严格来讲这两个变量存在根本性差异，但它们都无法执行实际操作。

> 即使未初始化的变量会被自动赋予 `undefined` 值，但我们仍然建议在声明变量的同时进行初始化。这样，当 `typeof` 返回 "undefined" 时，你就会知道那是因为给定的变量尚未声明，而不是声明了但未初始化。

`undefined` 是一个假值。因此，如果需要，可以用更简洁的方式检测它。不过要记住，也有很多其他可能的值同样是假值。所以一定要明确自己想检测的就是 `undefined` 这个字面值，而不仅仅是假值。

```javascript
let message; // 这个变量被声明了，只是值为 undefined 
// age 没有声明 
if (message) {
    // 这个块不会执行
}
if (!message) {
    // 这个块会执行
}
if (age) {
    // 这里会报错
}
```

#### Null 类型

Null 类型同样只有一个值，即特殊值 `null`。逻辑上讲，`null` 值表示一个空对象指针，这也是给 `typeof` 传一个 `null` 会返回 "object" 的原因：

```javascript
let car = null; 
console.log(typeof car); // "object" 
```

在定义将来要保存对象值的变量时，建议使用 `null` 来初始化，不要使用其他值。这样，只要检查这个变量的值是不是 `null` 就可以知道这个变量是否在后来被重新赋予了一个对象的引用，比如：

```javascript
if (car != null) { 
    // car 是一个对象的引用
} 
```

`undefined` 值是由 `null` 值派生而来的，因此 ECMA-262 将它们定义为表面上相等，如下面的例子所示：

```javascript
console.log(null == undefined); // true 
console.log(null === undefined); // false
```

用等于操作符（`==`）比较 `null` 和 `undefined` 始终返回 true。但要注意，这个操作符会为了比较而转换它的操作数。

即使 `null` 和 `undefined` 有关系，它们的用途也是完全不一样的。如前所述，永远不必显式地将变量值设置为 `undefined`。但 `null` 不是这样的。任何时候，只要变量要保存对象，而当时又没有那个对象可保存，就要用 `null` 来填充该变量。这样就可以保持 `null` 是空对象指针的语义，并进一步将其与 `undefined` 区分开来。

`null` 是一个假值。因此，如果需要，可以用更简洁的方式检测它。不过要记住，也有很多其他可能的值同样是假值。所以一定要明确自己想检测的就是 `null` 这个字面值，而不仅仅是假值。

```javascript
let message = null;
let age;
if (message) {
    // 这个块不会执行
}
if (!message) {
    // 这个块会执行
}
if (age) {
    // 这个块不会执行
}
if (!age) {
    // 这个块会执行
}
```

#### Boolean 类型

Boolean（布尔值）类型是 ECMAScript 中使用最频繁的类型之一，有两个字面值：`true` 和 `false`。这两个布尔值不同于数值，因此 `true` 不等于 1，`false` 不等于 0。下面是给变量赋布尔值的例子：

```javascript
let found = true; 
let lost = false;
```

注意，布尔值字面量 `true` 和 `false` 是区分大小写的，因此 `True` 和 `False`（及其他大小混写形式）是有效的标识符，但不是布尔值。

虽然布尔值只有两个，但所有其他 ECMAScript 类型的值都有相应布尔值的等价形式。要将一个其他类型的值转换为布尔值，可以调用特定的 `Boolean()` 转型函数：

```javascript
let message = "Hello world!"; 
let messageAsBoolean = Boolean(message);
```

在这个例子中，字符串 `message` 会被转换为布尔值并保存在变量 `messageAsBoolean` 中。`Boolean()` 转型函数可以在任意类型的数据上调用，而且始终返回一个布尔值。什么值能转换为 `true` 或 `false` 的规则取决于数据类型和实际的值。下表总结了不同类型与布尔值之间的转换规则。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-f@master/image.g3rsn3jhtxk.png)

理解以上转换非常重要，因为像 if 等流控制语句会自动执行其他类型值到布尔值的转换，例如：

```javascript
let message = "Hello world!"; 
if (message) { 
    console.log("Value is true"); 
} 
```

在这个例子中，`console.log` 会输出字符串 "Value is true"，因为字符串 `message` 会被自动转换为等价的布尔值 `true`。由于存在这种自动转换，理解流控制语句中使用的是什么变量就非常重要。错误地使用对象而不是布尔值会明显改变应用程序的执行流。

#### Number 类型

ECMAScript 中最有意思的数据类型或许就是 Number 了。Number 类型使用 IEEE 754 格式表示整数和浮点值（在某些语言中也叫双精度值）。不同的数值类型相应地也有不同的数值字面量格式。

最基本的数值字面量格式是十进制整数，直接写出来即可：

```javascript
let intNum = 55; // 整数
```

整数也可以用八进制（以 8 为基数）或十六进制（以 16 为基数）字面量表示。对于八进制字面量，第一个数字必须是零（0），然后是相应的八进制数字（数值 0~7）。如果字面量中包含的数字超出了应有的范围，就会忽略前缀的零，后面的数字序列会被当成十进制数，如下所示：

```javascript
let octalNum1 = 070; // 八进制的 56 
let octalNum2 = 079; // 无效的八进制值，当成 79 处理
let octalNum3 = 08; // 无效的八进制值，当成 8 处理
```

八进制字面量在严格模式下是无效的，会导致 JavaScript 引擎抛出语法错误。（ECMAScript 2015 或 ES6 中的八进制值通过前缀 0o 来表示；严格模式下，前缀 0 会被视为语法错误，如果要表示 八进制值，应该使用前缀 0o。）

要创建十六进制字面量，必须让真正的数值前缀 0x（区分大小写），然后是十六进制数字（0~9 以及 A~F）。十六进制数字中的字母大小写均可。下面是几个例子：

```javascript
let hexNum1 = 0xA; // 十六进制 10 
let hexNum2 = 0x1f; // 十六进制 31
```

使用八进制和十六进制格式创建的数值在所有数学操作中都被视为十进制数值。

① 浮点值

要定义浮点值，数值中必须包含小数点，而且小数点后面必须至少有一个数字。虽然小数点前面不是必须有整数，但推荐加上。下面是几个例子：

```javascript
let floatNum1 = 1.1; 
let floatNum2 = 0.1; 
let floatNum3 = .1; // 有效，但不推荐
```

因为存储浮点值使用的内存空间是存储整数值的两倍，所以 ECMAScript 总是想方设法把值转换为整数。在小数点后面没有数字的情况下，数值就会变成整数。类似地，如果数值本身就是整数，只是小数点后面跟着 0（如 1.0），那它也会被转换为整数，如下例所示：

```javascript
let floatNum1 = 1.; // 小数点后面没有数字，当成整数 1 处理
let floatNum2 = 10.0; // 小数点后面是零，当成整数 10 处理
```

对于非常大或非常小的数值，浮点值可以用科学记数法来表示。科学记数法用于表示一个应该乘以 10 的给定次幂的数值。ECMAScript 中科学记数法的格式要求是一个数值（整数或浮点数）后跟一个大写或小写的字母 e，再加上一个要乘的 10 的多少次幂。比如：

```javascript
let floatNum = 3.125e7; // 等于 31250000 
```

在这个例子中，`floatNum` 等于 31 250 000，只不过科学记数法显得更简洁。这种表示法实际上相当于说：“以 3.125 作为系数，乘以 10 的 7 次幂。”

科学记数法也可以用于表示非常小的数值，例如 0.000 000 000 000 000 03。这个数值用科学记数法可以表示为 3e-17。默认情况下， ECMAScript 会将小数点后至少包含 6 个零的浮点值转换为科学记数法（例如，0.000 000 3 会被转换为 3e-7）。

浮点值的精确度最高可达 17 位小数，但在算术计算中远不如整数精确。例如，0.1 加 0.2 得到的不是 0.3，而是 0.300 000 000 000 000 04。由于这种微小的舍入错误，导致很难测试特定的浮点值。比如下面的例子：

```javascript
if (a + b == 0.3) { // 别这么干！ 
    console.log("You got 0.3."); 
} 
```

这里检测两个数值之和是否等于 0.3。如果两个数值分别是 0.05 和 0.25，或者 0.15 和 0.15，那没问题。但如果是 0.1 和 0.2，如前所述，测试将失败。因此永远不要测试某个特定的浮点值。

> 之所以存在这种舍入错误，是因为使用了 IEEE 754 数值，这种错误并非 ECMAScript 所独有。其他使用相同格式的语言也有这个问题。

② 值的范围

由于内存的限制，ECMAScript 并不支持表示这个世界上的所有数值。ECMAScript 可以表示的最小数值保存在 `Number.MIN_VALUE` 中，这个值在多数浏览器中是 5e-324；可以表示的最大数值保存在 `Number.MAX_VALUE` 中，这个值在多数浏览器中是 1.797 693 134 862 315 7e+308。如果某个计算得到的数值结果超出了 JavaScript 可以表示的范围，那么这个数值会被自动转换为一个特殊的 Infinity（无穷）值。任何无法表示的负数以 `-Infinity`（负无穷大）表示，任何无法表示的正数以 `Infinity`（正无穷大）表示。

如果计算返回 `Infinity` 或 `-Infinity`，则该值将不能再进一步用于任何计算。这是因为 `Infinity` 没有可用于计算的数值表示形式。要确定一个值是不是有限大（即介于 JavaScript 能表示的最小值和最大值之间），可以使用 `isFinite()` 函数，如下所示：

```javascript
let result = Number.MAX_VALUE + Number.MAX_VALUE; 
console.log(isFinite(result)); // false 
```

虽然超出有限数值范围的计算并不多见，但总归还是有可能的。因此在计算非常大或非常小的数值时，有必要监测一下计算结果是否超出范围。

> 使用 `Number.NEGATIVE_INFINITY` 和 `Number.POSITIVE_INFINITY` 也可以获 取正、负 Infinity。没错，这两个属性包含的值分别就是 `-Infinity` 和 `Infinity`。

③ NaN

有一个特殊的数值叫 `NaN`，意思是“不是数值”（Not a Number），用于表示本来要返回数值的操作失败了（而不是抛出错误）。比如，用 0 除任意数值在其他语言中通常都会导致错误，从而中止代码执行。但在 ECMAScript 中，0、+0 或 -0 相除会返回 `NaN`：

```javascript
console.log(0/0); // NaN 
console.log(-0/+0); // NaN
```

如果分子是非 0 值，分母是有符号 0 或无符号 0，则会返回 `Infinity` 或 `-Infinity`：

```javascript
console.log(5/0); // Infinity 
console.log(5/-0); // -Infinity
```

`NaN` 有几个独特的属性。首先，任何涉及 `NaN` 的操作始终返回 `NaN`（如 `NaN/10` ），在连续多步计算时这可能是个问题。其次，`NaN` 不等于包括 `NaN` 在内的任何值。例如，下面的比较操作会返回 false：

```javascript
console.log(NaN == NaN); // false
```

为此，ECMAScript 提供了 `isNaN()` 函数。该函数接收一个参数，可以是任意数据类型，然后判断这个参数是否“不是数值”。把一个值传给  `isNaN()` 后，该函数会尝试把它转换为数值。某些非数值的值可以直接转换成数值，如字符串 "10" 或布尔值。任何不能转换为数值的值都会导致这个函数返回 true。举例如下：

```javascript
console.log(isNaN(NaN)); // true 
console.log(isNaN(10)); // false，10 是数值
console.log(isNaN("10")); // false，可以转换为数值 10 
console.log(isNaN("blue")); // true，不可以转换为数值
console.log(isNaN(true)); // false，可以转换为数值 1
```

上述的例子测试了 5 个不同的值。首先测试的是 `NaN` 本身，显然会返回 true。接着测试了数值 10 和字符串"10"，都返回 false，因为它们的数值都是 10。字符串 "blue" 不能转换为数值，因此函数返回 true。布尔值 true 可以转换为数值 1，因此返回 false。

> 虽然不常见，但 `isNaN()` 可以用于测试对象。此时，首先会调用对象的 `valueOf()` 方法，然后再确定返回的值是否可以转换为数值。如果不能，再调用 `toString()` 方法， 并测试其返回值。

④ 数值转换

有 3 个函数可以将非数值转换为数值：`Number()`、`parseInt()` 和 `parseFloat()`。`Number()` 是转型函数，可用于任何数据类型。后两个函数主要用于将字符串转换为数值。对于同样的参数，这 3 个函数执行的操作也不同。

`Number()` 函数基于如下规则执行转换：

+ 布尔值，true 转换为 1，false 转换为 0。
+ 数值，直接返回。
+ `null`，返回 0。
+ `undefined`，返回 `NaN`。
+ 字符串，应用以下规则：
  + 如果字符串包含数值字符，包括数值字符前面带加、减号的情况，则转换为一个十进制数值。 因此，`Number("1")` 返回 1，`Number("123")` 返回 123，`Number("011")` 返回 11（忽略前面的零）。
  + 如果字符串包含有效的浮点值格式如 "1.1"，则会转换为相应的浮点值（同样，忽略前面的零）。
  + 如果字符串包含有效的十六进制格式如 "0xf"，则会转换为与该十六进制值对应的十进制整数值。
  + 如果是空字符串（不包含字符），则返回 0。
  + 如果字符串包含除上述情况之外的其他字符，则返回 NaN。
+ 对象，调用 `valueOf()` 方法，并按照上述规则转换返回的值。如果转换结果是 `NaN`，则调用 `toString()` 方法，再按照转换字符串的规则转换。

从不同数据类型到数值的转换有时候会比较复杂，看一看 `Number()` 的转换规则就知道了。下面是几个具体的例子：

```javascript
let num1 = Number("Hello world!"); // NaN 
let num2 = Number(""); // 0 
let num3 = Number("000011"); // 11 
let num4 = Number(true); // 1 
```

可以看到，字符串"Hello world" 转换之后是 `NaN`，因为它找不到对应的数值。空字符串转换后是 0。字符串 000011 转换后是 11，因为前面的零被忽略了。最后，true 转换为 1。

> 一元加操作符与 `Number()` 函数遵循相同的转换规则。

考虑到用 `Number()` 函数转换字符串时相对复杂且有点反常规，通常在需要得到整数时可以优先使用 `parseInt()` 函数。`parseInt()` 函数更专注于字符串是否包含数值模式。字符串最前面的空格会被忽略，从第一个非空格字符开始转换。如果第一个字符不是数值字符、加号或减号，`parseInt()` 立即返回 `NaN`。这意味着空字符串也会返回 `NaN`（这一点跟 `Number()` 不一样，它返回 0）。如果第一个字符是数值字符、加号或减号，则继续依次检测每个字符，直到字符串末尾，或碰到非数值字符。比如，"1234blue" 会被转换为 1234，因为 "blue" 会被完全忽略。类似地，"22.5"会被转换为 22，因为小数点不是有效的整数字符。

假设字符串中的第一个字符是数值字符，`parseInt()` 函数也能识别不同的整数格式（十进制、八进制、十六进制）。换句话说，如果字符串以 "0x" 开头，就会被解释为十六进制整数。如果字符串以 "0" 开头，且紧跟着数值字符，在非严格模式下会被某些实现解释为八进制整数。下面几个转换示例有助于理解上述规则：

```javascript
let num1 = parseInt("1234blue"); // 1234 
let num2 = parseInt(""); // NaN 
let num3 = parseInt("0xA"); // 10，解释为十六进制整数
let num4 = parseInt(22.5); // 22 
let num5 = parseInt("70"); // 70，解释为十进制值
let num6 = parseInt("0xf"); // 15，解释为十六进制整数
```

不同的数值格式很容易混淆，因此 `parseInt()` 也接收第二个参数，用于指定底数（进制数）。如果知道要解析的值是十六进制，那么可以传入 16 作为第二个参数，以便正确解析：

```javascript
let num = parseInt("0xAF", 16); // 175
```

事实上，如果提供了十六进制参数，那么字符串前面的 "0x" 可以省掉：

```javascript
let num1 = parseInt("AF", 16); // 175 
let num2 = parseInt("AF"); // NaN 
```

在这个例子中，第一个转换是正确的，而第二个转换失败了。区别在于第一次传入了进制数作为参 数，告诉 `parseInt()` 要解析的是一个十六进制字符串。而第二个转换检测到第一个字符就是非数值字符，随即自动停止并返回 NaN。

通过第二个参数，可以极大扩展转换后获得的结果类型。比如：

```javascript
let num1 = parseInt("10", 2); // 2，按二进制解析
let num2 = parseInt("10", 8); // 8，按八进制解析
let num3 = parseInt("10", 10); // 10，按十进制解析
let num4 = parseInt("10", 16); // 16，按十六进制解析
```

因为不传底数参数相当于让 `parseInt()` 自己决定如何解析，所以为避免解析出错，建议始终传给它第二个参数。

> 多数情况下解析的应该都是十进制数，此时第二个参数就要传入 10。

`parseFloat()` 函数的工作方式跟 `parseInt()` 函数类似，都是从位置 0 开始检测每个字符。同样，它也是解析到字符串末尾或者解析到一个无效的浮点数值字符为止。这意味着第一次出现的小数点是有效的，但第二次出现的小数点就无效了，此时字符串的剩余字符都会被忽略。因此，"22.34.5" 将转换成 22.34。

`parseFloat()` 函数的另一个不同之处在于，它始终忽略字符串开头的零。这个函数能识别前面讨论的所有浮点格式，以及十进制格式（开头的零始终被忽略）。十六进制数值始终会返回 0。因为 `parseFloat()` 只解析十进制值，因此不能指定底数。最后，如果字符串表示整数（没有小数点或者小数点后面只有一个零），则 `parseFloat()` 返回整数。下面是几个示例：

```javascript
let num1 = parseFloat("1234blue"); // 1234，按整数解析
let num2 = parseFloat("0xA"); // 0 
let num3 = parseFloat("22.5"); // 22.5 
let num4 = parseFloat("22.34.5"); // 22.34 
let num5 = parseFloat("0908.5"); // 908.5 
let num6 = parseFloat("3.125e7"); // 31250000 
```

#### String 类型

String（字符串）数据类型表示零或多个 16 位 Unicode 字符序列。字符串可以使用双引号（"）、单引号（'）或反引号（\`）标示，因此下面的代码都是合法的：

```javascript
let firstName = "John"; 
let lastName = 'Jacob'; 
let lastName = `Jingleheimerschmidt` 
```

跟某些语言中使用不同的引号会改变对字符串的解释方式不同，ECMAScript 语法中表示字符串的引号没有区别。不过要注意的是，以某种引号作为字符串开头，必须仍然以该种引号作为字符串结尾。比如，下面的写法会导致语法错误：

```javascript
let firstName = 'Nicholas"; // 语法错误：开头和结尾的引号必须是同一种
```

① 字符字面量

字符串数据类型包含一些字符字面量，用于表示非打印字符或有其他用途的字符，如下表所示：

| 字面量   | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| `\n`     | 换行                                                         |
| `\t`     | 制表                                                         |
| `\b`     | 退格                                                         |
| `\r`     | 回车                                                         |
| `\f`     | 换页                                                         |
| `\\`     | 反斜杠                                                       |
| `\'`     | 单引号                                                       |
| `\"`     | 双引号                                                       |
| `\ˋ`     | 反引号                                                       |
| `\xnn`   | 以十六进制编码 nn 表示的字符（其中 n 是十六进制数字 0~F），例如 `\x41` 等于 "A" |
| `\unnnn` | 以十六进制编码 nnnn 表示的 Unicode 字符（其中 n 是十六进制数字 0~F），例如 `\u03a3` 等于希腊字符 "Σ" |

这些字符字面量可以出现在字符串中的任意位置，且可以作为单个字符被解释：

```javascript
let text = "This is the letter sigma: \u03a3.";
```

在这个例子中，即使包含 6 个字符长的转义序列，变量 text 仍然是 28 个字符长。因为转义序列表示一个字符，所以只算一个字符。

字符串的长度可以通过其 `length` 属性获取：

```javascript
console.log(text.length); // 28
```

这个属性返回字符串中 16 位字符的个数。

> 如果字符串中包含双字节字符，那么 `length` 属性返回的值可能不是准确的字符数。

② 字符串的特点

ECMAScript 中的字符串是不可变的（immutable），意思是一旦创建，它们的值就不能变了。要修改某个变量中的字符串值，必须先销毁原始的字符串，然后将包含新值的另一个字符串保存到该变量，如下所示：

```javascript
let lang = "Java"; 
lang = lang + "Script"; 
```

这里，变量 `lang` 一开始包含字符串 "Java"。紧接着，`lang` 被重新定义为包含 "Java" 和 "Script" 的组合，也就是 "JavaScript"。整个过程首先会分配一个足够容纳 10 个字符的空间，然后填充上 "Java" 和 "Script"。最后销毁原始的字符串 "Java" 和字符串 "Script"，因为这两个字符串都没有用了。所有处理都是在后台发生的，而这也是一些早期的浏览器（如 Firefox 1.0 之前的版本和 IE6.0）在拼接字符串时非常慢的原因。这些浏览器在后来的版本中都有针对性地解决了这个问题。

③ 转换为字符串

有两种方式把一个值转换为字符串。首先是使用几乎所有值都有的 `toString()` 方法。这个方法唯一的用途就是返回当前值的字符串等价物。比如：

```javascript
let age = 11; 
let ageAsString = age.toString(); // 字符串"11" 
let found = true; 
let foundAsString = found.toString(); // 字符串"true"
```

`toString()` 方法可见于数值、布尔值、对象和字符串值。（没错，字符串值也有 `toString()` 方法，该方法只是简单地返回自身的一个副本。）`null` 和 `undefined` 值没有 `toString()` 方法。

多数情况下，`toString()` 不接收任何参数。不过，在对数值调用这个方法时，`toString()` 可以接收一个底数参数，即以什么底数来输出数值的字符串表示。默认情况下，`toString()` 返回数值的十进制字符串表示。而通过传入参数，可以得到数值的二进制、八进制、十六进制，或者其他任何有效基数的字符串表示，比如：

```javascript
let num = 10; 
console.log(num.toString()); // "10" 
console.log(num.toString(2)); // "1010" 
console.log(num.toString(8)); // "12" 
console.log(num.toString(10)); // "10" 
console.log(num.toString(16)); // "a"
```

这个例子展示了传入底数参数时，`toString()` 输出的字符串值也会随之改变。数值 10 可以输出为任意数值格式。注意，默认情况下（不传参数）的输出与传入参数 10 得到的结果相同。

如果你不确定一个值是不是 `null` 或 `undefined`，可以使用 `String()` 转型函数，它始终会返回表示相应类型值的字符串。`String()` 函数遵循如下规则：

+ 如果值有 `toString()` 方法，则调用该方法（不传参数）并返回结果；
+ 如果值是 `null`，返回 "null"；
+ 如果值是 `undefined`，返回"undefined"。

下面看几个例子：

```javascript
let value1 = 10; 
let value2 = true; 
let value3 = null; 
let value4; 
console.log(String(value1)); // "10" 
console.log(String(value2)); // "true" 
console.log(String(value3)); // "null" 
console.log(String(value4)); // "undefined"
```

这里展示了将 4 个值转换为字符串的情况：一个数值、一个布尔值、一个 `null` 和一个 `undefined`。数值和布尔值的转换结果与调用 `toString()` 相同。因为 `null` 和 `undefined` 没有 `toString()` 方法，所以 `String()` 方法就直接返回了这两个值的字面量文本。

> 用加号操作符给一个值加上一个空字符串 `""` 也可以将其转换为字符串

④ 模板字面量

ECMAScript 6 新增了使用模板字面量定义字符串的能力。与使用单引号或双引号不同，模板字面量保留换行字符，可以跨行定义字符串：

```javascript
let myMultiLineString = 'first line\nsecond line'; 
let myMultiLineTemplateLiteral = `first line 
second line`; 
console.log(myMultiLineString); 
// first line 
// second line" 
console.log(myMultiLineTemplateLiteral); 
// first line 
// second line 
console.log(myMultiLineString === myMultiLinetemplateLiteral); // true 
```

顾名思义，模板字面量在定义模板时特别有用，比如下面这个 HTML 模板：

```javascript
let pageHTML = ` 
<div> 
  <a href="#"> 
    <span>Jake</span> 
  </a> 
</div>`; 
```

由于模板字面量会保持反引号内部的空格，因此在使用时要格外注意。格式正确的模板字符串看起来可能会缩进不当：

```javascript
// 这个模板字面量在换行符之后有 25 个空格符
let myTemplateLiteral = `first line
                         second line`; 
console.log(myTemplateLiteral.length); // 47 

// 这个模板字面量以一个换行符开头
let secondTemplateLiteral = `
first line 
second line`; 
console.log(secondTemplateLiteral[0] === '\n'); // true 

// 这个模板字面量没有意料之外的字符
let thirdTemplateLiteral = `first line 
second line`; 
console.log(thirdTemplateLiteral); 
// first line 
// second line
```

⑤ 字符串插值

模板字面量最常用的一个特性是支持字符串插值，也就是可以在一个连续定义中插入一个或多个值。技术上讲，模板字面量不是字符串，而是一种特殊的 JavaScript 句法表达式，只不过求值后得到的是字符串。模板字面量在定义时立即求值并转换为字符串实例，任何插入的变量也会从它们最接近的作用域中取值。

字符串插值通过在 `${}` 中使用一个 JavaScript 表达式实现：

```javascript
let value = 5;
let exponent = 'second';

// 以前，字符串插值是这样实现的：
let interpolatedString =
    value + ' to the ' + exponent + ' power is ' + (value * value);

// 现在，可以用模板字面量这样实现：
let interpolatedTemplateLiteral =
    `${value} to the ${exponent} power is ${value * value}`;

console.log(interpolatedString); // 5 to the second power is 25 
console.log(interpolatedTemplateLiteral); // 5 to the second power is 25
```

所有插入的值都会使用 `toString()` 强制转型为字符串，而且任何 JavaScript 表达式都可以用于插值。嵌套的模板字符串无须转义：

```javascript
console.log(`Hello, ${ `World` }!`); // Hello, World! 
```

将表达式转换为字符串时会调用 `toString()`：

```javascript
let foo = { toString: () => 'World' }; 
console.log(`Hello, ${ foo }!`); // Hello, World! 
```

在插值表达式中可以调用函数和方法：

```javascript
function capitalize(word) {
    return `${word[0].toUpperCase()}${word.slice(1)}`;
}
console.log(`${capitalize('hello')}, ${capitalize('world')}!`); // Hello, World!
```

此外，模板也可以插入自己之前的值：

```javascript
let value = '';
function append() {
    value = `${value}abc`
    console.log(value);
}
append(); // abc 
append(); // abcabc 
append(); // abcabcabc
```

⑥ 模板字面量标签函数

模板字面量也支持定义标签函数（tag function），而通过标签函数可以自定义插值行为。标签函数会接收被插值记号分隔后的模板和对每个表达式求值的结果。

标签函数本身是一个常规函数，通过前缀到模板字面量来应用自定义行为，如下例所示。标签函数接收到的参数依次是原始字符串数组和对每个表达式求值的结果。这个函数的返回值是对模板字面量求值得到的字符串。

最好通过一个例子来理解：

```javascript
let a = 6;
let b = 9;
function simpleTag(strings, aValExpression, bValExpression, sumExpression) {
    console.log(strings);
    console.log(aValExpression);
    console.log(bValExpression);
    console.log(sumExpression);
    return 'foobar';
}

let untaggedResult = `${a} + ${b} = ${a + b}`;
let taggedResult = simpleTag`${a} + ${b} = ${a + b}`;
// ["", " + ", " = ", ""] 
// 6 
// 9 
// 15 
console.log(untaggedResult); // "6 + 9 = 15" 
console.log(taggedResult); // "foobar" 
```

因为表达式参数的数量是可变的，所以通常应该使用剩余操作符（rest operator）将它们收集到一个数组中：

```javascript
let a = 6;
let b = 9;
function simpleTag(strings, ...expressions) {
    console.log(strings);
    for (const expression of expressions) {
        console.log(expression);
    }
    return 'foobar';
}

let taggedResult = simpleTag`${a} + ${b} = ${a + b}`;
// ["", " + ", " = ", ""] 
// 6 
// 9 
// 15 
console.log(taggedResult); // "foobar" 
```

对于有 n 个插值的模板字面量，传给标签函数的表达式参数的个数始终是 n，而传给标签函数的第一个参数所包含的字符串个数则始终是 n+1。因此，如果你想把这些字符串和对表达式求值的结果拼接起来作为默认返回的字符串，可以这样做：

```javascript
let a = 6;
let b = 9;
function zipTag(strings, ...expressions) {
    return strings[0] + expressions.map((e, i) => `${e}${strings[i + 1]}`).join('');
}

let untaggedResult = `${a} + ${b} = ${a + b}`;
let taggedResult = zipTag`${a} + ${b} = ${a + b}`;

console.log(untaggedResult); // "6 + 9 = 15" 
console.log(taggedResult); // "6 + 9 = 15" 
```

⑦ 原始字符串

使用模板字面量也可以直接获取原始的模板字面量内容（如换行符或 Unicode 字符），而不是被转换后的字符表示。为此，可以使用默认的 `String.raw` 标签函数：

```javascript
// Unicode 示例
// \u00A9 是版权符号
console.log(`\u00A9`); // © 
console.log(String.raw`\u00A9`); // \u00A9 

// 换行符示例
console.log(`first line\nsecond line`);
// first line 
// second line 

console.log(String.raw`first line\nsecond line`); // "first line\nsecond line" 

// 对实际的换行符来说是不行的它们不会被转换成转义序列的形式
console.log(`first line 
second line`);
// first line 
// second line 
console.log(String.raw`first line 
second line`);
// first line
// second line 
```

另外，也可以通过标签函数的第一个参数，即字符串数组的 `.raw` 属性取得每个字符串的原始内容：

```javascript
function printRaw(strings) {
    console.log('Actual characters:');
    for (const string of strings) {
        console.log(string);
    }
    console.log('Escaped characters;');
    for (const rawString of strings.raw) {
        console.log(rawString);
    }
}
printRaw`\u00A9${'and'}\n`;
// Actual characters:
// © 
//（换行符）
// Escaped characters: 
// \u00A9 
// \n
```

#### Symbol 类型

Symbol（符号）是 ECMAScript 6 新增的数据类型。符号是原始值，且符号实例是唯一、不可变的。符号的用途是确保对象属性使用唯一标识符，不会发生属性冲突的危险。

尽管听起来跟私有属性有点类似，但符号并不是为了提供私有属性的行为才增加的（尤其是因为 Object API 提供了方法，可以更方便地发现符号属性）。相反，符号就是用来创建唯一记号，进而用作非字符串形式的对象属性。

① 符号的基本用法

符号需要使用 `Symbol()` 函数初始化。因为符号本身是原始类型，所以 `typeof` 操作符对符号返回 `symbol`。

```javascript
let sym = Symbol(); 
console.log(typeof sym); // symbol 
```

调用 `Symbol()` 函数时，也可以传入一个字符串参数作为对符号的描述（description），将来可以通过这个字符串来调试代码。但是，这个字符串参数与符号定义或标识完全无关：

```javascript
let genericSymbol = Symbol(); 
let otherGenericSymbol = Symbol(); 

let fooSymbol = Symbol('foo'); 
let otherFooSymbol = Symbol('foo'); 

console.log(genericSymbol == otherGenericSymbol); // false 
console.log(fooSymbol == otherFooSymbol); // false 
```

符号没有字面量语法，这也是它们发挥作用的关键。按照规范，你只要创建 `Symbol()` 实例并将其用作对象的新属性，就可以保证它不会覆盖已有的对象属性，无论是符号属性还是字符串属性。

```javascript
let genericSymbol = Symbol(); 
console.log(genericSymbol); // Symbol() 

let fooSymbol = Symbol('foo'); 
console.log(fooSymbol); // Symbol(foo); 
```

最重要的是，`Symbol()` 函数不能与 new 关键字一起作为构造函数使用。这样做是为了避免创建符号包装对象，像使用 Boolean、String 或 Number 那样，它们都支持构造函数且可用于初始化包含原始值的包装对象：

```javascript
let myBoolean = new Boolean(); 
console.log(typeof myBoolean); // "object" 

let myString = new String(); 
console.log(typeof myString); // "object" 

let myNumber = new Number(); 
console.log(typeof myNumber); // "object" 

let mySymbol = new Symbol(); // TypeError: Symbol is not a constructor 
```

如果你确实想使用符号包装对象，可以借用 `Object()` 函数：

```javascript
let mySymbol = Symbol(); 
let myWrappedSymbol = Object(mySymbol); 
console.log(typeof myWrappedSymbol); // "object"
```

② 使用全局符号注册表

如果运行时的不同部分需要共享和重用符号实例，那么可以用一个字符串作为键，在全局符号注册表中创建并重用符号。

为此，需要使用 `Symbol.for()` 方法：

```javascript
let fooGlobalSymbol = Symbol.for('foo'); 
console.log(typeof fooGlobalSymbol); // symbol
```

`Symbol.for()` 对每个字符串键都执行幂等操作。第一次使用某个字符串调用时，它会检查全局运行时注册表，发现不存在对应的符号，于是就会生成一个新符号实例并添加到注册表中。后续使用相同字符串的调用同样会检查注册表，发现存在与该字符串对应的符号，然后就会返回该符号实例。

```javascript
let fooGlobalSymbol = Symbol.for('foo'); // 创建新符号
let otherFooGlobalSymbol = Symbol.for('foo'); // 重用已有符号
console.log(fooGlobalSymbol === otherFooGlobalSymbol); // true 
```

即使采用相同的符号描述，在全局注册表中定义的符号跟使用 `Symbol()` 定义的符号也并不等同：

```javascript
let localSymbol = Symbol('foo'); 
let globalSymbol = Symbol.for('foo'); 
console.log(localSymbol === globalSymbol); // false
```

全局注册表中的符号必须使用字符串键来创建，因此作为参数传给 `Symbol.for()` 的任何值都会被转换为字符串。此外，注册表中使用的键同时也会被用作符号描述。

```javascript
let emptyGlobalSymbol = Symbol.for(); 
console.log(emptyGlobalSymbol); // Symbol(undefined) 
```

还可以使用 `Symbol.keyFor()` 来查询全局注册表，这个方法接收符号，返回该全局符号对应的字符串键。如果查询的不是全局符号，则返回 `undefined`。

```javascript
// 创建全局符号
let s = Symbol.for('foo'); 
console.log(Symbol.keyFor(s)); // foo 

// 创建普通符号
let s2 = Symbol('bar'); 
console.log(Symbol.keyFor(s2)); // undefined
```

如果传给 `Symbol.keyFor()` 的不是符号，则该方法抛出 TypeError：

```javascript
Symbol.keyFor(123); // TypeError: 123 is not a symbol
```

③ 使用符号作为属性

凡是可以使用字符串或数值作为属性的地方，都可以使用符号。这就包括了对象字面量属性和 `Object.defineProperty()` / `Object.defineProperties()` 定义的属性。对象字面量只能在计算属性语法中使用符号作为属性。

```javascript
let s1 = Symbol('foo'),
    s2 = Symbol('bar'),
    s3 = Symbol('baz'),
    s4 = Symbol('qux');
let o = {
    [s1]: 'foo val'
};

// 这样也可以：o[s1] = 'foo val'; 
console.log(o);
// {Symbol(foo): foo val} 

Object.defineProperty(o, s2, { value: 'bar val' });
console.log(o);
// {Symbol(foo): foo val, Symbol(bar): bar val} 

Object.defineProperties(o, {
    [s3]: { value: 'baz val' },
    [s4]: { value: 'qux val' }
});
console.log(o);
// {Symbol(foo): foo val, Symbol(bar): bar val, Symbol(baz): baz val, Symbol(qux): qux val}
```

类似于 `Object.getOwnPropertyNames()` 返回对象实例的常规属性数组，`Object.getOwnPropertySymbols()` 返回对象实例的符号属性数组。这两个方法的返回值彼此互斥。`Object.getOwnPropertyDescriptors()` 会返回同时包含常规和符号属性描述符的对象。`Reflect.ownKeys()` 会返回两种类型的键：

```javascript
let s1 = Symbol('foo'),
    s2 = Symbol('bar');
let o = {
    [s1]: 'foo val',
    [s2]: 'bar val',
    baz: 'baz val',
    qux: 'qux val'
};

console.log(Object.getOwnPropertySymbols(o));
// [Symbol(foo), Symbol(bar)] 
console.log(Object.getOwnPropertyNames(o));
// ["baz", "qux"] 
console.log(Object.getOwnPropertyDescriptors(o));
// {baz: {...}, qux: {...}, Symbol(foo): {...}, Symbol(bar): {...}} 
console.log(Reflect.ownKeys(o));
// ["baz", "qux", Symbol(foo), Symbol(bar)]
```

因为符号属性是对内存中符号的一个引用，所以直接创建并用作属性的符号不会丢失。但是，如果没有显式地保存对这些属性的引用，那么必须遍历对象的所有符号属性才能找到相应的属性键：

```javascript
let o = {
    [Symbol('foo')]: 'foo val',
    [Symbol('bar')]: 'bar val'
};

console.log(o);
// {Symbol(foo): "foo val", Symbol(bar): "bar val"} 

let barSymbol = Object.getOwnPropertySymbols(o)
    .find((symbol) => symbol.toString().match(/bar/));

console.log(barSymbol);
// Symbol(bar)
```

④ 常用内置符号

ECMAScript 6 也引入了一批常用内置符号（well-known symbol），用于暴露语言内部行为，开发者可以直接访问、重写或模拟这些行为。这些内置符号都以 Symbol 工厂函数字符串属性的形式存在。

这些内置符号最重要的用途之一是重新定义它们，从而改变原生结构的行为。比如，我们知道 for-of 循环会在相关对象上使用 `Symbol.iterator` 属性，那么就可以通过在自定义对象上重新定义 `Symbol.iterator` 的值，来改变 for-of 在迭代该对象时的行为。

这些内置符号也没有什么特别之处，它们就是全局函数 Symbol 的普通字符串属性，指向一个符号的实例。所有内置符号属性都是不可写、不可枚举、不可配置的。

> 在提到 ECMAScript 规范时，经常会引用符号在规范中的名称，前缀为 `@@`。比如， `@@iterator` 指的就是 `Symbol.iterator`。

⑤  `Symbol.asyncIterator`

根据 ECMAScript 规范，这个符号作为一个属性表示“一个方法，该方法返回对象默认的 `AsyncIterator`。由 for-await-of 语句使用”。换句话说，这个符号表示实现异步迭代器 API 的函数。

for-await-of 循环会利用这个函数执行异步迭代操作。循环时，它们会调用以 `Symbol.asyncIterator` 为键的函数，并期望这个函数会返回一个实现迭代器 API 的对象。很多时候，返回的对象是实现该 API 的 `AsyncGenerator`：

TODO 现在看不懂 Symbol  P73



#### Object 类型

ECMAScript 中的对象其实就是一组数据和功能的集合。对象通过 new 操作符后跟对象类型的名称来创建。开发者可以通过创建 Object 类型的实例来创建自己的对象，然后再给对象添加属性和方法：

```javascript
let o = new Object(); 
```

这个语法类似 Java，但 ECMAScript 只要求在给构造函数提供参数时使用括号。如果没有参数，如上面的例子所示，那么完全可以省略括号（不推荐）：

```javascript
let o = new Object; // 合法，但不推荐
```

Object 的实例本身并不是很有用，但理解与它相关的概念非常重要。类似 Java 中的 java.lang.Object，ECMAScript 中的 Object 也是派生其他对象的基类。Object 类型的所有属性和方法在派生的对象上同样存在。

每个 Object 实例都有如下属性和方法：

+ `constructor`：用于创建当前对象的函数。在前面的例子中，这个属性的值就是 `Object()`  函数。
+ `hasOwnProperty(propertyName)`：用于判断当前对象实例（不是原型）上是否存在给定的属性。要检查的属性名必须是字符串（如 `o.hasOwnProperty("name")`）或符号。
+ `isPrototypeOf(object)`：用于判断当前对象是否为另一个对象的原型。
+ `propertyIsEnumerable(propertyName)`：用于判断给定的属性是否可以使用 for-in 语句枚举。与 `hasOwnProperty()` 一样，属性名必须是字符串。
+ `toLocaleString()`：返回对象的字符串表示，该字符串反映对象所在的本地化执行环境。
+ `valueOf()`：返回对象对应的字符串、数值或布尔值表示。通常与 `toString()` 的返回值相同。

因为在 ECMAScript 中 Object 是所有对象的基类，所以任何对象都有这些属性和方法。

> 严格来讲，ECMA-262 中对象的行为不一定适合 JavaScript 中的其他对象。比如浏览器环境中的 BOM 和 DOM 对象，都是由宿主环境定义和提供的宿主对象。而宿主对象不受 ECMA-262 约束，所以它们可能会也可能不会继承 Object。

### 操作符

ECMA-262 描述了一组可用于操作数据值的操作符，包括数学操作符（如加、减）、位操作符、关系操作符和相等操作符等。ECMAScript 中的操作符是独特的，因为它们可用于各种值，包括字符串、数值、布尔值，甚至还有对象。在应用给对象时，操作符通常会调用 `valueOf()` 和（或） `toString()` 方法来取得可以计算的值。

#### 一元操作符

只操作一个值的操作符叫一元操作符（unary operator）。一元操作符是 ECMAScript中最简单的操作符。

① 递增/递减操作符

递增和递减操作符直接照搬自 C 语言，但有两个版本：前缀版和后缀版。顾名思义，前缀版就是位于要操作的变量前头，后缀版就是位于要操作的变量后头。前缀递增操作符会给数值加 1，把两个加号（`++`）放到变量前头即可：

```javascript
let age = 29; 
++age;
```

在这个例子中，前缀递增操作符把 `age` 的值变成了 30（给之前的值 29 加 1）。因此，它实际上等于如下表达式：

```javascript
let age = 29; 
age = age + 1; 
```

前缀递减操作符也类似，只不过是从一个数值减 1。使用前缀递减操作符，只要把两个减号（`--`）放到变量前头即可：

```javascript
let age = 29; 
--age; 
```

执行操作后，变量 `age` 的值变成了 28（从 29 减 1）。

无论使用前缀递增还是前缀递减操作符，变量的值都会在语句被求值之前改变。（在计算机科学中，这通常被称为具有副作用。）请看下面的例子：

```javascript
let age = 29; 
let anotherAge = --age + 2; 
console.log(age); // 28 
console.log(anotherAge); // 30
```

在这个例子中，变量 `anotherAge` 以 `age` 减 1 后的值再加 2 进行初始化。因为递减操作先发生，所以 `age` 的值先变成 28，然后再加 2，结果是 30。

前缀递增和递减在语句中的优先级是相等的，因此会从左到右依次求值。比如：

```javascript
let num1 = 2; 
let num2 = 20; 
let num3 = --num1 + num2; 
let num4 = num1 + num2; 
console.log(num3); // 21 
console.log(num4); // 21
```

这里，`num3` 等于 21 是因为 `num1` 先减 1 之后才加 `num2`。变量 `num4` 也是 21，那是因为加法使用的也是递减后的值。

递增和递减的后缀版语法一样（分别是 `++` 和 `--`），只不过要放在变量后面。后缀版与前缀版的主要区别在于，后缀版递增和递减在语句被求值后才发生。在某些情况下，这种差异没什么影响，比如：

```javascript
let age = 29; 
age++; 
```

把递增操作符放到变量后面不会改变语句执行的结果，因为递增是唯一的操作。可是，在跟其他操作混合时，差异就会变明显，比如：

```javascript
let num1 = 2; 
let num2 = 20; 
let num3 = num1-- + num2; 
let num4 = num1 + num2;

console.log(num3); // 22 
console.log(num4); // 21 
```

这个例子跟前面的那个一样，只是把前缀递减改成了后缀递减，区别很明显。在使用前缀版的例子中，`num3` 和 `num4` 的值都是 21。而在这个例子中，`num3` 的值是 22，`num4` 的值是 21。这里的不同之处在于，计算 `num3` 时使用的是 `num1` 的原始值（2），而计算 `num4` 时使用的是 `num1` 递减后的值（1）。

这 4 个操作符可以作用于任何值，意思是不限于整数——字符串、布尔值、浮点值，甚至对象都可以。递增和递减操作符遵循如下规则。

+ 对于字符串，如果是有效的数值形式，则转换为数值再应用改变。变量类型从字符串变成数值。
+ 对于字符串，如果不是有效的数值形式，则将变量的值设置为 NaN 。变量类型从字符串变成数值。
+ 对于布尔值，如果是 false，则转换为 0 再应用改变。变量类型从布尔值变成数值。
+ 对于布尔值，如果是 true，则转换为 1 再应用改变。变量类型从布尔值变成数值。
+ 对于浮点值，加 1 或减 1。
+ 如果是对象，则调用其 `valueOf()` 方法取得可以操作的值。对得到的值应用上述规则。如果是 NaN，则调用  `toString()` 并再次应用其他规则。变量类型从对象变成数值。

下面的例子演示了这些规则：

```javascript
let s1 = "2";
let s2 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
        return -1;
    }
};

s1++; // 值变成数值 3 
s2++; // 值变成 NaN 
b++; // 值变成数值 1 
f--; // 值变成 0.10000000000000009（因为浮点数不精确）
o--; // 值变成-2 
```

② 一元加和减

一元加和减操作符对大多数开发者来说并不陌生，它们在 ECMAScript 中跟在数学中的用途一样。一元加由一个加号（`+`）表示，放在变量前头，对数值没有任何影响：

```javascript
let num = 25; 
num = +num; 
console.log(num); // 25 
```

如果将一元加应用到非数值，则会执行与使用 `Number()` 转型函数一样的类型转换：布尔值 `false` 和 `true` 转换为 0 和 1，字符串根据特殊规则进行解析，对象会调用它们的 `valueOf()` 和（或） `toString()` 方法以得到可以转换的值。

下面的例子演示了一元加在应用到不同数据类型时的行为：

```javascript
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
        return -1;
    }
};

s1 = +s1; // 值变成数值 1 
s2 = +s2; // 值变成数值 1.1 
s3 = +s3; // 值变成 NaN 
b = +b; // 值变成数值 0 
f = +f; // 不变，还是 1.1 
o = +o; // 值变成数值-1 
```

一元减由一个减号（`-`）表示，放在变量前头，主要用于把数值变成负值，如把 1 转换为 -1。示例如下：

```javascript
let num = 25; 
num = -num; 
console.log(num); // -25
```

对数值使用一元减会将其变成相应的负值（如上面的例子所示）。在应用到非数值时，一元减会遵循与一元加同样的规则，先对它们进行转换，然后再取负值：

```javascript
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
        return -1;
    }
};

s1 = -s1; // 值变成数值-1 
s2 = -s2; // 值变成数值-1.1 
s3 = -s3; // 值变成 NaN 
b = -b; // 值变成数值 0 
f = -f; // 变成-1.1 
o = -o; // 值变成数值 1 
```

一元加和减操作符主要用于基本的算术，但也可以像上面的例子那样，用于数据类型转换。

#### 位操作符

接下来要介绍的操作符用于数值的底层操作，也就是操作内存中表示数据的比特（位）。ECMAScript 中的所有数值都以 IEEE 754 64 位格式存储，但位操作并不直接应用到 64 位表示，而是先把值转换为 32 位整数，再进行位操作，之后再把结果转换为 64 位。对开发者而言，就好像只有 32 位整数一样，因为 64 位整数存储格式是不可见的。既然知道了这些，就只需要考虑 32 位整数即可。

有符号整数使用 32 位的前 31 位表示整数值。第 32 位表示数值的符号，如 0 表示正，1 表示负。这一位称为符号位（sign bit），它的值决定了数值其余部分的格式。正值以真正的二进制格式存储，即 31 位中的每一位都代表 2 的幂。第一位（称为第 0 位）表示 2<sup>0</sup>，第二位表示 2<sup>1</sup>，依此类推。如果一个位是空的，则以 0 填充，相当于忽略不计。比如，数值 18 的二进制格式为 00000000000000000000000000010010，或更精简的 10010。后者是用到的 5 个有效位，决定了实际的值。

负值以一种称为二补数（或补码）的二进制编码存储。一个数值的二补数通过如下 3 个步骤计算得到：

1. 确定绝对值的二进制表示（如，对于 -18，先确定 18 的二进制表示）
2. 找到数值的一补数（或反码），换句话说，就是每个 0 都变成 1，每个 1 都变成 0；
3. 给结果加 1。

基于上述步骤确定 -18 的二进制表示，首先从 18 的二进制表示开始：

```
0000 0000 0000 0000 0000 0000 0001 0010
```

然后，计算一补数，即反转每一位的二进制值：

```
1111 1111 1111 1111 1111 1111 1110 1101
```

最后，给一补数加 1：

```
 1111 1111 1111 1111 1111 1111 1110 1101 
+                                      1 
-----------------------------------------
 1111 1111 1111 1111 1111 1111 1110 1110
```

那么，-18 的二进制表示就是 11111111111111111111111111101110。要注意的是，在处理有符号整数时，我们无法访问第 31 位。

ECMAScript 会帮我们记录这些信息。在把负值输出为一个二进制字符串时，我们会得到一个前面加了减号的绝对值，如下所示：

```javascript
let num = -18; 
console.log(num.toString(2)); // "-10010"
```

在将 -18 转换为二进制字符串时，结果得到 -10010。转换过程会求得二补数，然后再以更符合逻辑的形式表示出来。

> 默认情况下，ECMAScript 中的所有整数都表示为有符号数。不过，确实存在无符号整数。对无符号整数来说，第 32 位不表示符号，因为只有正值。无符号整数比有符号整数的范围更大，因为符号位被用来表示数值了。

在对 ECMAScript 中的数值应用位操作符时，后台会发生转换：64 位数值会转换为 32 位数值，然后执行位操作，最后再把结果从 32 位转换为 64 位存储起来。整个过程就像处理 32 位数值一样，这让二进制操作变得与其他语言中类似。但这个转换也导致了一个奇特的副作用，即特殊值 `NaN` 和 `Infinity` 在位操作中都会被当成 0 处理。

如果将位操作符应用到非数值，那么首先会使用 `Number()` 函数将该值转换为数值（这个过程是自动的），然后再应用位操作。最终结果是数值。

① 按位非

按位非操作符用波浪符（`~`）表示，它的作用是返回数值的一补数。按位非是 ECMAScript 中为数不多的几个二进制数学操作符之一。看下面的例子：

```javascript
let num1 = 25; // 二进制 00000000000000000000000000011001 
let num2 = ~num1; // 二进制 11111111111111111111111111100110 
console.log(num2); // -26
```

这里，按位非操作符作用到了数值 25，得到的结果是 -26。由此可以看出，按位非的最终效果是数值取反并减 1，就像执行如下操作的结果一样：

```javascript
let num1 = 25; 
let num2 = -num1 - 1; 
console.log(num2); // "-26"
```

实际上，尽管两者返回的结果一样，但位操作的速度快得多。这是因为位操作是在数值的底层表示上完成的。

② 按位与

按位与操作符用和号（ `&` ）表示，有两个操作数。本质上，按位与就是将两个数的每一个位对齐，然后基于真值表中的规则，对每一位执行相应的与操作。

| 第一个数值的位 | 第二个数值的位 | 结果 |
| -------------- | -------------- | ---- |
| 1              | 1              | 1    |
| 1              | 0              | 0    |
| 0              | 1              | 0    |
| 0              | 0              | 0    |

按位与操作在两个位都是 1 时返回 1，在任何一位是 0 时返回 0。

下面看一个例子，我们对数值 25 和 3 求与操作，如下所示：

```javascript
let result = 25 & 3; 
console.log(result); // 1 
```

25 和 3 的按位与操作的结果是 1。为什么呢？看下面的二进制计算过程：

```
25  = 0000 0000 0000 0000 0000 0000 0001 1001 
 3  = 0000 0000 0000 0000 0000 0000 0000 0011 
--------------------------------------------- 
AND = 0000 0000 0000 0000 0000 0000 0000 0001
```

如上所示，25 和 3 的二进制表示中，只有第 0 位上的两个数都是 1。于是结果数值的所有其他位都会以 0 填充，因此结果就是 1。

③ 按位或

按位或操作符用管道符（ `|` ）表示，同样有两个操作数。按位或遵循如下真值表：

| 第一个数值的位 | 第二个数值的位 | 结果 |
| -------------- | -------------- | ---- |
| 1              | 1              | 1    |
| 1              | 0              | 1    |
| 0              | 1              | 1    |
| 0              | 0              | 0    |

按位或操作在至少一位是 1 时返回 1，两位都是 0 时返回 0。

仍然用按位与的示例，如果对 25 和 3 执行按位或，代码如下所示：

```javascript
let result = 25 | 3; 
console.log(result); // 27
```

可见 25 和 3 的按位或操作的结果是 27：

```
25  = 0000 0000 0000 0000 0000 0000 0001 1001 
 3  = 0000 0000 0000 0000 0000 0000 0000 0011 
--------------------------------------------- 
 OR = 0000 0000 0000 0000 0000 0000 0001 1011 
```

在参与计算的两个数中，有 4 位都是 1，因此它们直接对应到结果上。二进制码 11011 等于 27。

④ 按位异或

按位异或用脱字符（ `^` ）表示，同样有两个操作数。下面是按位异或的真值表：

| 第一个数值的位 | 第二个数值的位 | 结果 |
| -------------- | -------------- | ---- |
| 1              | 1              | 0    |
| 1              | 0              | 1    |
| 0              | 1              | 1    |
| 0              | 0              | 0    |

按位异或与按位或的区别是，它只在一位上是 1 的时候返回 1（两位都是 1 或 0，则返回 0）。

对数值 25 和 3 执行按位异或操作：

```javascript
let result = 25 ^ 3; 
console.log(result); // 26
```

可见，25 和 3 的按位异或操作结果为 26，如下所示：

```
25  = 0000 0000 0000 0000 0000 0000 0001 1001 
 3  = 0000 0000 0000 0000 0000 0000 0000 0011 
--------------------------------------------- 
XOR = 0000 0000 0000 0000 0000 0000 0001 1010
```

两个数的第 0 位都是 1，因此那一位在结果中就变成了 0。其余位上的 1 在另一个数上没有对应的 1，因此会直接传递到结果中。二进制码 11010 等于 26。（注意，这比对同样两个值执行按位或操作得到的结果小 1。）

⑤ 左移

左移操作符用两个小于号（ `<<` ）表示，会按照指定的位数将数值的所有位向左移动。比如，如果数值 2（二进制 10）向左移 5 位，就会得到 64（二进制 1000000），如下所示：

```javascript
let oldValue = 2; // 等于二进制 10 
let newValue = oldValue << 5; // 等于二进制 1000000，即十进制 64
```

注意在移位后，数值右端会空出 5 位。左移会以 0 填充这些空位，让结果是完整的 32 位数值。

注意，左移会保留它所操作数值的符号。比如，如果 -2 左移 5 位，将得到 -64，而不是正 64。

⑥ 有符号右移

有符号右移由两个大于号（ `>>` ）表示，会将数值的所有 32 位都向右移，同时保留符号（正或负）。有符号右移实际上是左移的逆运算。比如，如果将 64 右移 5 位，那就是 2：

```javascript
let oldValue = 64; // 等于二进制 1000000 
let newValue = oldValue >> 5; // 等于二进制 10，即十进制 2 
```

同样，移位后就会出现空位。不过，右移后空位会出现在左侧，且在符号位之后。ECMAScript 会用符号位的值来填充这些空位，以得到完整的数值。

⑦ 无符号右移

无符号右移用 3 个大于号表示（ `>>>` ），会将数值的所有 32 位都向右移。对于正数，无符号右移与有符号右移结果相同。仍然以前面有符号右移的例子为例，64 向右移动 5 位，会变成 2：

```javascript
let oldValue = 64; // 等于二进制 1000000 
let newValue = oldValue >>> 5; // 等于二进制 10，即十进制 2
```

对于负数，有时候差异会非常大。与有符号右移不同，无符号右移会给空位补 0，而不管符号位是什么。对正数来说，这跟有符号右移效果相同。但对负数来说，结果就差太多了。无符号右移操作符将负数的二进制表示当成正数的二进制表示来处理。因为负数是其绝对值的二补数，所以右移之后结果变得非常之大，如下面的例子所示：

```javascript
let oldValue = -64; // 等于二进制 11111111111111111111111111000000 
let newValue = oldValue >>> 5; // 等于十进制 134217726 
```

在对 -64 无符号右移 5 位后，结果是 134 217 726。这是因为 -64 的二进制表示是 11111111111111111111111111000000，无符号右移却将它当成正值，也就是 4 294 967 232。把这个值右移 5 位后，结果是 00000111111111111111111111111110，即 134 217 726。

#### 布尔操作符

对于编程语言来说，布尔操作符跟相等操作符几乎同样重要。如果没有能力测试两个值的关系，那么像 if-else 和循环这样的语句也没什么用了。布尔操作符一共有 3 个：逻辑非、逻辑与和逻辑或。

① 逻辑非

逻辑非操作符由一个叹号（ `!` ）表示，可应用给 ECMAScript 中的任何值。这个操作符始终返回布尔值，无论应用到的是什么数据类型。逻辑非操作符首先将操作数转换为布尔值，然后再对其取反。换句话说，逻辑非操作符会遵循如下规则。

+ 如果操作数是对象，则返回 false。
+ 如果操作数是空字符串，则返回 true。
+ 如果操作数是非空字符串，则返回 false。
+ 如果操作数是数值 0，则返回 true。
+ 如果操作数是非 0 数值（包括 Infinity），则返回 false。
+ 如果操作数是 null，则返回 true。
+ 如果操作数是 NaN，则返回 true。
+ 如果操作数是 undefined，则返回 true。

以下示例验证了上述行为：

```javascript
console.log(!false); // true 
console.log(!"blue"); // false 
console.log(!0); // true 
console.log(!NaN); // true 
console.log(!""); // true 
console.log(!12345); // false
```

逻辑非操作符也可以用于把任意值转换为布尔值。同时使用两个叹号（ `!!` ），相当于调用了转型函数 `Boolean()` 。无论操作数是什么类型，第一个叹号总会返回布尔值。第二个叹号对该布尔值取反，从而给出变量真正对应的布尔值。结果与对同一个值使用 `Boolean()` 函数是一样的：

```javascript
console.log(!!"blue"); // true 
console.log(!!0); // false 
console.log(!!NaN); // false 
console.log(!!""); // false 
console.log(!!12345); // true 
```

② 逻辑与

逻辑与操作符由两个和号（ `&&` ）表示，应用到两个值，如下所示：

```javascript
let result = true && false; 
```

逻辑与操作符遵循如下真值表：

| 第一个操作数 | 第二个操作数 | 结果  |
| ------------ | ------------ | ----- |
| true         | true         | true  |
| true         | false        | false |
| false        | true         | false |
| false        | false        | false |

逻辑与操作符可用于任何类型的操作数，不限于布尔值。如果有操作数不是布尔值，则逻辑与并不一定会返回布尔值，而是遵循如下规则。

+ 如果第一个操作数是对象，则返回第二个操作数。
+ 如果第二个操作数是对象，则只有第一个操作数求值为 true 才会返回该对象。
+ 如果两个操作数都是对象，则返回第二个操作数。
+ 如果有一个操作数是 null，则返回 null。
+ 如果有一个操作数是 NaN，则返回 NaN。
+ 如果有一个操作数是 undefined，则返回 undefined。

逻辑与操作符是一种短路操作符，意思就是如果第一个操作数决定了结果，那么永远不会对第二个操作数求值。对逻辑与操作符来说，如果第一个操作数是 false，那么无论第二个操作数是什么值，结果也不可能等于 true。看下面的例子：

```javascript
let found = true; 
let result = (found && someUndeclaredVariable); // 这里会出错
console.log(result); // 不会执行这一行
```

上面的代码之所以会出错，是因为 `someUndeclaredVariable` 没有事先声明，所以当逻辑与操作符对它求值时就会报错。变量 `found` 的值是 true，逻辑与操作符会继续求值变量 `someUndeclaredVariable`。但是由于 `someUndeclaredVariable` 没有定义，不能对它应用逻辑与操作符，因此就报错了。假如变量 `found` 的值是 false，那么就不会报错了：

```javascript
let found = false; 
let result = (found && someUndeclaredVariable); // 不会出错
console.log(result); // 会执行
```

这里，`console.log` 会成功执行。即使变量 `someUndeclaredVariable` 没有定义，由于第一个操作数是 false，逻辑与操作符也不会对它求值，因为此时对 `&&` 右边的操作数求值是没有意义的。在使用逻辑与操作符时，一定别忘了它的这个短路的特性。

③ 逻辑或

逻辑或操作符由两个管道符（ `||` ）表示，比如：

```javascript
let result = true || false; 
```

逻辑或操作符遵循如下真值表：

| 第一个操作数 | 第二个操作数 | 结果  |
| ------------ | ------------ | ----- |
| true         | true         | true  |
| true         | false        | true  |
| false        | true         | true  |
| false        | false        | false |

与逻辑与类似，如果有一个操作数不是布尔值，那么逻辑或操作符也不一定返回布尔值。它遵循如下规则。

+ 如果第一个操作数是对象，则返回第一个操作数。
+ 如果第一个操作数求值为 false，则返回第二个操作数。
+ 如果两个操作数都是对象，则返回第一个操作数。
+ 如果两个操作数都是 null，则返回 null。
+ 如果两个操作数都是 NaN，则返回 NaN。
+ 如果两个操作数都是 undefined，则返回 undefined。

同样与逻辑与类似，逻辑或操作符也具有短路的特性。只不过对逻辑或而言，第一个操作数求值为 true，第二个操作数就不会再被求值了。看下面的例子：

```javascript
let found = true; 
let result = (found || someUndeclaredVariable); // 不会出错
console.log(result); // 会执行
```

跟前面的例子一样，变量 `someUndeclaredVariable` 也没有定义。但是，因为变量 `found` 的值为 true，所以逻辑或操作符不会对变量 `someUndeclaredVariable` 求值，而直接返回 true。假如把 `found` 的值改为 false，那就会报错了：

```javascript
let found = false; 
let result = (found || someUndeclaredVariable); // 这里会出错
console.log(result); // 不会执行这一行
```

利用这个行为，可以避免给变量赋值 null 或 undefined。比如：

```javascript
let myObject = preferredObject || backupObject; 
```

在这个例子中，变量 `myObject` 会被赋予两个值中的一个。其中，`preferredObject` 变量包含首选的值，`backupObject` 变量包含备用的值。如果 `preferredObject` 不是 null，则它的值就会赋给 `myObject`；如果 `preferredObject` 是 null，则 `backupObject` 的值就会赋给 `myObject`。这种模式在 ECMAScript 代码中经常用于变量赋值。

#### 乘性操作符

ECMAScript 定义了 3 个乘性操作符：乘法、除法和取模。这些操作符跟它们在 Java、C 语言及 Perl 中对应的操作符作用一样，但在处理非数值时，它们也会包含一些自动的类型转换。如果乘性操作符有不是数值的操作数，则该操作数会在后台被使用 `Number()` 转型函数转换为数值。这意味着空字符串会被当成 0，而布尔值 true 会被当成 1。

①  乘法操作符

乘法操作符由一个星号（ `*` ）表示，可以用于计算两个数值的乘积。其语法类似于 C 语言，比如：

```javascript
let result = 34 * 56; 
```

不过，乘法操作符在处理特殊值时也有一些特殊的行为。

+ 如果操作数都是数值，则执行常规的乘法运算，即两个正值相乘是正值，两个负值相乘也是正值，正负符号不同的值相乘得到负值。如果 ECMAScript 不能表示乘积，则返回 Infinity 或 -Infinity。
+ 如果有任一操作数是 NaN，则返回 NaN。
+ 如果是 Infinity 乘以 0，则返回 NaN。
+ 如果是 Infinity 乘以非 0的有限数值，则根据第二个操作数的符号返回 Infinity 或 -Infinity。
+ 如果是 Infinity 乘以 Infinity，则返回 Infinity。
+ 如果有不是数值的操作数，则先在后台用 `Number()` 将其转换为数值，然后再应用上述规则。

② 除法操作符

除法操作符由一个斜杠（ `/` ）表示，用于计算第一个操作数除以第二个操作数的商，比如：

```javascript
let result = 66 / 11; 
```

跟乘法操作符一样，除法操作符针对特殊值也有一些特殊的行为。

+ 如果操作数都是数值，则执行常规的除法运算，即两个正值相除是正值，两个负值相除也是正值，符号不同的值相除得到负值。如果 ECMAScriptm不能表示商，则返回 Infinity 或 -Infinity。
+ 如果有任一操作数是 NaN，则返回 NaN。
+ 如果是 Infinity 除以 Infinity，则返回 NaN。
+ 如果是 0 除以 0，则返回 NaN。
+ 如果是非 0 的有限值除以 0，则根据第一个操作数的符号返回 Infinity 或 -Infinity。
+ 如果是 Infinity 除以任何数值，则根据第二个操作数的符号返回 Infinity 或-Infinity。
+ 如果有不是数值的操作数，则先在后台用 `Number()` 函数将其转换为数值，然后再应用上述规则。

③ 取模操作符

取模（余数）操作符由一个百分比符号（ `%` ）表示，比如：

```javascript
let result = 26 % 5; // 等于 1
```

与其他乘性操作符一样，取模操作符对特殊值也有一些特殊的行为。

+ 如果操作数是数值，则执行常规除法运算，返回余数。
+ 如果被除数是无限值，除数是有限值，则返回 NaN。
+ 如果被除数是有限值，除数是 0，则返回 NaN。
+ 如果是 Infinity 除以 Infinity，则返回 NaN。
+ 如果被除数是有限值，除数是无限值，则返回被除数。
+ 如果被除数是 0，除数不是 0，则返回 0。
+ 如果有不是数值的操作数，则先在后台用 `Number()` 函数将其转换为数值，然后再应用上述规则。

#### 指数操作符

ECMAScript 7 新增了指数操作符，`Math.pow()` 现在有了自己的操作符 `**`，结果是一样的：

```javascript
console.log(Math.pow(3, 2); // 9 
console.log(3 ** 2); // 9 
console.log(Math.pow(16, 0.5); // 4 
console.log(16** 0.5); // 4
```

不仅如此，指数操作符也有自己的指数赋值操作符 `**=`，该操作符执行指数运算和结果的赋值操作：

```javascript
let squared = 3; 
squared **= 2; 
console.log(squared); // 9
let sqrt = 16; 
sqrt **= 0.5; 
console.log(sqrt); // 4
```

#### 加性操作符

加性操作符，即加法和减法操作符，一般都是编程语言中最简单的操作符。不过，在 ECMAScript 中，这两个操作符拥有一些特殊的行为。与乘性操作符类似，加性操作符在后台会发生不同数据类型的转换。只不过对这两个操作符来说，转换规则不是那么直观。

① 加法操作符

加法操作符（ `+` ）用于求两个数的和，比如：

```javascript
let result = 1 + 2; 
```


如果两个操作数都是数值，加法操作符执行加法运算并根据如下规则返回结果：

+ 如果有任一操作数是 NaN，则返回 NaN；
+ 如果是 Infinity 加 Infinity，则返回 Infinity；
+ 如果是 -Infinity 加 -Infinity，则返回 -Infinity；
+ 如果是 Infinity 加 -Infinity，则返回 NaN；
+ 如果是 +0 加 +0，则返回 +0；
+ 如果是 -0 加 +0，则返回 +0；
+ 如果是 -0 加 -0，则返回 -0。

不过，如果有一个操作数是字符串，则要应用如下规则：

+ 如果两个操作数都是字符串，则将第二个字符串拼接到第一个字符串后面；
+ 如果只有一个操作数是字符串，则将另一个操作数转换为字符串，再将两个字符串拼接在一起。

如果有任一操作数是对象、数值或布尔值，则调用它们的 `toString()` 方法以获取字符串，然后再应用前面的关于字符串的规则。对于 undefined 和 null，则调用 `String()` 函数，分别获取 "undefined" 和 "null"。

看下面的例子：

```javascript
let result1 = 5 + 5; // 两个数值
console.log(result1); // 10 
let result2 = 5 + "5"; // 一个数值和一个字符串
console.log(result2); // "55"
```

以上代码展示了加法操作符的两种运算模式。正常情况下，5 + 5 等于 10（数值），如前两行代码所示。但是，如果将一个操作数改为字符串，比如 "5"，则相加的结果就变成了 "55"（原始字符串值），因为第一个操作数也会被转换为字符串。

ECMAScript 中最常犯的一个错误，就是忽略加法操作中涉及的数据类型。比如下面这个例子：

```javascript
let num1 = 5; 
let num2 = 10; 
let message = "The sum of 5 and 10 is " + num1 + num2; 
console.log(message); // "The sum of 5 and 10 is 510"
```

这里，变量 `message` 中保存的是一个字符串，是执行两次加法操作之后的结果。有人可能会认为最终得到的字符串是 "The sum of 5 and 10 is 15"。可是，实际上得到的是 "The sum of 5 and 10  is 510"。这是因为每次加法运算都是独立完成的。第一次加法的操作数是一个字符串和一个数值（5），结果还是一个字符串。第二次加法仍然是用一个字符串去加一个数值（10），同样也会得到一个字符串。如果想真正执行数学计算，然后把结果追加到字符串末尾，只要使用一对括号即可：

```javascript
let num1 = 5; 
let num2 = 10; 
let message = "The sum of 5 and 10 is " + (num1 + num2); 
console.log(message); // "The sum of 5 and 10 is 15"
```

在此，我们用括号把两个数值变量括了起来，意思是让解释器先执行两个数值的加法，然后再把结果追加给字符串。因此，最终得到的字符串变成了 "The sum of 5 and 10 is 15"。

② 减法操作符

减法操作符（ `-` ）也是使用很频繁的一种操作符，比如：

```javascript
let result = 2 - 1;
```

 与加法操作符一样，减法操作符也有一组规则用于处理 ECMAScript 中不同类型之间的转换。

+ 如果两个操作数都是数值，则执行数学减法运算并返回结果。
+ 如果有任一操作数是 NaN，则返回 NaN。
+ 如果是 Infinity 减 Infinity，则返回 NaN。
+ 如果是-Infinity 减 -Infinity，则返回 NaN。
+ 如果是 Infinity 减 -Infinity，则返回 Infinity。
+ 如果是 -Infinity 减 Infinity，则返回 -Infinity。
+ 如果是 +0 减 +0，则返回 +0。
+ 如果是 +0 减 -0，则返回 -0。
+ 如果是 -0 减 -0，则返回 +0。
+ 如果有任一操作数是字符串、布尔值、null 或 undefined，则先在后台使用 `Number()` 将其转换为数值，然后再根据前面的规则执行数学运算。如果转换结果是 NaN，则减法计算的结果是 NaN。
+ 如果有任一操作数是对象，则调用其 `valueOf()` 方法取得表示它的数值。如果该值是 NaN，则减法计算的结果是 NaN。如果对象没有 `valueOf()` 方法，则调用其 `toString()` 方法，然后再将得到的字符串转换为数值。

以下示例演示了上面的规则：

```javascript
let result1 = 5 - true; // true 被转换为 1，所以结果是 4 
let result2 = NaN - 1; // NaN 
let result3 = 5 - 3; // 2 
let result4 = 5 - ""; // ""被转换为 0，所以结果是 5 
let result5 = 5 - "2"; // "2"被转换为 2，所以结果是 3 
let result6 = 5 - null; // null 被转换为 0，所以结果是 5
```

#### 关系操作符

关系操作符执行比较两个值的操作，包括小于（ `<` ）、大于（ `>` ）、小于等于（ `<=` ）和大于等于（ `>=` ），用法跟数学课上学的一样。这几个操作符都返回布尔值，如下所示：

```javascript
let result1 = 5 > 3; // true 
let result2 = 5 < 3; // false
```

与 ECMAScript 中的其他操作符一样，在将它们应用到不同数据类型时也会发生类型转换和其他行为。

+ 如果操作数都是数值，则执行数值比较。
+ 如果操作数都是字符串，则逐个比较字符串中对应字符的编码。
+ 如果有任一操作数是数值，则将另一个操作数转换为数值，执行数值比较。
+ 如果有任一操作数是对象，则调用其 `valueOf()` 方法，取得结果后再根据前面的规则执行比较。
+ 如果没有 `valueOf()` 操作符，则调用 `toString()` 方法，取得结果后再根据前面的规则执行比较。
+ 如果有任一操作数是布尔值，则将其转换为数值再执行比较。

在使用关系操作符比较两个字符串时，会发生一个有趣的现象。很多人认为小于意味着“字母顺序靠前”，而大于意味着“字母顺序靠后”，实际上不是这么回事。对字符串而言，关系操作符会比较字符串中对应字符的编码，而这些编码是数值。比较完之后，会返回布尔值。问题的关键在于，大写字母的编码都小于小写字母的编码，因此以下这种情况就会发生：

```javascript
let result = "Brick" < "alphabet"; // true 
```

在这里，字符串 "Brick" 被认为小于字符串 "alphabet"，因为字母 B 的编码是 66，字母 a 的编码是 97。要得到确实按字母顺序比较的结果，就必须把两者都转换为相同的大小写形式（全大写或全小写），然后再比较：

```javascript
let result = "Brick".toLowerCase() < "alphabet".toLowerCase(); // false
```

将两个操作数都转换为小写，就能保证按照字母表顺序判定 "alphabet" 在 "Brick" 前头。另一个奇怪的现象是在比较两个数值字符串的时候，比如下面这个例子：

```javascript
let result = "23" < "3"; // true 
```

这里在比较字符串 "23" 和 "3" 时返回 true。因为两个操作数都是字符串，所以会逐个比较它们的字符编码（字符 "2" 的编码是 50，而字符 "3" 的编码是 51）。不过，如果有一个操作数是数值，那么比较的结果就对了：

```javascript
let result = "23" < 3; // false 
```

因为这次会将字符串 "23" 转换为数值 23，然后再跟 3 比较，结果当然对了。只要是数值和字符串比较，字符串就会先被转换为数值，然后进行数值比较。对于数值字符串而言，这样能保证结果正确。但如果字符串不能转换成数值呢？比如下面这个例子：

```javascript
let result = "a" < 3; // 因为"a"会转换为 NaN，所以结果是 false
```

因为字符 "a" 不能转换成任何有意义的数值，所以只能转换为 NaN。这里有一个规则，即任何关系操作符在涉及比较 NaN 时都返回 false。这样一来，下面的例子有趣了：

```javascript
let result1 = NaN < 3; // false 
let result2 = NaN >= 3; // false
```

在大多数比较的场景中，如果一个值不小于另一个值，那就一定大于或等于它。但在比较 NaN 时，无论是小于还是大于等于，比较的结果都会返回 false。

#### 相等操作符

判断两个变量是否相等是编程中最重要的操作之一。在比较字符串、数值和布尔值是否相等时，过程都很直观。但是在比较两个对象是否相等时，情形就比较复杂了。ECMAScript 中的相等和不相等操作符，原本在比较之前会执行类型转换，但很快就有人质疑这种转换是否应该发生。最终，ECMAScript 提供了两组操作符。第一组是等于和不等于，它们在比较之前执行转换。第二组是全等和不全等，它们在比较之前不执行转换。

① 等于和不等于

ECMAScript 中的等于操作符用两个等于号（ `==` ）表示，如果操作数相等，则会返回 true。不等于操作符用叹号和等于号（ `!=` ）表示，如果两个操作数不相等，则会返回 true。这两个操作符都会先进行类型转换（通常称为强制类型转换）再确定操作数是否相等。

在转换操作数的类型时，相等和不相等操作符遵循如下规则。

+ 如果任一操作数是布尔值，则将其转换为数值再比较是否相等。false 转换为 0，true 转换为 1。
+ 如果一个操作数是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否相等。
+ 如果一个操作数是对象，另一个操作数不是，则调用对象的 `valueOf()` 方法取得其原始值，再根据前面的规则进行比较。

在进行比较时，这两个操作符会遵循如下规则。

+ null 和 undefined 相等。
+ null 和 undefined 不能转换为其他类型的值再进行比较。
+ 如果有任一操作数是 NaN，则相等操作符返回 false，不相等操作符返回 true。记住：即使两个操作数都是 NaN，相等操作符也返回 false，因为按照规则，NaN 不等于 NaN。
+ 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回 true。否则，两者不相等。

下面总结了一些特殊情况及比较的结果。

```javascript
console.log(null == undefined) // true
console.log("NaN" == NaN)      // false
console.log(5 == NaN)          // false
console.log(NaN == NaN)        // false
console.log(NaN != NaN)        // true
console.log(false == 0)        // true
console.log(true == 1)         // true
console.log(true == 2)         // false
console.log(undefined == 0)    // false
console.log(null == 0)         // false
console.log("5" == 5)          // true
```

② 全等和不全等

全等和不全等操作符与相等和不相等操作符类似，只不过它们在比较相等时不转换操作数。全等操作符由 3 个等于号（ `===` ）表示，只有两个操作数在不转换的前提下相等才返回 true，比如：

```javascript
let result1 = ("55" == 55); // true，转换后相等
let result2 = ("55" === 55); // false，不相等，因为数据类型不同
```

在这个例子中，第一个比较使用相等操作符，比较的是字符串 "55" 和数值 55。如前所述，因为字符串 "55" 会被转换为数值 55，然后再与数值 55 进行比较，所以返回 true。第二个比较使用全等操作符，因为没有转换，字符串和数值当然不能相等，所以返回 false。

不全等操作符用一个叹号和两个等于号（ `!==` ）表示，只有两个操作数在不转换的前提下不相等才返回 true。比如：

```javascript
let result1 = ("55" != 55); // false，转换后相等
let result2 = ("55" !== 55); // true，不相等，因为数据类型不同
```

这一次，第一个比较使用不相等操作符，它会把字符串 "55" 转换为数值 55，跟第二个操作数相等。既然转换后两个值相等，那就返回 false。第二个比较使用不全等操作符。这时候可以这么问：“字符串 55 和数值 55 有区别吗？”答案是“有”（true）。

另外，虽然 `null == undefined` 是 true（因为这两个值类似），但 `null === undefined` 是 false，因为它们不是相同的数据类型。

> 由于相等和不相等操作符存在类型转换问题，因此推荐使用全等和不全等操作符。这样有助于在代码中保持数据类型的完整性。

#### 条件操作符

条件操作符是 ECMAScript 中用途最为广泛的操作符之一，语法跟 Java 中一样：

```javascript
variable = boolean_expression ? true_value : false_value; 
```

上面的代码执行了条件赋值操作，即根据条件表达式 `boolean_expression` 的值决定将哪个值赋给变量 `variable` 。如果 `boolean_expression` 是 true ，则赋值 `true_value` ；如果 `boolean_expression` 是 false，则赋值 `false_value`。比如：

```javascript
let max = (num1 > num2) ? num1 : num2; 
```

在这个例子中，`max` 将被赋予一个最大值。这个表达式的意思是，如果 `num1` 大于 `num2`（条件表达式为 true），则将 `num1` 赋给 `max`。否则，将 `num2` 赋给 `max`。

#### 赋值操作符

简单赋值用等于号（ `=` ）表示，将右手边的值赋给左手边的变量，如下所示：

```javascript
let num = 10;
```

复合赋值使用乘性、加性或位操作符后跟等于号（ `=` ）表示。这些赋值操作符是类似如下常见赋值操作的简写形式：

```javascript
let num = 10; 
num = num + 10; 
```

以上代码的第二行可以通过复合赋值来完成：

```javascript
let num = 10; 
num += 10;
```

每个数学操作符以及其他一些操作符都有对应的复合赋值操作符：

+ 乘后赋值（ `*=` ）
+ 除后赋值（ `/=` ）
+ 取模后赋值（ `%=` ）
+ 加后赋值（ `+=` ）
+ 减后赋值（ `-=` ）
+ 左移后赋值（ `<<=` ）
+ 右移后赋值（ `>>=` ）
+ 无符号右移后赋值（ `>>>=` ）

这些操作符仅仅是简写语法，使用它们不会提升性能。

#### 逗号操作符

逗号操作符可以用来在一条语句中执行多个操作，如下所示：

```javascript
let num1 = 1, num2 = 2, num3 = 3; 
```

在一条语句中同时声明多个变量是逗号操作符最常用的场景。不过，也可以使用逗号操作符来辅助赋值。在赋值时使用逗号操作符分隔值，最终会返回表达式中最后一个值：

```javascript
let num = (5, 1, 4, 8, 0); // num 的值为 0 
```

在这个例子中，`num` 将被赋值为 0，因为 0 是表达式中最后一项。逗号操作符的这种使用场景并不多见，但这种行为的确存在。

### 语句

ECMA-262 描述了一些语句（也称为流控制语句），而 ECMAScript 中的大部分语法都体现在语句中。语句通常使用一或多个关键字完成既定的任务。语句可以简单，也可以复杂。简单的如告诉函数退出，复杂的如列出一堆要重复执行的指令。

#### if 语句

if 语句是使用最频繁的语句之一，语法如下：

```javascript
if (condition) statement1 else statement2
```

这里的条件（condition）可以是任何表达式，并且求值结果不一定是布尔值。ECMAScript 会自动调用 `Boolean()` 函数将这个表达式的值转换为布尔值。如果条件求值为 true，则执行语句 statement1；如果条件求值为 false，则执行语句 statement2。这里的语句可能是一行代码，也可能是一个代码块（即包含在一对花括号中的多行代码）。来看下面的例子：

```javascript
if (i > 25)
    console.log("Greater than 25."); // 只有一行代码的语句
else {
    console.log("Less than or equal to 25."); // 一个语句块
}
```

这里的最佳实践是使用语句块，即使只有一行代码要执行也是如此。这是因为语句块可以避免对什么条件下执行什么产生困惑。

可以像这样连续使用多个 if 语句：

```javascript
if (condition1) statement1 else if (condition2) statement2 else statement3 
```

下面是一个例子：

```javascript
if (i > 25) {
    console.log("Greater than 25.");
} else if (i < 0) {
    console.log("Less than 0.");
} else {
    console.log("Between 0 and 25, inclusive.");
}
```

#### do-while 语句

do-while 语句是一种后测试循环语句，即循环体中的代码执行后才会对退出条件进行求值。换句话说，循环体内的代码至少执行一次。do-while 的语法如下：

```javascript
do { 
    statement 
} while (expression);
```

下面是一个例子：

```javascript
let i = 0;
do {
    i += 2;
} while (i < 10);
```

在这个例子中，只要 i 小于 10，循环就会重复执行。i 从 0 开始，每次循环递增 2。

> 后测试循环经常用于这种情形：循环体内代码在退出前至少要执行一次。

#### while 语句

while 语句是一种先测试循环语句，即先检测退出条件，再执行循环体内的代码。因此，while 循环体内的代码有可能不会执行。下面是 while 循环的语法：

```javascript
while(expression) statement
```

这是一个例子：

```javascript
let i = 0; 
while (i < 10) { 
    i += 2; 
}
```

在这个例子中，变量 i 从 0 开始，每次循环递增 2。只要 i 小于 10，循环就会继续。

#### for 语句

for 语句也是先测试语句，只不过增加了进入循环之前的初始化代码，以及循环执行后要执行的表达式，语法如下：

```javascript
for (initialization; expression; post-loop-expression) statement
```

下面是一个用例：

```javascript
let count = 10; 
for (let i = 0; i < count; i++) { 
    console.log(i); 
} 
```

以上代码在循环开始前定义了变量 i 的初始值为 0。然后求值条件表达式，如果求值结果为 true （ `i < count` ），则执行循环体。因此循环体也可能不会被执行。如果循环体被执行了，则循环后表达式也会执行，以便递增变量 i。for 循环跟下面的 while 循环是一样的：

```javascript
let count = 10;
let i = 0;
while (i < count) {
    console.log(i);
    i++;
}
```

无法通过 while 循环实现的逻辑，同样也无法使用 for 循环实现。因此 for 循环只是将循环相关的代码封装在了一起而已。

在 for 循环的初始化代码中，其实是可以不使用变量声明关键字的。不过，初始化定义的迭代器变量在循环执行完成后几乎不可能再用到了。因此，最清晰的写法是使用 `let` 声明迭代器变量，这样就可以将这个变量的作用域限定在循环中。

初始化、条件表达式和循环后表达式都不是必需的。因此，下面这种写法可以创建一个无穷循环：

```javascript
for (; ;) { // 无穷循环
    doSomething();
}
```

如果只包含条件表达式，那么 for 循环实际上就变成了 while 循环：

```javascript
let count = 10; 
let i = 0;
for (; i < count; ) {
    console.log(i);
    i++;
}
```

这种多功能性使得 for 语句在这门语言中使用非常广泛。

#### for-in 语句

for-in 语句是一种严格的迭代语句，用于枚举对象中的非符号键属性，语法如下：

```javascript
for (property in expression) statement
```

下面是一个例子：

```javascript
for (const propName in window) { 
    document.write(propName); 
} 
```

这个例子使用 for-in 循环显示了 BOM 对象 `window` 的所有属性。每次执行循环，都会给变量 `propName` 赋予一个 `window` 对象的属性作为值，直到 `window` 的所有属性都被枚举一遍。与 for 循环一样，这里控制语句中的 `const` 也不是必需的。但为了确保这个局部变量不被修改，推荐使用 `const`。

ECMAScript 中对象的属性是无序的，因此 for-in 语句不能保证返回对象属性的顺序。换句话说，所有可枚举的属性都会返回一次，但返回的顺序可能会因浏览器而异。

如果 for-in 循环要迭代的变量是 `null` 或 `undefined`，则不执行循环体。

#### for-of 语句

for-of 语句是一种严格的迭代语句，用于遍历可迭代对象的元素，语法如下：

```javascript
for (property of expression) statement
```

下面是示例：

```javascript
for (const el of [2,4,6,8]) { 
    document.write(el); 
}
```

在这个例子中，我们使用 for-of 语句显示了一个包含 4 个元素的数组中的所有元素。循环会一直持续到将所有元素都迭代完。与 for 循环一样，这里控制语句中的 `const` 也不是必需的。但为了确保这个局部变量不被修改，推荐使用 `const`。

for-of 循环会按照可迭代对象的 `next()` 方法产生值的顺序迭代元素。

如果尝试迭代的变量不支持迭代，则 for-of 语句会抛出错误。

> ES2018 对 for-of 语句进行了扩展，增加了 for-await-of 循环，以支持生成期 约（promise）的异步可迭代对象。

#### 标签语句

标签语句用于给语句加标签，语法如下：

```javascript
label: statement
```

下面是一个例子：

```javascript
start: for (let i = 0; i < count; i++) { 
    console.log(i); 
}
```

在这个例子中，start 是一个标签，可以在后面通过 break 或 continue 语句引用。标签语句的典型应用场景是嵌套循环。

#### break 和 continue 语句

break 和 continue 语句为执行循环代码提供了更严格的控制手段。其中，break 语句用于立即退出循环，强制执行循环后的下一条语句。而 continue 语句也用于立即退出循环，但会再次从循环顶部开始执行。下面看一个例子：

```javascript
let num = 0;
for (let i = 1; i < 10; i++) {
    if (i % 5 == 0) {
        break;
    }
    num++;
}
console.log(num); // 4 
```

在上面的代码中，for 循环会将变量 i 由 1 递增到 10。而在循环体内，有一个 if 语句用于检查 i 能否被 5 整除（使用取模操作符）。如果是，则执行 break 语句，退出循环。变量 num 的初始值为 0，表示循环在退出前执行了多少次。当 break 语句执行后，下一行执行的代码是 `console.log(num)`，显示 4。之所以循环执行了 4 次，是因为当 i 等于 5 时，break 语句会导致循环退出，该次循环不会执行递增 num 的代码。如果将 break 换成 continue，则会出现不同的效果：

```javascript
let num = 0;
for (let i = 1; i < 10; i++) {
    if (i % 5 == 0) {
        continue;
    }
    num++;
}
console.log(num); // 8 
```

这一次，`console.log` 显示 8，即循环被完整执行了 8 次。当 i 等于 5 时，循环会在递增 num 之前退出，但会执行下一次迭代，此时 i 是 6。然后，循环会一直执行到自然结束，即 i 等于 10。最终 num 的值是 8 而不是 9，是因为 continue 语句导致它少递增了一次。break 和 continue 都可以与标签语句一起使用，返回代码中特定的位置。这通常是在嵌套循环中，如下面的例子所示：

```javascript
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break outermost;
        }
        num++;
    }
}
console.log(num); // 55
```

在这个例子中，`outermost` 标签标识的是第一个 for 语句。正常情况下，每个循环执行 10 次，意味着 `num++` 语句会执行 100 次，而循环结束时 `console.log` 的结果应该是 100。但是，break 语句带来了一个变数，即要退出到的标签。添加标签不仅让 `break` 退出（使用变量 j 的）内部循环，也会退出（使用变量 i 的）外部循环。当执行到 i 和 j 都等于 5 时，循环停止执行，此时 num 的值是 55。continue 语句也可以使用标签，如下面的例子所示：

```javascript
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            continue outermost;
        }
        num++;
    }
}
console.log(num); // 95 
```

这一次，continue 语句会强制循环继续执行，但不是继续执行内部循环，而是继续执行外部循环。当 i 和 j 都等于 5 时，会执行 continue，跳到外部循环继续执行，从而导致内部循环少执行 5 次，结果 num 等于 95。

组合使用标签语句和 break、continue 能实现复杂的逻辑，但也容易出错。注意标签要使用描述性强的文本，而嵌套也不要太深。

#### with 语句

with 语句的用途是将代码作用域设置为特定的对象，其语法是：

```javascript
with (expression) statement; 
```

使用 with 语句的主要场景是针对一个对象反复操作，这时候将代码作用域设置为该对象能提供便利，如下面的例子所示：

```javascript
let qs = location.search.substring(1); 
let hostName = location.hostname; 
let url = location.href;
```

上面代码中的每一行都用到了 `location` 对象。如果使用 with 语句，就可以少写一些代码：

```javascript
with(location) {
    let qs = search.substring(1); 
    let hostName = hostname; 
    let url = href; 
} 
```

这里，with 语句用于连接 `location` 对象。这意味着在这个语句内部，每个变量首先会被认为是一个局部变量。如果没有找到该局部变量，则会搜索 `location` 对象，看它是否有一个同名的属性。如果有，则该变量会被求值为 `location` 对象的属性。

严格模式不允许使用 with 语句，否则会抛出错误。

> 由于 with 语句影响性能且难于调试其中的代码，通常不推荐在产品代码中使用 with 语句。

#### switch 语句

switch 语句是与 if 语句紧密相关的一种流控制语句，从其他语言借鉴而来。ECMAScript中 switch 语句跟 C 语言中 switch 语句的语法非常相似，如下所示：

```javascript
switch (expression) {
    case value1:
        statement
        break;
    case value2:
        statement
        break;
    case value3:
        statement
        break;
    case value4:
        statement
        break;
    default:
        statement
}
```

这里的每个 case（条件/分支）相当于：“如果表达式等于后面的值，则执行下面的语句。”break 关键字会导致代码执行跳出 switch 语句。如果没有 break，则代码会继续匹配下一个条件。default 关键字用于在任何条件都没有满足时指定默认执行的语句（相当于 else 语句）。

有了 switch 语句，开发者就用不着写类似这样的代码了：

```javascript
if (i == 25) {
    console.log("25");
} else if (i == 35) {
    console.log("35");
} else if (i == 45) {
    console.log("45");
} else {
    console.log("Other");
}
```

而是可以这样写：

```javascript
switch (i) {
    case 25:
        console.log("25");
        break;
    case 35:
        console.log("35");
        break;
    case 45:
        console.log("45");
        break;
    default:
        console.log("Other");
}
```

为避免不必要的条件判断，最好给每个条件后面都加上 break 语句。如果确实需要连续匹配几个条件，那么推荐写个注释表明是故意忽略了 break，如下所示：

```javascript
switch (i) {
    case 25:
    /*跳过*/
    case 35:
        console.log("25 or 35");
        break;
    case 45:
        console.log("45");
        break;
    default:
        console.log("Other");
}
```

虽然 switch 语句是从其他语言借鉴过来的，但 ECMAScript 为它赋予了一些独有的特性。首先，switch 语句可以用于所有数据类型（在很多语言中，它只能用于数值），因此可以使用字符串甚至对象。其次，条件的值不需要是常量，也可以是变量或表达式。看下面的例子：

```javascript
switch ("hello world") {
    case "hello" + " world":
        console.log("Greeting was found.");
        break;
    case "goodbye":
        console.log("Closing was found.");
        break;
    default:
        console.log("Unexpected message was found.");
}
```

这个例子在 switch 语句中使用了字符串。第一个条件实际上使用的是表达式，求值为两个字符串拼接后的结果。因为拼接后的结果等于 switch 的参数，所以 `console.log` 会输出 "Greeting was found."。能够在条件判断中使用表达式，就可以在判断中加入更多逻辑：

```javascript
let num = 25;
switch (true) {
    case num < 0:
        console.log("Less than 0.");
        break;
    case num >= 0 && num <= 10:
        console.log("Between 0 and 10.");
        break;
    case num > 10 && num <= 20:
        console.log("Between 10 and 20.");
        break;
    default:
        console.log("More than 20.");
}
```

上面的代码首先在外部定义了变量 num，而传给 switch 语句的参数之所以是 true，就是因为每个条件的表达式都会返回布尔值。条件的表达式分别被求值，直到有表达式返回 true；否则，就会一直跳到 default 语句（这个例子正是如此）。

> switch 语句在比较每个条件的值时会使用全等操作符，因此不会强制转换数据类 型（比如，字符串"10"不等于数值 10）。

### 函数

函数对任何语言来说都是核心组件，因为它们可以封装语句，然后在任何地方、任何时间执行。ECMAScript 中的函数使用 function 关键字声明，后跟一组参数，然后是函数体。

以下是函数的基本语法：

```javascript
function functionName(arg0, arg1,...,argN) { 
    statements 
}
```

下面是一个例子：

```javascript
function sayHi(name, message) { 
    console.log("Hello " + name + ", " + message); 
} 
```

可以通过函数名来调用函数，要传给函数的参数放在括号里（如果有多个参数，则用逗号隔开）。下面是调用函数  `sayHi()` 的示例：

```javascript
sayHi("Nicholas", "how are you today?");
```

调用这个函数的输出结果是 "Hello Nicholas, how are you today?"。参数 `name` 和 `message` 在函数内部作为字符串被拼接在了一起，最终通过 `console.log` 输出到控制台。

ECMAScript 中的函数不需要指定是否返回值。任何函数在任何时间都可以使用 `return` 语句来返回函数的值，用法是后跟要返回的值。比如：

```javascript
function sum(num1, num2) { 
    return num1 + num2; 
}
```

函数 `sum()` 会将两个值相加并返回结果。注意，除了 return 语句之外没有任何特殊声明表明该函数有返回值。然后就可以这样调用它：

```javascript
const result = sum(5, 10); 
```

要注意的是，只要碰到 return 语句，函数就会立即停止执行并退出。因此，return 语句后面的代码不会被执行。比如：

```javascript
function sum(num1, num2) { 
    return num1 + num2; 
    console.log("Hello world"); // 不会执行
}
```

在这个例子中，console.log 不会执行，因为它在 return 语句后面。一个函数里也可以有多个 return 语句，像这样：

```javascript
function diff(num1, num2) { 
    if (num1 < num2) { 
        return num2 - num1; 
    } else {
        return num1 - num2; 
    } 
}
```

这个 `diff()` 函数用于计算两个数值的差。如果第一个数值小于第二个，则用第二个减第一个；否则，就用第一个减第二个。代码中每个分支都有自己的 return 语句，返回正确的差值。

return 语句也可以不带返回值。这时候，函数会立即停止执行并返回 undefined。这种用法最常用于提前终止函数执行，并不是为了返回值。比如在下面的例子中，console.log 不会执行：

```javascript
function sayHi(name, message) { 
    return; 
    console.log("Hello " + name + ", " + message); // 不会执行
}
```

> 最佳实践是函数要么返回值，要么不返回值。只在某个条件下返回值的函数会带来麻烦，尤其是调试时

严格模式对函数也有一些限制：

+ 函数不能以 eval 或 arguments 作为名称；
+ 函数的参数不能叫 eval 或 arguments；
+ 两个命名参数不能拥有同一个名称。

如果违反上述规则，则会导致语法错误，代码也不会执行。

















































## 表达式和运算符

### 展开语法

[展开语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax)（Spread syntax)，可以在函数调用/数组构造时，将数组表达式或者 string 在语法层面展开；还可以在构造字面量对象时，将对象表达式按key-value 的方式展开。例如：

```javascript
function sum(x, y, z) {
  return x + y + z;
}

const numbers = [1, 2, 3];

console.log(sum(...numbers));
// expected output: 6

console.log(sum.apply(null, numbers));
// expected output: 6
```

####  在函数调用时使用展开语法

TODO



## 数据类型

ECMAScript 中有 5 种简单数据类型（也称为基本数据类型）：`Undefined`、`Null`、`Boolean`、`Number`和 `String`。还有 1 种复杂数据类型——Object，Object 本质上是由一组无序的名值对组成的。

JavaScript不区分整数和浮点数，统一用Number表示,以下都是合法的Number类型：

```javascript
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

> 十六进制表示整数比较方便，十六进制用0x前缀和0-9，a-f表示，例如：`0xff00`，`0xa5b4c3d2`，等等，它们和十进制表示的数值完全一样。

Number可以直接做四则运算，规则和数学一致

```javascript
1 + 2; // 3
(1 + 2) * 5 / 2; // 7.5
2 / 0; // Infinity
0 / 0; // NaN
10 % 3; // 1
10.5 % 3; // 1.5
// 注意%是求余运算
```

### `typeof`操作符

`typeof`操作符用来检测给定变量的数据类型，返回以下字符串

+ "undefined"——这个值未定义；
+ "boolean"——这个值是布尔值；
+ "string"——这个值是字符串；
+ "number"——这个值是数值；
+ "object"——这个值是对象或 null；
+ "function"——如果这个值是函数

```javascript
var message = "hello",found = false;
console.log(typeof message) // string
console.log(typeof(found))  // boolean
console.log(typeof null)  // object
```

### `null`和`undefined`

`null`表示一个“空”的值，它和`0`以及空字符串`''`不同，`0`是一个数值，`''`表示长度为0的字符串，而`null`表示“空”。

`null`值表示一个空对象指针，而这也正是使用`typeof`操作符检测`null`值时会返回"object"的原因

在其他语言中，也有类似JavaScript的`null`的表示，例如Java也用`null`，Swift用`nil`，Python用`None`表示。但是，在JavaScript中，还有一个和`null`类似的`undefined`，它表示“未定义”。

JavaScript的设计者希望用`null`表示一个空的值，而`undefined`表示值未定义。事实证明，这并没有什么用，区分两者的意义不大。大多数情况下，我们都应该用`null`。`undefined`仅仅在判断函数参数是否传递的情况下有用。

只要意在保存对象的变量还没有真正保存对象，就应该明确地让该变量保存`null`值。这样做不仅可以体现`null`作为空对象指针的惯例，而且也有助于进一步区分`null`和 `undefined`。****

###  `String`类型

字符串是以单引号`'`或双引号`"`括起来的任意文本，比如`'abc'`，`"xyz"`等等。`''`或`""`本身只是一种表示方式，不是字符串的一部分，因此，字符串`'abc'`只有`a`，`b`，`c`这3个字符。

如果字符串内部既包含`'`又包含`"`，可以用转义字符`\`来标识

字符字面量

| 字面量   | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| `\n`     | 换行                                                         |
| `\t`     | Tab制表                                                      |
| `\b`     | 空格                                                         |
| `\r`     | 回车                                                         |
| `\f`     | 换页                                                         |
| `\\`     | 转义斜杠                                                     |
| `\xnn`   | 以十六进制代码nn表示的一个字符（其中n为0～F）。例如，\x41表示"A" |
| `\unnnn` | 以十六进制代码nnnn表示的一个Unicode字符（其中n为0～F）。例如，\u03a3表示希腊字符Σ |

#### 多行字符串

由于多行字符串用`\n`写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用反引号表示：

```javascript
`这是一个
多行
字符串`;
```

#### 模板字符串

要把多个字符串连接起来，可以用`+`号连接：

```javascript
var name = '小明';
var age = 20;
var message = '你好, ' + name + ', 你今年' + age + '岁了!';
alert(message);
```

如果有很多变量需要连接，用`+`号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量：

```javascript
var name = '小明';
var age = 20;
var message = `你好, ${name}, 你今年${age}岁了!`;
alert(message);
```

#### 操作字符串

字符串常见的操作如下：

```javascript
var s = 'Hello, world!';
s.length; // 13
```

要获取字符串某个指定位置的字符，使用类似Array的下标操作，索引号从0开始：

```javascript
var s = 'Hello, world!';

s[0]; // 'H'
s[6]; // ' '
s[7]; // 'w'
s[12]; // '!'
s[13]; // undefined 超出范围的索引不会报错，但一律返回undefined
```

**需要特别注意的是**，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果：

```javascript
var s = 'Test';
s[0] = 'X';
alert(s); // s仍然为'Test'
```

JavaScript为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串：

+ toUpperCase
+ toLowerCase
+ indexOf
+ substring

`toUpperCase()`把一个字符串全部变为大写：

```javascript
var s = 'Hello';
s.toUpperCase(); // 返回'HELLO'
```

`toLowerCase()`把一个字符串全部变为小写：

```javascript
var s = 'Hello';
var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
lower; // 'hello'
```

`indexOf()`会搜索指定字符串出现的位置：

```javascript
var s = 'hello, world';
s.indexOf('world'); // 返回7
s.indexOf('World'); // 没有找到指定的子串，返回-1
```

`substring()`返回指定索引区间的子串：

```javascript
var s = 'hello, world'
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
s.substring(7); // 从索引7开始到结束，返回'world'
```

### `Boolean`类型

布尔值和布尔代数的表示完全一致，一个布尔值只有`true`、`false`两种值，要么是`true`，要么是`false`，可以直接用`true`、`false`表示布尔值，也可以通过布尔运算计算出来

+ `&&`运算是与运算，只有所有都为`true`，`&&`运算结果才是`true`
+ `||`运算是或运算，只要其中有一个为`true`，`||`运算结果就是`true`
+ `!`运算是非运算，它是一个单目运算符，把`true`变成`false`，`false`变成`true`

其他数据类型对于布尔值转换

| 数据类型  | true                   | false     |
| --------- | ---------------------- | --------- |
| Boolean   | true                   | false     |
| String    | 非空字符串             | ” “       |
| Number    | 非零数字（包括无穷大） | 0和NaN    |
| Object    | 任何对象               | null      |
| Undefined | N/A                    | undefined |

### `Number`类型

默认十进制

八进制前两位为0o

十六进制前两位为0x

NaN（Not a Number），0除以 0会返回NaN，因此不会影响其他代码的执行

NaN 与任何值都不相等，包括NaN本身，ECMAScript 定义了`isNaN()`函数

```javascript
console.log(100);  // 100
console.log(0o100); // 64
console.log(0xAB);  // 171
console.log(0/0);   // NaN
console.log(1/0);   // Infinity
console.log(-1/0);  // -Infinity
```

### 操作符

一元操作符，只能操作一个值的操作符

自增和自减

```javascript
var age = 29;
console.log(++ age); // 30; 与age = age + 1 效果相同
console.log(age ++); // 30
console.log(age);    // 31
```





### 关系操作符

当我们对Number做比较时，可以通过比较运算符得到一个布尔值：

```javascript
2 > 5; // false
5 >= 2; // true
7 == 7; // true
```

实际上，JavaScript允许对任意数据类型做比较：

```javascript
false == 0; // true
false === 0; // false
```

要特别注意相等运算符`==`。JavaScript在设计时，有两种比较运算符：

第一种是`==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；

第二种是`===`比较，它不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比较。

由于JavaScript这个设计缺陷，**不要**使用`==`比较，始终坚持使用`===`比较。

另一个例外是`NaN`这个特殊的Number与所有其他值都不相等，包括它自己：

```javascript
NaN === NaN; // false
```

唯一能判断`NaN`的方法是通过`isNaN()`函数：

```javascript
isNaN(NaN); // true
```

最后要注意浮点数的相等比较：

```javascript
1 / 3 === (1 - 2 / 3); // false
```

这不是JavaScript的设计缺陷。浮点数在运算过程中会产生误差，因为计算机无法精确表示无限循环小数。要比较两个浮点数是否相等，只能计算它们之差的绝对值，看是否小于某个阈值：

```javascript
Math.abs(1 / 3 - (1 - 2 / 3)) < 0.0000001; // true
```

### 数组

数组是一组按顺序排列的集合，集合的每个值称为元素。JavaScript的数组可以包括任意数据类型。例如：

```javascript
[1, 2, 3.14, 'Hello', null, true];
```

上述数组包含6个元素。数组用`[]`表示，元素之间用`,`分隔。

另一种创建数组的方法是通过`Array()`函数实现：

```javascript
new Array(1, 2, 3); // 创建了数组[1, 2, 3]
```

然而，出于代码的可读性考虑，强烈建议直接使用`[]`。

数组的元素可以通过索引来访问。请注意，索引的起始值为`0`：

```javascript
var arr = [1, 2, 3.14, 'Hello', null, true];
arr[0]; // 返回索引为0的元素，即1
arr[5]; // 返回索引为5的元素，即true
arr[6]; // 索引超出了范围，返回undefined
```

要取得`Array`的长度，直接访问`length`属性：

```javascript
var arr = [1, 2, 3.14, 'Hello', null, true];
arr.length; // 6
```

**请注意**，直接给`Array`的`length`赋一个新的值会导致`Array`大小的变化：

```javascript
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```

`Array`可以通过索引把对应的元素修改为新的值，因此，对`Array`的索引进行赋值会直接修改这个`Array`：

```javascript
var arr = ['A', 'B', 'C'];
arr[1] = 99;
arr; // arr现在变为['A', 99, 'C']
```

**请注意**，如果通过索引赋值时，索引超过了范围，同样会引起`Array`大小的变化：

```javascript
var arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```

大多数其他编程语言不允许直接改变数组的大小，越界访问索引会报错。然而，JavaScript的`Array`却不会有任何错误。在编写代码时，不建议直接修改`Array`的大小，访问索引时要确保索引不会越界。

#### indexOf

与String类似，`Array`也可以通过`indexOf()`来搜索一个指定的元素的位置：

```javascript
var arr = [10, 20, '30', 'xyz'];
arr.indexOf(10); // 元素10的索引为0
arr.indexOf(20); // 元素20的索引为1
arr.indexOf(30); // 元素30没有找到，返回-1
arr.indexOf('30'); // 元素'30'的索引为2
```

注意了，数字`30`和字符串`'30'`是不同的元素。

#### slice

`slice()` 就是对应 String 的 `substring()` 版本，它截取`Array`的部分元素，然后返回一个新的`Array`：

```javascript
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
arr.slice(3); // 从索引3开始到结束: ['D', 'E', 'F', 'G']
```

注意到`slice()`的起止参数包括开始索引，不包括结束索引。

如果不给`slice()`传递任何参数，它就会从头到尾截取所有元素。利用这一点，我们可以很容易地复制一个`Array`：

```javascript
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
var aCopy = arr.slice();
aCopy; // ['A', 'B', 'C', 'D', 'E', 'F', 'G']
aCopy === arr; // false
```

#### push 和 pop

`push()` 向`Array`的末尾添加若干元素，`pop()`则把`Array`的最后一个元素删除掉：

```javascript
var arr = [1, 2];
arr.push('A', 'B'); // 返回Array新的长度: 4
arr; // [1, 2, 'A', 'B']
arr.pop(); // pop()返回'B'
arr; // [1, 2, 'A']
arr.pop(); arr.pop(); arr.pop(); // 连续pop 3次
arr; // []
arr.pop(); // 空数组继续pop不会报错，而是返回undefined
arr; // []
```

#### unshift和shift

如果要往`Array`的头部添加若干元素，使用`unshift()`方法，`shift()`方法则把`Array`的第一个元素删掉：

```javascript
var arr = [1, 2];
arr.unshift('A', 'B'); // 返回Array新的长度: 4
arr; // ['A', 'B', 1, 2]
arr.shift(); // 'A'
arr; // ['B', 1, 2]
arr.shift(); arr.shift(); arr.shift(); // 连续shift 3次
arr; // []
arr.shift(); // 空数组继续shift不会报错，而是返回undefined
arr; // []
```

#### sort

`sort()`可以对当前`Array`进行排序，它会直接修改当前`Array`的元素位置，直接调用时，按照默认顺序排序：

```javascript
var arr = ['B', 'C', 'A'];
arr.sort();
arr; // ['A', 'B', 'C']
```

能否按照我们自己指定的顺序排序呢？完全可以，我们将在后面的函数中讲到。

#### reverse

`reverse()`把整个`Array`的元素给掉个个，也就是反转：

```javascript
var arr = ['one', 'two', 'three'];
arr.reverse(); 
arr; // ['three', 'two', 'one']
```

#### splice

`splice()`方法是修改`Array`的“万能方法”，它可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素：

```javascript
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];
// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook'); // 返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```

#### concat

`concat()`方法把当前的`Array`和另一个`Array`连接起来，并返回一个新的`Array`：

```javascript
var arr = ['A', 'B', 'C'];
var added = arr.concat([1, 2, 3]);
added; // ['A', 'B', 'C', 1, 2, 3]
arr; // ['A', 'B', 'C']
```

**请注意**，`concat()`方法并没有修改当前`Array`，而是返回了一个新的`Array`。

实际上，`concat()`方法可以接收任意个元素和`Array`，并且自动把`Array`拆开，然后全部添加到新的`Array`里：

```javascript
var arr = ['A', 'B', 'C'];
arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C', 1, 2, 3, 4]
```

#### join

`join()`方法是一个非常实用的方法，它把当前`Array`的每个元素都用指定的字符串连接起来，然后返回连接后的字符串：

```javascript
var arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```

如果`Array`的元素不是字符串，将自动转换为字符串后再连接。

#### 多维数组

如果数组的某个元素又是一个`Array`，则可以形成多维数组，例如：

```javascript
var arr = [[1, 2, 3], [400, 500, 600], '-'];
```

### 对象

JavaScript的对象是一组由键-值组成的无序集合，例如：

```javascript
var person = {
    name: 'Bob',
    age: 20,
    tags: ['js', 'web', 'mobile'],
    city: 'Beijing',
    hasCar: true,
    zipcode: null
};
```

JavaScript对象的键都是字符串类型，值可以是任意数据类型。上述`person`对象一共定义了6个键值对，其中每个键又称为对象的属性，例如，`person`的`name`属性为`'Bob'`，`zipcode`属性为`null`。

要获取一个对象的属性，我们用`对象变量.属性名`的方式：

```javascript
person.name; // 'Bob'
person.zipcode; // null
```

JavaScript用一个`{...}`表示一个对象，键值对以`xxx: xxx`形式申明，用`,`隔开。注意，最后一个键值对不需要在末尾加`,`，如果加了，有的浏览器（如低版本的IE）将报错。

访问属性是通过`.`操作符完成的，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须用`''`括起来：

```javascript
var xiaohong = {
    name: '小红',
    'middle-school': 'No.1 Middle School'
};
```

`xiaohong`的属性名`middle-school`不是一个有效的变量，就需要用`''`括起来。访问这个属性也无法使用`.`操作符，必须用`['xxx']`来访问：

```javascript
xiaohong['middle-school']; // 'No.1 Middle School'
xiaohong['name']; // '小红'
xiaohong.name; // '小红'
```

如果访问一个不存在的属性会返回什么呢？JavaScript规定，访问不存在的属性不报错，而是返回`undefined`

如果我们要检测`xiaoming`是否拥有某一属性，可以用`in`操作符：

```javascript
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
'name' in xiaoming; // true
'grade' in xiaoming; // false
```

不过要小心，如果`in`判断一个属性存在，这个属性不一定是`xiaoming`的，它可能是`xiaoming`继承得到的：

```javascript
'toString' in xiaoming; // true
```

因为`toString`定义在`object`对象中，而所有对象最终都会在原型链上指向`object`，所以`xiaoming`也拥有`toString`属性。

要判断一个属性是否是`xiaoming`自身拥有的，而不是继承得到的，可以用`hasOwnProperty()`方法：

```javascript
var xiaoming = {
    name: '小明'
};
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```



### 变量

变量的概念基本上和初中代数的方程变量是一致的，只是在计算机程序中，变量不仅可以是数字，还可以是任意数据类型。

变量在JavaScript中就是用一个变量名表示，变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。申明一个变量用`var`语句，比如：

```javascript
var a; // 申明了变量a，此时a的值为undefined
var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1
var s_007 = '007'; // s_007是一个字符串
var Answer = true; // Answer是一个布尔值true
var t = null; // t的值是null
```

变量名也可以用中文，但是，请不要给自己找麻烦。

在JavaScript中，使用等号`=`对变量进行赋值。可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，但是要注意只能用`var`申明一次，例如：

```javascript
var a = 123; // a的值是整数123
a = 'ABC'; // a变为字符串
```

这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言，赋值语句如下：

```javascript
int a = 123; // a是整数类型变量，类型用int申明
a = "ABC"; // 错误：不能把字符串赋给整型变量
```

和静态语言相比，动态语言更灵活，就是这个原因。

请不要把赋值语句的等号等同于数学的等号。比如下面的代码：

```javascript
var x = 10;
x = x + 2;
```

如果从数学上理解 `x = x + 2` 那无论如何是不成立的，在程序中，赋值语句先计算右侧的表达式`x + 2`，得到结果`12`，再赋给变量`x`。由于`x`之前的值是`10`，重新赋值后，`x`的值变成`12`。

### strict模式

JavaScript在设计之初，为了方便初学者学习，并不强制要求用`var`申明变量。这个设计错误带来了严重的后果：如果一个变量没有通过`var`申明就被使用，那么该变量就自动被申明为全局变量：

```javascript
i = 10; // i现在是全局变量
```

在同一个页面的不同的JavaScript文件中，如果都不用`var`申明，恰好都使用了变量`i`，将造成变量`i`互相影响，产生难以调试的错误结果。

使用`var`申明的变量则不是全局变量，它的范围被限制在该变量被申明的函数体内（函数的概念将稍后讲解），同名变量在不同的函数体内互不冲突。

为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，强制通过`var`申明变量，未使用`var`申明变量就使用的，将导致运行错误。

启用strict模式的方法是在JavaScript代码的第一行写上：

```java
'use strict';
```

这是一个字符串，不支持strict模式的浏览器会把它当做一个字符串语句执行，支持strict模式的浏览器将开启strict模式运行JavaScript。

## 语句

### 条件判断

JavaScript使用`if () { ... } else { ... }`来进行条件判断。例如，根据年龄显示不同内容，可以用`if`语句实现如下：

```javascript
var age = 20;
if (age >= 18) { // 如果age >= 18为true，则执行if语句块
    alert('adult');
} else { // 否则执行else语句块
    alert('teenager');
}
```

其中`else`语句是可选的。如果语句块只包含一条语句，那么可以省略`{}`：

```javascript
var age = 20;
if (age >= 18)
    alert('adult');
else
    alert('teenager');
```

#### 多行条件判断

如果还要更细致地判断条件，可以使用多个`if...else...`的组合：

```javascript
var age = 3;
if (age >= 18) {
    alert('adult');
} else if (age >= 6) {
    alert('teenager');
} else {
    alert('kid');
}
```

上述多个`if...else...`的组合实际上相当于两层`if...else...`：

```javascript
var age = 3;
if (age >= 18) {
    alert('adult');
} else {
    if (age >= 6) {
        alert('teenager');
    } else {
        alert('kid');
    }
}
```

### 循环

JavaScript的循环有两种，一种是`for`循环，通过初始条件、结束条件和递增条件来循环执行语句块：

```javascript
var x = 0;
var i;
for (i=1; i<=10000; i++) {
    x = x + i;
}
x; // 50005000
```

#### for

让我们来分析一下`for`循环的控制条件：

- i=1 这是初始条件，将变量i置为1；
- i<=10000 这是判断条件，满足时就继续循环，不满足就退出循环；
- i++ 这是每次循环后的递增条件，由于每次循环后变量i都会加1，因此它终将在若干次循环后不满足判断条件`i<=10000`而退出循环。

`for`循环最常用的地方是利用索引来遍历数组：

```javascript
var arr = ['Apple', 'Google', 'Microsoft'];
var i, x;
for (i=0; i<arr.length; i++) {
    x = arr[i];
    console.log(x);
}
```

`for`循环的3个条件都是可以省略的，如果没有退出循环的判断条件，就必须使用`break`语句退出循环，否则就是死循环：

```javascript
var x = 0;
for (;;) { // 将无限循环下去
    if (x > 100) {
        break; // 通过if判断来退出循环
    }
    x ++;
}
```

#### for ... in

`for`循环的一个变体是`for ... in`循环，它可以把一个对象的所有属性依次循环出来：

```javascript
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    console.log(key); // 'name', 'age', 'city'
}
```

要过滤掉对象继承的属性，用`hasOwnProperty()`来实现：

```javascript
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    if (o.hasOwnProperty(key)) {
        console.log(key); // 'name', 'age', 'city'
    }
}
```

由于`Array`也是对象，而它的每个元素的索引被视为对象的属性，因此，`for ... in`循环可以直接循环出`Array`的索引：

```javascript
var a = ['A', 'B', 'C'];
for (var i in a) {
    console.log(i); // '0', '1', '2'
    console.log(a[i]); // 'A', 'B', 'C'
}
```

**请注意**，`for ... in`对`Array`的循环得到的是`String`而不是`Number`。

#### while

`for`循环在已知循环的初始和结束条件时非常有用。而上述忽略了条件的`for`循环容易让人看不清循环的逻辑，此时用`while`循环更佳。

`while`循环只有一个判断条件，条件满足，就不断循环，条件不满足时则退出循环。比如我们要计算100以内所有奇数之和，可以用while循环实现：

```javascript
var x = 0;
var n = 99;
while (n > 0) {
    x = x + n;
    n = n - 2;
}
x; // 2500
```

在循环内部变量`n`不断自减，直到变为`-1`时，不再满足`while`条件，循环退出。

#### do ... while

最后一种循环是`do { ... } while()`循环，它和`while`循环的唯一区别在于，不是在每次循环开始的时候判断条件，而是在每次循环完成的时候判断条件：

```javascript
var n = 0;
do {
    n = n + 1;
} while (n < 100);
n; // 100
```

用 `do { ... } while()` 循环要小心，循环体会至少执行1次，而`for`和`while`循环则可能一次都不执行。

#### break 和 continue

break 语句会立即退出循环， 强制继续执行循环后面的语句

continue 语句虽然也是立即退出循环，但退出循环后会从循环的顶部继续执行

### 其他

#### with 语句

with 语句的作用是将代码的作用域设置到一个特定的对象中

由于大量使用 with 语句会导致性能下降，同时也会给调试代码造成困难，因此 在开发大型应用程序时，不建议使用 with 语句

#### switch 语句

```javascript
switch (expression) {
    case value: statement
        break;
    case value: statement
        break;
    case value: statement
        break;
}
```

## 函数

```javascript
function functionName(arg0, arg1,...,argN) {
    statements
} 
```

使用`return`返回`return`后语句的返回值

参数，ECMAScript 函数不介意传递进来多少个参数，也不在乎传进来参数是什么数据类型

ECMAScript 函数不能像传统意义上那样实现重载

如果在 ECMAScript 中定义了两个名字相同的函数，则该名字只属于后定义的函数

```javascript
function addSomeNumber(num){
    return num + 100;
}
function addSomeNumber(num) {
    return num + 200;
}
var result = addSomeNumber(100); 
console.log(result); //300
```

通过检查传入函数中参数的类型和数量并作出不同的反应，可以模仿方法的重载



## 面向对象编程





## 代理与反射



## Promise 与异步函数







