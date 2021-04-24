---
title: 博客增加锚点功能
tags:
  - Hexo
  - Html
categories:
  - [Hexo]
date: 2020-12-28 13:51:58
summary: Hexo自带的Markdown渲染器没有脚注等语法的支持，提供一下方法增加该功能的支持
---

Here is a footnote reference,[^1] and another.[^longnote]

[^1]: Here is the footnote.

[^longnote]: Here's one with multiple blocks.

Hexo自带的Markdown渲染器没有脚注等语法的支持，提供以下方法增加该功能的支持[^这就是脚注]

[^这就是脚注]: 这是脚注内容

## 锚点功能的解决途径

一种是手动添加`a`标签，但这种方法比较麻烦。

```html
<a href="#bib1" id="bib1ref"><sup>[1]</sup></a>
<a id="bib1" href="#bib1ref"><sup>[1]</sup></a>
```

还有一种是采用插件，这里选择了[hexo-renderer-markdown-it](https://github.com/hexojs/hexo-renderer-markdown-it)插件

插件的安装

首先在Hexo博客目录下，选择删除原来的渲染器

```bash
npm un hexo-renderer-marked --save
```

在安装该插件

```bash
npm i hexo-renderer-markdown-it --save
```

之后在 Hexo 的 _config.yml 文件中进行相关的配置。

2021年4月24日更换了博客主题对配置进行了修改（来源：[链接](https://zhanghuimeng.github.io/post/add-footnote-plugin-for-hexo-blog/#fnref3)）

```yml
# Markdown-it config
## Docs: https://github.com/celsomiranda/hexo-renderer-markdown-it/wiki
markdown:
  # 渲染设置
  render:
    # 置为true时，html内容保持不变；置为false时，html内容将被转义成普通字符串
    html: true
    # 是否生成与XHTML完全兼容的标签（虽然我不懂是什么意思）
    xhtmlOut: false
    # 置为true时，每个换行符都被渲染成一个<br>（即Hexo的默认表现）；置为false时，只有空行才会被渲染为<br>（GFM的默认表现）
    breaks: true
    # 是否自动识别链接并把它渲染成链接
    linkify: true
    # 是否自动识别印刷格式（意思是把(c)渲染为©这样的）
    typographer: true
    # 如果typographer被设置为true，则该选项用于设置将dumb quotes（""）自动替换为smart quotes
    quotes: '“”‘’'
  # 设置所需插件
  plugins:
    - markdown-it-abbr
    - markdown-it-footnote
    - markdown-it-ins
    - markdown-it-sub
    - markdown-it-sup
  # 锚点设置（因为我没有尝试相关内容，所以就不翻译相关说明了）
  anchors:
    level: 2
    collisionSuffix: 'v'
    permalink: true
    permalinkClass: header-anchor
    permalinkSymbol: ""
```



```yml
markdown:
  preset: 'default'
  render:
    html: true
    xhtmlOut: false
    langPrefix: 'language-'
    breaks: true
    linkify: true
    typographer: true
    quotes: '“”‘’'
  enable_rules:
  disable_rules:
  plugins:
  anchors:
    level: 2
    collisionSuffix: ''
    permalink: false
    permalinkClass: 'header-anchor'
    permalinkSide: 'left'
    permalinkSymbol: '¶'
    case: 0
    separator: '-'
```


> 在安装插件的过程中遇到没有生效的问题，首先使用`hexo clean`命令清理，在重新尝试
> 如果仍不生效可以查看 packge.json 和 node_modules 有没有导入包，Markdown脚注的格式是否正确，

## 对于锚点的适配问题

在我使用的[matery](https://github.com/blinkfox/hexo-theme-matery/)主题中，文章内`a`标签中默认加入了`target:_brank`属性，这使得点击链接会跳转到新的窗口中现实，不符合锚点需要的页面内的跳转功能

解决办法，在主题的 source\js\matery.js 文件中找到如下代码，并注释`$('#articleContent a').attr('target', '_blank');`

```javascript
  /*文章内容详情的一些初始化特性*/
  let articleInit = function () {
      //$('#articleContent a').attr('target', '_blank');
      }
```

完成以上内容，即可使用锚点，但锚点总会跳转到页面最顶端，由于主题banner是浮动效果，会导致遮住跳转的链接。

下面提供一种Js代码的解决方式，[参考文章](https://www.cnblogs.com/attlia/p/7488271.html)

```javascript
    /*文章内容详情的一些初始化特性*/
    let articleInit = function () {
        // $('#articleContent a').attr('target', '_blank');
        $('#articleContent a').click(function () {
            var target = $(this).attr('href');
            console.log(target);
            $('html, body').animate({
               scrollTop: $(target).offset().top - 65 //65位设置的偏移值
             }, 500);
             return false;
        });
    }
```

自此完成了对脚注的支持，但因为这个renderer-markdown-it渲染器的原因，可以会存在一些问题，之后遇到了在说。
