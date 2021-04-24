---
title: BootStrap
tags:
  - BootStrap
categories:
  - Web
date: 2020-03-21 20:36:22
---


Bootstrap 是由Twitter的Mark Otto和Jacob Thornton开发的一个基于 HTML、CSS、JavaScript的用于快速开发 Web 应用程序和网站的前端框架。

---

## 响应式布局

同一套页面可以兼容不同分辨率的设备。

实现：依赖于栅格系统，将一行平均分成12个格子，可以指定元素占几个格子

步骤：

1. 定义容器。相当于之前的tableI

2. 定义行。相当于之前的tr

3. 定义元素。指定该元素在不同的设备上，所占的格子数目。`col-设备代号-格子数目`

## 栅格系统

栅格系统用于通过一系列的行（row）与列（column）的组合来创建页面布局，你的内容就可以放入这些创建好的布局中。下面就介绍一下 Bootstrap 栅格系统的工作原理：

- “行（row）”必须包含在 `.container` （固定宽度）或 `.container-fluid` （100% 宽度）中，以便为其赋予合适的排列（aligment）和内补（padding）。
- 通过“行（row）”在水平方向创建一组“列（column）”。
- 你的内容应当放置于“列（column）”内，并且，只有“列（column）”可以作为行（row）”的直接子元素。
- 类似 `.row` 和 `.col-xs-4` 这种预定义的类，可以用来快速创建栅格布局。Bootstrap 源码中定义的 mixin 也可以用来创建语义化的布局。
- 通过为“列（column）”设置 `padding` 属性，从而创建列与列之间的间隔（gutter）。通过为 `.row` 元素设置负值 `margin` 从而抵消掉为 `.container` 元素设置的 `padding`，也就间接为“行（row）”所包含的“列（column）”抵消掉了`padding`。
- 负值的 margin就是下面的示例为什么是向外突出的原因。在栅格列中的内容排成一行。
- 栅格系统中的列是通过指定1到12的值来表示其跨越的范围。例如，三个等宽的列可以使用三个 `.col-xs-4` 来创建。
- 如果一“行（row）”中包含了的“列（column）”大于 12，多余的“列（column）”所在的元素将被作为一个整体另起一行排列。
- 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小屏幕设备覆盖栅格类。 因此，在元素上应用任何 `.col-md-*` 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小屏幕设备覆盖栅格类。 因此，在元素上应用任何 `.col-lg-*` 不存在， 也影响大屏幕设备。

## 使用BootStrap

### Bootstrap 4 CDN

```html
<!-- 新 Bootstrap4 核心 CSS 文件 -->
<link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/4.3.1/css/bootstrap.min.css">
 
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="https://cdn.staticfile.org/jquery/3.2.1/jquery.min.js"></script>
 
<!-- bootstrap.bundle.min.js 用于弹窗、提示、下拉菜单，包含了 popper.min.js -->
<script src="https://cdn.staticfile.org/popper.js/1.15.0/umd/popper.min.js"></script>
 
<!-- 最新的 Bootstrap4 核心 JavaScript 文件 -->
<script src="https://cdn.staticfile.org/twitter-bootstrap/4.3.1/js/bootstrap.min.js"></script>
```

<table class="grid" cellspacing="0">
<tbody><tr>
  <td width="8.33%" >1</td>
  <td width="8.33%">1</td>		
  <td width="8.33%">1</td>
  <td width="8.33%">1</td>
  <td width="8.33%">1</td>		
  <td width="8.33%">1</td>
  <td width="8.33%">1</td>
  <td width="8.33%">1</td>		
  <td width="8.33%">1</td>
  <td width="8.33%">1</td>
  <td width="8.33%">1</td>		
  <td width="8.33%">1</td>
</tr>
<tr>
  <td colspan="4">4</td>
  <td colspan="4">4</td>		
  <td colspan="4">4</td>
</tr>
<tr>
  <td colspan="4">4</td>
  <td colspan="8">8</td>		
</tr>
<tr>
  <td colspan="6">6</td>
  <td colspan="6">6</td>		
</tr>
<tr>
  <td colspan="12">12</td>
</tr>
</tbody></table>

