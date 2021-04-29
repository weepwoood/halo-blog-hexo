---
title: 线性代数-行列式
date: 2021-04-23 11:26:54
updated: 2021-04-23 14:52:41
mathjax: true
cover: https://img.imgdb.cn/item/5fc129d415e771908483c0d5.jpg
---

## 二阶与三阶行列式

二阶行列式，记作：

<div>
$$\begin{vmatrix}
  a_{1} & a_{2}\\
  b_{1} & b_{2}
\end{vmatrix}$$
</div>


三阶行列式，记作：

<div>
$$\begin{vmatrix}a_{1} & a_{2} & a_{3}\\b_{1} & b_{2} & b_{3}\\c_{1} & c_{2} & c_{3}\end{vmatrix}$$
</div>

<div>
$$\begin{vmatrix}
 a_{1} & a_{2} & a_{3}\\
 b_{1} & b_{2} & b_{3}\\
 c_{1} & c_{2} & c_{3}
\end{vmatrix}= a_{1}b_{2}c_{3}+a_{2}b_{3}c_{1}+a_{3}b_{1}c_{2}-a_{3}b_{2}c_{1}-a_{1}b_{3}c_{2}$$
</div>

![三阶行列式的计算](https://halo-blog-img.oss-cn-hangzhou.aliyuncs.com/%E7%BA%BF%E6%80%A7%E4%BB%A3%E6%95%B0/%E4%B8%89%E9%98%B6%E8%A1%8C%E5%88%97%E5%BC%8F%E7%9A%84%E8%AE%A1%E7%AE%97.png)

## 全排列和对换

### 排列及其逆序数

把$n$个不同的元素排成一列，叫做这$n$个元素的全排列（也简称<font color="#ea66a6">排列</font>）
$n$个不同元素的所有排列的种数，通常用$P_\{n\}$表示：

<span>
$$P_{n}=n\cdot(n-1)\cdot\dots\cdot3\cdot2\cdot1=n!$$
</span>

<span>
$$\left( \sum_{k=1}^n a_k b_k \right)^{\!\!2}\leq   
\left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)  $$
</span>
