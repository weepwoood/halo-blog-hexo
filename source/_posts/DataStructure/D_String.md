---
title: 串
date: 2021-05-07 11:54:51
description: 字符串模式匹配
# top_img: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面顶部图片
# cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面缩略图
mathjax: true
#katex: true
large: true
bilibili_banner: spring
categories: 数据结构
tags:
- 数据结构
---

【知识框架】

TODO 完成串知识框架

【复习提示】

本章是统考大纲第 6 章内容，采纳读者建议单独作为一章，统考大纲只要求掌握字符串模式匹配，需重点掌握 KMP 匹配算法的原理及 next 数组的推理过程，手工求 `next` 数组可以先计算出部分匹配值表然后再变形，或根据公式来求解。了解 `nextval` 数组的求解方法。

## 串的定义和实现

字符串简称串，计算机上非数值处理的对象基本都是字符串数据。我们常见的信息检索系统（如搜索引擎）、文本编辑程序（如Word）、问答系统、自然语言翻译系统等，都是以字符串数据作为处理对象的。本章详细介绍字符串的存储结构及相应的操作。

### 串的定义

串（string）是由零个或多个字符组成的有限序列。一般记为$$S=' a_1a_2\dots a_n \ (n\ge 0)'$$其中，$S$ 是串名，单引号括起来的字符序列是串的值；$a_i$可以是字母、数字或其他字符；串中字符的个数 $n$ 称为串的长度。$n=0$ 时的串称为空串（用 $\varnothing$ 表示）。

+ 串中任意个<font color="#FF666">连续的</font>字符组成的子序列称为该串的<font color="#ea66a6">子串</font>（包括空串）
+ 包含子串的串相应地称为<font color="#ea66a6">主串</font>。
+ 某个字符在串中的序号称为该<font color="#ea66a6">字符在串中的位置</font>（序号从1开始）。
+ <font color="#ea66a6">子串在主串中的位置</font>以子串的第一个字符在主串中的位置来表示。
+ 当两个串的长度相等且每个对应位置的字符都相等时，称这两个串是相等的。

> 需要注意的是，由一个或多个空格（空格是特殊字符）组成的串称为空格串（注意，空格串不是空串），其长度为串中空格字符的个数。

串的逻辑结构和线性表极为相似，区别仅在于串的数据对象限定为字符集。在基本操作上，串和线性表有很大差别。线性表的基本操作主要以单个元素作为操作对象，如查找、插入或删除某个元素等；而串的基本操作通常以子串作为操作对象，如查找、插入或删除一个子串等。

### 串的存储结构

#### 定长顺序存储表示

类似于线性表的顺序存储结构，用一组地址连续的存储单元存储串值的字符序列。在串的定长顺序存储结构中，为每个串变量分配一个固定长度的存储区，即定长数组。

```c++
#define MAXLEN 255
typedef struct {
    char ch[MAXLEN];  // 每个分量存储一个字符串
    int length;       // 串的实际长度
} SString;
```

串的实际长度只能小于等于 `MAXLEN`，超过预定义长度的串值会被舍去，称为截断。串长有两种表示方法：

+ 一是如上述定义描述的那样，用一个额外的变量 `length` 来存放串的长度
+ 二是在串值后面加一个不计入串长的结束标记字符“\0”， 此时的串长为隐含值。

在一些串的操作（如插入、联接等）中，若串值序列的长度超过上界 `MAXLEN`，约定用“截断”法处理，要克服这种弊端，只能不限定串长的最大长度，即采用动态分配的方式。

#### 堆分配存储表示

堆分配存储表示仍然以一组地址连续的存储单元存放串值的字符序列，但它们的存储空间是在程序执行过程中动态分配得到的。

```c++
#include <cstdlib>

#define MAXLEN 255
typedef struct {
    char *ch;
    int length;
} HString;

int InitHString() {
    HString S;
    S.ch = (char *)malloc(MAXLEN * sizeof(char));
    S.length = 0;
}
```

在C语言中，存在一个称之为“堆”的自由存储区，并用 `malloc()` 和 `free()` 函数来完成动态存储管理。利用 `malloc()` 为每个新产生的串分配一块实际串长所需的存储空间，若分配成功，则返回一个指向起始地址的指针，作为串的基地址，这个串由 `ch` 指针来指示；若分配失败，则返回 `NULL`。已分配的空间可用 `free()` 释放掉。

上述两种存储表示通常为高级程序设计语言所采用。块链存储表示仅做简单介绍。

#### 块链存储表示

类似于线性表的链式存储结构，也可采用链表方式存储串值。由于串的特殊性（每个元素只有一个字符），在具体实现时，每个结点既可以存放一个字符，也可以存放多个字符。每个结点称为块，整个链表称为块链结构。最后一个结点占不满时通常用 `#` 补上。

```c++
typedef struct {
    char ch[4]; // 每个结点存多个字符
    struct StringNode * next;
} StringNode, * String;
```

### 串的基本操作

+ `StrAssign(&T,chars)`：赋值操作。把串 T 赋值为 chars
+ `StrCopy(&T,S)`：复制操作。由串 S 复制得到串 T
+ `StrEmpty(S)`：判空操作。若 S 为空串，则返回 TRUE，否则返回 FALSE
+ `StrCompare(S, T)`：比较操作。若 S>T，则返回值 >0 ；若S=T，则返回值 =0 ；若 S<T，则返回值 <0
+ `StrLength(S)`：求串长。返回串 S 的元素个数
+ `SubString (&Sub,S,pos,len)`：求子串。用 Sub 返回串 S 的第 pos 个字符起长度为 len 的子串
+ `Concat (&T,S1,S2)`：串联接。用 T 返回由 S1 和 S2 联接而成的新串
+ `Index(S,T)`：定位操作。若主串 S 中存在与串 T 值相同的子串，则返回它在主串 S 中第一次出现的位置；否则函数值为0
+ `ClearString(&S)`：清空操作。将 S 清为空串
+ `DestroyString(&S)`：销毁串。将串S销毁

不同的高级语言对串的基本操作集可以有不同的定义方法。在上述定义的操作中，串赋值 `StrAssign`、串比较 `StrCompare`、求串长 `StrLength`、串联接 `Concat` 及求子串 `SubString` 五种操作构成串类型的最小操作子集，即这些操作不可能利用其他串操作来实现；反之，其他串操作（除串清除 `ClearString` 和串销毁 `DestroyString` 外）均可在该最小操作子集上实现。

例如，可利用判等、求串长和求子串等操作实现定位函数 `Index(S, T)`。算法思想为：在主串 $S$ 中取从第一个字符起、长度和串 $T$ 相等的子串，与串 $T$ 比较，若相等则求得函数值为 $i$，否则 $i$ 值增1，直至串 $S$ 中不存在和串 $T$ 相等的子串为止。

#### 实现求子串

```c++
// 求子串。用 Sub 返回串 S 的第 pos 个字符起长度为 len 的子串
bool SubString(SString &Sub, SString S, int pos, int len) {
    // 子串范围越界
    if (pos + len - 1 > S.length) {
        return false;
    }
    for (int i = pos; i < pos + len; i++) {
        Sub.ch[i - pos + 1] = S.ch[i];
    }
    Sub.length = len;
    return true;
}
```

#### 实现比较操作

```c++
// 比较操作。若 S>T，则返回值 >0 ；若S=T，则返回值 =0 ；若 S<T，则返回值 <0
int StrCompare(SString S, SString T) {
    for (int i = 1; i <= S.length && i <= T.length; i++) {
        if (S.ch[i] != T.ch[i]) {
            return S.ch[i] - T.ch[i];
        }
    }
    // 扫描过所有字符都相同，则长度长的串更大
    return S.length - T.length;
}
```

#### 实现定位操作

```c++
// 定位操作。
// 若主串 S 中存在与串 T 值相同的子串，则返回它在主串 S 中第一次出现的位置；
// 否则函数值为0
int Index(SString S, SString T) {
    int i = 1, n = S.length, m = T.length;
    SString sub;
    while (i <= n - m + 1) {
        SubString(sub, S, i, m);        // 求子串
        if (StrCompare(sub, T) != 0) {  // 比较操作
            ++i;
        } else {
            return i;  // 返回子串在主串中的位置
        }
    }
    return 0;
}
```

## 串的模式匹配

### 简单的模式匹配算法

子串的定位操作通常称为串的模式匹配，它求的是子串（常称模式串）在主串中的位置。这里采用定长顺序存储结构，给出一种不依赖于其他串操作的暴力匹配算法。

```c++
// 朴素模式匹配算法
int Index2(SString S, SString T) {
    int k = 1;  // 指向主串开始匹配的位置
    int i = k;  // 主串匹配时的匹配位置 
    int j = 1;  // 子串匹配时对应的位置
    while (i <= S.length && j <= T.length) {
        if (S.ch[i] == T.ch[j]) {  // 相等检查下一个字符
            ++i;
            ++j;
        } else {  // 有不相等检查下一个子串
            k++;
            i = k; // 如果不设置k，i=i-j+2
            j = 1;
        }
    }
    // 如果因为主串匹配完导致结束需要判断子串是否刚好匹配
    // 如果j超出边界说明子串得到匹配
    if (j > T.length) {
        return k;
    } else {
        return 0;
    }
}
```

在上述算法中，分别用计数指针 $i$ 和 $j$ 指示主串 $S$ 和模式串 $T$ 中当前正待比较的字符位置。算法思想为：从主串 $S$ 的第一个字符起，与模式 $T$ 的第一个字符比较，若相等，则继续逐个比较后续字符；否则从主串的下一个字符起，重新和模式的字符比较；以此类推，直至模式 $T$ 中的每个字符依次和主串 $S$ 中的一个连续的字符序列相等，则称匹配成功，函数值为与模式 $T$ 中第一个字符相等的字符在主串 $S$ 中的序号，否则称匹配不成功，函数值为零。将主串中与模式串长度相同的子串搞出来，挨个与模式串对比当子串与模式串某个对应字符不匹配时，就立即放弃当前子串，转而检索下一个子串。

若 $m$ 为模式串长度，$n$ 为主串长度，则

+ 匹配成功的最好时间复杂度为：$O(m)$ 
+ 匹配失败的最好时间复杂度为：$O(n-m+1)=O(n-m)\approx O(n)$
+ 匹配成功的最坏时间复杂度为：$O(nm-m^2+m)\approx O(nm)$

最坏情况：每个子串的前 $m-1$ 个字符都和模式串匹配，只有第 $m$ 个字符不匹配；

比较好的情况：每个子串的第 1 个字符就与模式串不匹配。

### KMP算法

> 由D. E.Knuth，J.H.Morris和 V.R.Pratt 提出，因此称为KMP算法

推荐直接看视频理解思想：[数据结构，KPM算法](https://www.bilibili.com/video/BV1b7411N798?p=35)

概况是对朴素模式匹配算法的优化，通过引入next数组来减少回溯。

 ```c++
 // KMP算法思想
 int Index_KMP(SString S, SString T, int next[]) {
     int i = 1, j = 1;
     while (i <= S.length && j <= T.length) {
         if (j == 0 || S.ch[i] == T.ch[j]) {
             ++i;
             ++j;
         } else {
             j = next[j]; // 模式串向右移动
         }
     }
     if (j > T.length) {
         return i - T.length;
     } else {
         return 0;
     }
 }
 ```

#### 求模式串的next数组

+ 串的前缀：包含第一个字符，且不包含最后一个字符的子串。
+ 串的后缀：包含最后一个字符，且不包含第一个字符的子串.

next数组手算方法：当第 j 个字符匹配失败，由前 1~j-1 个字符组成的串记为 S，则：next[j] = S的最长相等前缀长度+1，特别的 next[1] = 0；此外存在next[2]时 next[2] = 1。

#### KMP算法性能分析

```c++
// 获取next数组
void get_next(SString T, int next[]) {
    int i = 1, j = 0;
    next[1] = 0;
    while (i < T.length) {
        if (j == 0 || T.ch[i] == T.ch[j]) {
            ++i;
            ++j;
            next[i] = j;
        } else {
            j = next[j];
        }
    }
}

// KMP算法实现
int Index_KMP(SString S, SString T) {
    int i = 1, j = 1;
    int next[T.length + 1];
    get_next(T, next);
    while (i <= S.length && j <= T.length) {
        if (j == 0 || S.ch[i] == T.ch[j]) {
            ++i;
            ++j;
        } else {
            j = next[j];
        }
    }
    if (j > T.length) {
        return i - T.length;
    } else {
        return 0;
    }
}
```

KMP算法平均时间复杂度：$O(n+m)$

#### KMP算法优化

next 数组在某些情况下尚有缺陷，还可以进一步优化。

```c++
// nextval
void get_nextval(SString T, int nextval[]) {
    int i = 1, j = 0;
    nextval[1] = 0;
    while (i < T.length) {
        if (j == 0 || T.ch[i] == T.ch[j]) {
            nextval[i] = j;
            if (T.ch[i] != T.ch[j]) {
                nextval[i] = j;
            } else {
                nextval[i] = nextval[j];
            }
        } else {
            j = nextval[j];
        }
    }
}
```













