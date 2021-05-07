---
title: 数据结构——栈和队列
date: 2021-05-02 16:35:51
description: 描述。
# top_img: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面顶部图片
# cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面缩略图
mathjax: true
large: true
bilibili_banner: spring
---

【考纲内容】

+ 栈和队列的基本概念
+ 栈和队列的顺序存储结构
+ 栈和队列的链式存储结构
+ 栈和队列的应用
+ 特殊矩阵的压缩存储

【知识框架】

TODO 制作栈和队列章节的脑图

【复习提示】

本章通常以选择题的形式考查，题目不算难，但命题的形式比较灵活，其中栈（出入栈的过程、出栈序列的合法性）和队列的操作及其特征是重点。由于它们均是线性表的应用和推广，因此也容易出现在算法设计题中。此外，栈和队列的顺序存储、链式存储及其特点、双端队列的特点、栈和队列的常见应用，以及数组和特殊矩阵的压缩存储都是读者必须掌握的内容。

## 栈

### 栈的基本概念

#### 栈的定义

栈（Stack）：只允许在一端进行插入或删除操作的线性表。首先栈是一种线性表，但限定这种线性表只能在某一端进行插入和删除操作。

栈顶（Top）：线性表允许进行插入删除的那一端。

栈底（Bottom）：固定的，不允许进行插入和删除的另一端。

空栈：不含任何元素的空表。

假设某个栈 $S = (a_{1},a_2, a_3,a_4,a_5)$，则 $a_1$ 为栈底元素，$a_5$ 为栈顶元素。由于栈只能在栈顶进行插入和删除操作，进栈次序依次为 $a_{1},a_2, a_3,a_4,a_5$ 而出栈次序为 $a_{5},a_4, a_3,a_2,a_1$。由此可见，栈的操作特性可以明显地概括为后进先出（Last In First Out，LIFO）。

栈的数学性质：$n$ 个不同元素进栈，出栈元素不同排列的个数为 $\frac{1}{n+1}C_{2n}^{n}  $。上述公式称为{% wavy 卡特兰（Catalan）数 %}，可采用数学归纳法证明。

#### 找的基本操作

+ `InitStack(&S)`：初始化一个空栈 S。
+ `StackEmpty(S)`：判断一个栈是否为空，若栈 S 为空则返回 true，否则返回 false。
+ `Push(&S,x)`：进栈，若栈 S 未满，则将 x 加入使之成为新栈顶。
+ `Pop(&S,&x)`：出栈，若栈 S 非空，则弹出栈顶元素，并用 x 返回。
+ `GetTop(S, &x) `：读栈顶元素，若栈 S 非空，则用 x 返回栈项元素。
+ `DestroyStack(&S)`：销毁栈，并释放栈 S 占用的存储空间。

### 栈的顺序存储结构

栈是一种操作受限的线性表，类似于线性表，它也有对应的两种存储方式。

#### 顺序栈的实现

采用顺序存储的栈称为<font color="#ea66a6">顺序栈</font>，它利用一组地址连续的存储单元存放自栈底到栈顶的数据元素，同时附设一个指针（top）指示当前栈项元素的位置。栈的顺序存储类型可描述为：

```c++
#define MaxSize 10
typedef struct {
    int data[MaxSize];
    int top;
} SqStack;
```

+ 栈顶指针：`S.top`，初始时设置 `S.top=-1`；
+ 栈顶元素：`S.data[S.top]`；
+ 进栈操作：栈不满时，栈顶指针先加1，再送值到栈顶元素。
+ 出栈操作：栈非空时，先取栈顶元素值，再将栈项指针减 1。
+ 栈空条件：`S.top==-1`；
+ 栈满条件：`S.top==MaxSize-1`
+ 栈长：`S.top+1`

由于顺序栈的入栈操作受数组上界的约束，当对栈的最大使用空间估计不足时，有可能发生栈上溢，此时应及时向用户报告消息，以便及时处理，避免出错。

#### 顺序栈的基本运算

初始化：

```c++
void InitStack(SqStack &S){
    S.top = -1;
}
```

判断栈空：

```c++
bool StackEmpty(SqStack S) {
    if (S.top == -1) {
        return true;
    } else {
        return false;
    }
}
```

进栈：

```c++
bool Push(SqStack &S,int x){
    if(S.top == MaxSize-1){
        return false;
    }
    S.data[++S.top] = x;
    return true;
}
```

当找不满时， top 先加 1，再入栈。

出栈：

```c++
bool Pop(SqStack &S,int &x){
    if(S.top == -1){
        return false;
    }
    X = S.data[S.top--];
    return true;
}
```

读取栈顶元素：

```c++
bool Pop(SqStack &S,int &x){
    if(S.top == -1){
        return false;
    }
    X = S.data[S.top];
    return true;
}
```

> 注意：这里 top 指向的是栈顶元素，所以进栈操作为 `S.data[++S.top]=x`，出栈操作为`x=S.data[S.top--]`。
>
> 若栈顶指针初始化为 `S.top=0`，即 top 指向栈顶元素的下一位置，则入栈操作变为`S.data[S.top++]=x`; 出栈操作变为 `x=S.data[--S.top]`。 相应的栈空、栈满条件也会发生变化。

#### 共享栈

利用栈底位置相对不变的特性，可让两个顺序栈共享一个维数组空间，将两个栈的栈底分
别设置在共享空间的<font color="#faa755">两端</font>，两个栈顶向共享空间的中间延伸。

两个栈的栈顶指针都指向栈顶元素，`top0=-1`时0号栈为空，`top1=MaxSize` 时1号栈为
空；仅当两个栈顶指针相邻（`top1-top0=1`） 时，判断为栈满。当0号栈进栈时 top0 先加1
再赋值，1号栈进栈时 top1 先减1再赋值；出栈时则刚好相反。

共享栈是为了更有效地利用存储空间，两个栈的空间相互调节，只有在整个存储空间被占满
时才发生溢。其存取数据的时间复杂度均为$O(1)$，所以对存取效率没有什么影响。

### 栈的链式存储结构

采用链式存储的栈称为链栈，链栈的优点是便于多个栈共享存储空间和提高其效率，且不存在栈满上溢的情况。通常采用单链表实现，并规定所有操作都是在单链表的表头进行的。拢的链式存储类型可描述为：

```c++
typedef struct LinkNode {
    int data;
    struct LinkNode *next;
} *LiStack;
```

采用链式存储，便于结点的插入与删除。链栈的操作与链表类似，入栈和出栈的操作都在链表的表头进行。需要注意的是，对于带头结点和不带头结点的链栈，具体的实现会有所不同。

## 队列

### 队列的基本概念

#### 队列的定义

队列（Queue）简称队，也是一种操作受限的线性表，只允许在表的一端进行插入，而在表的另一端进行删除。向队列中插入元素称为入队或进队；删除元素称为出队或离队。这和我们日常生活中的排队是一致的，最早排队的也是最早离队的，其操作的特性是先进先出（First In First Out，FIFO）。

+ 队头（Front）：允许删除的一端，又称队首。
+ 队尾（Rear）：允许插入的一端。
+ 空队列：不含任何元素的空表。

#### 队列常见的基本操作

+ `InitQueue(&Q)`：初始化队列，构造一个空队列 Q。
+ `QueueEmpty(Q)`：判队列空，若队列 Q 为空返回 true，否则返回 false。
+ `EnQueue(&Q,x)`：入队，若队列 Q 未满，将 x 加入，使之成为新的队尾。
+ `DeQueue (&Q, &X)`：出队，若队列 Q 非空，删除队头元素，并用 x 返回。
+ `GetHead(Q,&x)`：读队头元素，若队列 Q 非空，则将队头元素赋值给 x。

### 队列的顺序存储结构

#### 队列的顺序存储

队列的顺序实现是指分配一块连续的存储单元存放队列中的元素，并附设两个指针：队头指
针 front 指向队头元素，队尾指针 rear 指向队尾元素的下一个位置（不同教材对 front 和 rear 的定义可能不同，例如，可以让 rear 指向队尾元素、front 指向队头元素。对于不同的定义，出队入队的操作是不同的）。队列的顺序存储类型可描述为：

```c++
#define MaxSize 10
typedef struct {
    int data[MaxSize];
    int front,rear;
} SqQueue;
```

+ 初始状态（队空条件）：`Q.front==Q.rear==0`。
+ 进队操作：队不满时，先送值到队尾元素，再将队尾指针加 1。
+ 出队操作：队不空时，先取队头元素值，再将队头指针加 1。

这样的顺序存储只能进行一次的排满队和出队的操作。

#### 循环队列

这里引出循环队列的概念将顺序队列臆造为一个环状的空间，即把存储队列元素的表从逻辑上视为一个环，称为循环队列。当队首指针 `Q. front=MaxSize-1` 后，再前进一个位置就自动到 0，这可以利用除法取余运算来实现。

+ 初始时：`Q.front=Q.rear=0`
+ 队首指针进1：`Q.front=(Q.front+1)%MaxSize`
+ 队尾指针进1：`Q.rear=(Q.rear+1)%MaxSize`
+ 队列长度：`(Q.rear+MaxSize-Q.front)%MaxSize`

为了区分队空还是 队满的情况，有3种处理方式：

1. 牺牲一个单元来区分队空和队满，入队时少用一个队列单元，这是较为普遍的做法约定以“队头指针在队尾指针的下一位置作为队满的标志”。
   + 队满条件：`(Q.rear+1)%MaxSize==Q.front`
   + 队空条件：`Q.front==Q.rear`
   + 队中元素个数：`(Q.rear-Q.front+MaxSize)%MaxSize`
2. 类型中增设表示元素个数的数据成员。这样，队空的条件为`Q.size==0`；队满的条件为   `Q.size==MaxSize`。这两种情况都有`Q.front==Q.rear`。
3. 类型中增设 tag 数据成员，以区分是队满还是队空。每次删除操作成功，都令`tag=0`；每次插入操作成功时，都令`tag=1`。
   + tag 等于 0 时，若因删除导致`Q.front==Q.rear`，则为队空；
   + tag 等于 1 时，若因插入导致`Q.front==Q.rear`，则为队满。

#### 循环队列的操作


```c++
// 判断队列是否为空
bool QueueEmpty(SqQueue Q){
    if(Q.rear==Q.front){
        return true;
    } else {
        return false;
    }
}

// 入队
bool EnQueue(SqQueue &Q,int x){
    if((Q.rear+1)%MaxSize==Q.front){
        return false;
    }
    Q.data[Q.rear]=x;
    Q.rear=(Q.rear+1)%MaxSize;
    return true;
}

// 出队
bool DeQueue(SqQueue &Q,int &x){
    if(Q.rear==Q.front){
        return false;
    }
    x = Q.data[Q.front];
    Q.front = (Q.front+1)%MaxSize;
    return true;
}

// 获得队头值
bool GetHeaf(SqQueue &Q,int &x){
    if(Q.rear==Q.front){
        return false;
    }
    x = Q.data[Q.front];
    return true;
}
```

### 队列的链式存储结构

#### 队列的链式存储

队列的链式表示称为链队列，它实际上是一个同时带有队头指针和队尾指针的单链表。头指针指向队头结点，尾指针指向队尾结点，即单链表的最后一个结点（注意与顺序存储的不同）。队列的链式存储类型可以描述为：

```c++
typedef struct LinkNode {  // 链式队列结点
    int data;
    struct LinkNode *next;
} LinkNode;

typedef struct {  // 链式队列
    LinkNode *front, *rear;
} LinkQueue;
```

#### 带头结点链式队列的基本操作


```c++
#include <cstdlib>
// 初始化（带头结点）
void InitQueue(LinkQueue &Q) {
    // 初始时 front、rear 都指向头节点
    Q.front = Q.rear = (LinkNode *)malloc(sizeof(LinkNode));
    Q.front->next = NULL;
}

// 判断队列是否为空
bool IsEmpty(LinkQueue Q) {
    if (Q.front == Q.rear) {
        return true;
    } else {
        return false;
    }
}

// 入队
void EnQueue(LinkQueue &Q, int x) {
    LinkNode *s = (LinkNode *)malloc(sizeof(LinkNode));
    s->data = x;
    s->next = NULL;
    Q.rear->next = s;
    Q.rear = s;
}

// 出队
bool DeQueue(LinkQueue &Q, int &x) {
    if (Q.front == Q.rear) {
        return false;  // 空队列
    }
    LinkNode *p = Q.front->next;
    x = p->data;              // 变量x返回队头元素
    Q.front->next = p->next;  // 修改头结点的next指针
    if (Q.rear == p) {     // 如果删除的是队列中的最后一个结点
        Q.rear = Q.front;  // 设置队列为空
    }
    free(p);
    return true;
}

// QueueByChain.cpp
```

#### 不带头结点链式队列的基本操作

```c++
// 初始化（不带头结点）
void InitQueue(LinkQueue &Q){
    Q.front = NULL;
    Q.rear = NULL;
}

// 判断队列是否为空
bool IsEmpty(LinkQueue Q){
    if(Q.front==NULL){
        return true;
    } else {
        return false;
    }
}

// 入队
void EnQueue(LinkQueue &Q,int x){
    LinkNode *s = (LinkNode *)malloc(sizeof(LinkNode));
    s->data = x;
    s->next = NULL;
    if(Q.front == NULL){
        Q.front = s;
        Q.rear = s;
    } else {
        Q.rear->next = s;
        Q.rear = s
    }
}

// 出队
bool DeQueue(LinkQueue &Q, int &x) {
    if (Q.front == nullptr) {
        return false;  // 空队列
    }
    LinkNode *p = Q.front;
    x = p->data;            // 变量x返回队头元素
    Q.front = p->next;      // 修改头结点的next指针
    if (Q.rear == p) {      // 如果删除的是队列中的最后一个结点
        Q.front = nullptr;  // 设置队列为空
        Q.rear = nullptr;
    }
    free(p);
    return true;
}
// QueueByChainWithoutHead.cpp
```

### 双端队列

双端队列是指允许两端都可以进行入队和出队操作的队列。其元素的逻辑结构仍是线性结构。将队列的两端分别称为前端和后端，两端都可以入队和出队。

+ 栈：只允许从一端插入和删除的线性表
+ 队列：只允许从一端插入、另一端删除的线性表
+ 双端队列：只允许从两端插入、两端删除的线性表
+ 输入受限的双端队列：只允许从一端插入、 两端删除的线性表
+ 输出受限的双端队列：只允许从两端插入、一端删除的线性表

## 栈和队列的应用

### 栈在括号匹配中的应用

```c++
// 数据结构
#define MaxSize 10
typedef struct {
    char data[MaxSize];
    int top;  // 栈顶指针
} SqStack;
// 初始化栈
void InitStack(SqStack &S)
// 判断栈是否为空
bool StackEmpty(SqStack S) 
// 入栈
bool Push(SqStack &S, char x) 
// 出栈
bool Pop(SqStack &S, char &x) 
```

```c++
// 括号匹配
bool bracketCheck(char str[], int length) {
    SqStack S;
    InitStack(S);
    for (int i = 0; i < length; i++) {
        if (str[i] == '(' || str[i] == '[' || str[i] == '{') {
            Push(S, str[i]);  // 扫描左括号入栈
        } else {
            if (StackEmpty(S)) {  // 扫描右括号，如果栈空
                return false;     // 匹配失败
            }
            char topElem;
            Pop(S, topElem);  // 栈顶元素出栈
            if (str[i] == ')' && topElem != '(') {
                return false;
            }
            if (str[i] == ']' && topElem != '[') {
                return false;
            }
            if (str[i] == '}' && topElem != '{') {
                return false;
            }
        }
    }
    return StackEmpty(S);  // 检查完全部括号后，栈空说明匹配成功
}
// BracketCheck.cpp
```

### 栈在表达式求值的应用

#### 算数表达式

算数表达式由三个部分组成：操作数、运算符、界限符（即括号，反映计算的先后顺序）

+ 中缀表达式：运算符在两个操作数的中间，如 `a + b`，`a + b - c`，`a + b - c * d`
+ 后缀表达式：运算符在两个操作数的后面，如 `a b +`，`a b + c -` ，`a b + c d * - `
+ 前缀表达式：运算符在两个操作数的前面，如 `+ a b `，`- + a b c` ，`- + a b * c d`

#### 中缀转后缀

**中缀转后缀**的手算方法：

1. 确定中缀表达式中各个运算符的运算顺序
2. 选择下一个运算符，按照「左操作数 右操作数 运算符」的方式组合成一个新的操作数
3. 如果还有运算符没被处理，就继续步骤2

> 注意：保证手算和计算机算结果相同，需要遵循“左优先”原则，即只要左边的运算符能先计算就优先计算左边的。（可以保证运算顺序唯一）

中缀转后缀的机算方法：

+ 初始化一个栈，用于保存暂时还不能确定运算顺序的运算符。
+ 从左到右处理各个元素，直到末尾。可能遇到三种情况：
  1. 遇到操作数。直接加入后缀表达式
  2. 遇到界限符。遇到 `(` 直接入栈；遇到 `)` 则依次弹出栈内运算符并加入后缀表达式，直到弹出 `(` 为止。注意：`)` 不加入后缀表达式。
  3. 遇到运算符。依次弹出栈中优先级高于或等于当前运算符的所有运算符，并加入后缀表达式，若碰到 `(` 或栈空则停止。之后再把当前运算符入栈。
+ 按上述方法处理完所有字符后，将栈中剩余运算符依次弹出，并加入后缀表达式。

后缀表达式结果的手算方法：从左往右扫描，每遇到一个运算符，就让运算符前面最近的两个操作数执行对应运算，合体为一个操作数。

用栈实现后缀表达式的计算：

1. 从左往右扫描下一个元素，直到处理完所有元素
2. 若扫描到操作数则压入栈，并回到步骤1；否则执行步骤3
3. 若扫描到运算符，则弹出两个栈顶元素（先出栈的是“右操作数”），执行相应运算，运算结果压回栈顶，回到步骤1

#### 中缀转前缀

前缀表达式较后缀表达式用的较少

**中缀转前缀**的手算方法：

1. 确定中缀表达式中各个运算符的运算顺序
2. 选择下一个运算符，按照「运算符 左操作数 右操作数」的方式组合成一个新的操作数
3. 如果还有运算符没被处理，就继续步骤2

> 注意：同样为保证手算和计算机算结果相同，需要遵循“右优先”原则，即只要右边的运算符能先计算，就优先算右边的。

用栈实现前缀表达式的计算：

1. {% wavy 从右往左 %}扫描下一个元素，直到处理完所有元素
2. 若扫描到操作数则压入栈，并回到步骤1；否则执行步骤3
3. 若扫描到运算符，则弹出两个栈顶元素（先出栈的是“左操作数”），执行相应运算，运算结果压回栈顶，回到步骤1

#### 中缀表达式的计算

用栈实现中缀表达式的计算（中缀转后缀+后缀表达式求值）：

1. 初始化两个栈，操作数栈和运算符栈
2. 若扫描到操作数，压入操作数栈
3. 若扫描到运算符或界限符，则按照“中缀转后缀”相同的逻辑压入运算符栈（期间也会弹出运算符，每当弹出一个运算符时，就需要再弹出两个操作数栈的栈顶元素并执行相应运算，运算结果再压回操作数栈）

TODO 中缀表达式的计算的代码实现

### 栈在递归的应用

函数调用的特点：最后被调用的函数最先执行结束（LIFO）

函数调用时，需要用一个栈存储：调用返回地址；实参；局部变量

适合用“递归”算法解决：可以把原始问题转换为属性相同，但规模较小的问题。必须注意递归模型不能是循环定义的，其必须满足下面的两个条件，即递归表达式（递归体）和边界条件（递归出口）。

缺点：太多层递归可能会导致栈溢出

例如，计算正整数的阶乘 $n!$ ：

$$\mathrm{factorial}(n)=\begin{cases}
  n*\mathrm{factorial}(n-1), & n>1 \\\\  
  1, & n=1 \\\\
  1, & n=0
\end{cases}$$

```c++
// 计算正整数 n!
#include <iostream>
using namespace std;

int factorial(int n) {
    if (n == 0 || n == 1) {
        return 1;
    } else {
        return n * factorial(n - 1);
    }
}

int main() {
    int x = factorial(3);
    printf("%d", x);
}
```

求斐波那契数列：


$$\mathrm{Fib}(n)=\begin{cases}
  \mathrm{Fib}(n-1)+\mathrm{Fib}(n-2), & n>1 \\\\  
  1, & n=1 \\\\
  0, & n=0
\end{cases}$$



```c++
// 计算斐波那契数列
#include <iostream>
using namespace std;

int Fib(int n) {
    if (n == 0) {
        return 0;
    } else if (n == 1) {
        return 1;
    } else {
        return Fib(n - 1) + Fib(n - 2);
    }
}

int main() {
    for (int i = 0; i <= 10; i++) {
        printf("%d,", Fib(i));
    }
}
```

![斐波那契数列递归计算过程](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/斐波那契数列递归计算过程.png)

显然，在递归调用的过程中，`Fib(3)` 被计算了2次，`Fib(2)` 被计算了3次。`Fib(1)` 被调用了5次，`Fib(0)`被调用了3次。所以，递归的效率低下，但优点是代码简单，容易理解。

可以将递归算法转换为非递归算法，通常需要借助找来实现这种转换。

### 队列在层次遍历中的应用

层次遍历二叉树的过程：

1. 根节点入队
2. 若队空（所有结点都已处理完毕），则结束遍历；否则重复步骤3操作。
3. 队列中第一个结点出队，并访问之。若其有左孩子，则将左孩子入队；若其有右孩子，则将右孩子入队，返回步骤2。

![层次遍历二叉树的过程](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/层次遍历二叉树的过程.png)

### 队列在图的广度优先遍历的应用

详见“图”章节，思想类似于树的层次遍历

### 队列在计算机系统中的应用

多个进程争抢着使用有限的系统资源时，FCFS ( First Come First Service）先来先服务是一种常用策略。

## 特殊矩阵的压缩存储

矩阵在计算机图形学、工程计算中占有举足轻重的地位。在数据结构中考虑的是如何用最小的内存空间来存储同样的一组数据。所以，我们不研究矩阵及其运算等，而把精力放在如何将矩阵更有效地存储在内存中，并能方便地提取矩阵中的元素。

### 数组的定义

<font color="#ea66a6">数组</font>是由 $n\ (n\ge 1)$ 个相同类型的数据元素构成的有限序列，每个数据元素称为一个数组元素，每个元素在 $n$ 个线性关系中的序号称为该元素的下标，下标的取值范围称为数组的维界。

数组与线性表的关系：数组是线性表的推广。一维数组可视为一个线性表；二维数组可视为其元素也是定长线性表的线性表，以此类推。数组一旦被定义，其维数和维界就不再改变。因此，除结构的初始化和销毁外，数组只会有存取元素和修改元素的操作。

### 数组的存储结构

大多数计算机语言都提供了数组数据类型，逻辑意义上的数组可采用计算机语言中的数组数据类型进行存储，一个数组的所有元素在内存中占用一段连续的存储空间。

以一维数组 `A[0...n-1]` 为例，其存储结构关系式为：$$LOC(a_i)=LOC(a_0)+i\times L （0\le i < n）$$ 其中，$LOC$ 是内存地址，$L$ 是每个数组元素所占的存储单元。

对于多维数组，有两种映射方法：按行优先（一行一行存，一行存完存下一行）和按列优先（一列一列存，一列存完存下一列）。以二维数组为例：

按行优先存储的基本思想是：先行后列，先存储行号较小的元素，行号相等先存储列号较小的元素。设二维数组的行下标与列下标的范围分别为 $[0, h_1]$ 与 $[0, h_2]$，则存储结构关系式为：$$LOC(a_{i,j})=LOC(a_{0,0})+[i \times (h_2+1) +j] \times L$$ 当以列优先方式存储时， 得出存储结构关系式为：$$LOC(a_{i,j})=LOC(a_{0,0})+[j \times (h_1+1) +i] \times L$$

### 矩阵的压缩存储

普通矩阵可用二维数组存储，特殊矩阵可以压缩存储空间。

压缩存储：指为多个值相同的元素只分配一个存储空间，对零元素不分配存储空间。其目的是为了节省存储空间。

特殊矩阵：指具有许多相同矩阵元素或零元素，并且这些相同矩阵元素或零元素的分布有一定规律性的矩阵。常见的特殊矩阵有对称矩阵、上（下）三角矩阵、对角矩阵等。

特殊矩阵的压缩存储方法：找出特殊矩阵中值相同的矩阵元素的分布规律，把那些呈现规律性分布的、值相同的多个矩阵元素压缩存储到一个存储空间中。

#### 对称矩阵

若对一个 $n$ 阶方阵 $A[1...n] [1...n]$ 中的任意一个元素 $a_{i,j}$ 都有 $a_{i,j}=a_{j,i}\ (a \le i,j \le n)$ ，称为对称矩阵。对于一个 $n$ 阶方阵，其中的元素可以划分为3个部分，即上三角区（$i < j$）、主对角线（$i=j$）和下三角区（$i >j$）。


$$\begin{bmatrix}
 a_{1,1} & a_{1,2} & \dots  & a_{1,n} \\\\
 a_{2,1} & a_{2,2} & \dots & a_{2,n} \\\\
 \vdots  &  \vdots & \ddots  & \vdots \\\\
 a_{n,1} & a_{n,2} & \dots  &  a_{n,n}
\end{bmatrix}$$ 


对于 $n$ 阶对称矩阵，上三角区的所有元素和下三角区的对应元素相同，若仍采用二维数组存放，则会浪费几乎一半的空间，为此将对称矩阵 $A[1..n] [1..n]$ 存放在一维数组 $B[\frac{n(n+1)}{2} ]$ 中，即元素 $a_{i,j}$ 存放在 $b_k$ 中。只按{% wavy 行优先 %}原则将各元素存入一维数组中，存放下三角部分（含主对角）的元素。

在数组 $B$ 中，位于元素 $a_{i,j} \ (i \ge j)$ 前面的元素个数为：

+ 第1行：1个元素 $(a_{1,1})$
+ 第2行：2个元素 $(a_{2,1},a_{2,2})$
+ ……
+ 第 $ i-1 $ 行：$i-1$ 个元素 $(a_{i-1,1},a_{i-1,2},\dots ,a_{i-1,i-1})$ 
+ 第 $i$ 行：$j-1$ 个元素 $(a_{i,1},a_{i,2},\dots a_{i,j-1})$

因此，元素 $a_{i,j}$ 在数组 $B$ 中的下标 $k=1 +2+ \dots +(i - 1) + j- 1 = \frac{i(i-1)}{2}+j-1$ （数组下标从0开始）。因此，元素下标之间的对应关系如下：

$$k = \begin{cases} 
  \frac{i(i-1)}{2}+j-1, & i \ge j \\\\  
  \frac{j(j-1)}{2}+i-1, & i < j
\end{cases}$$

TODO 对称矩阵压缩代码实现

#### 三角矩阵


$$\begin{bmatrix}  
  a_{1,1} &  \\\\  
  a_{2,1} & a_{2,2} \\\\  
  \vdots & \vdots & \ddots  \\\\  
  a_{n,1} & a_{n,2} & \cdots & a_{n,n}  
\end{bmatrix} 
\begin{bmatrix}  
  a_{1,1}& a_{1,2} & \cdots & a_{1,n} \\\\  
  & a_{2,2} & \cdots &a_{2,n} \\\\  
   &  & \ddots & \vdots \\\\  
   &  &  & a_{n,n}
\end{bmatrix} $$


下三角矩阵中，上三角区的所有元素均为同一常量。其存储思想与对称矩阵类似，不同之处在于存储完下三角区和主对角线上的元素之后，紧接着存储对角线上方的常量一次，故可以将下三角矩阵 $A[1\dots n][1\dots n]$ 压缩存储在 $B[\frac{n(n+1)}{2}+1]$ 中。元素下标之间的对应关系为：

$$k = \begin{cases} \frac{i(i-1)}{2}+j-1, & i \ge j \\\\ \frac{n(n+1)}{2}, & i < j \end{cases}$$

上三角矩阵中，下三角区的所有元素均为同一常量。只需存储主对角线、上三角区上的元素和下三角区的常量一次，可将其压缩存储在 $B[\frac{n(n+1)}{2}+1]$ 中，元素下标之间的对应关系如下：


$$ k = \begin{cases} 
  \frac{(i-1)(2n-i+2)}{2}+(j-i), & i \ge j \\\\  
  \frac{n(n+1)}{2}, & i < j
\end{cases} $$

TODO 三角矩阵压缩代码实现

#### 三对角矩阵

对角矩阵也称带状矩阵。对于 $n$ 阶方阵 $\mathit{A} $ 中任一元素 $a_{i,j}$ ，当 $|i-j|>1$ 时，有 $a_{i,j}=0 \ (1\le i,j \le n)$ ，则称为三对角矩阵。


$$\begin{bmatrix}  
  a_{1,1}& a_{1,2}&   &  \\\\
  a_{2,1}& a_{2,2}& a_{2,3} &   &   \\\\
  & a_{3,2}& a_{3,3} & a_{3,4}  &   \\\\
 & &\ddots &\ddots &  \ddots &  \\\\
  && &a_{n-1,n-2} & a_{n-1,n-1} & a_{n-1,n} \\\\  
  && & & a_{n,n-1} & a_{n,n}  
\end{bmatrix} $$


在三对角矩阵中，所有非零元素都集中在以主对角线为中心的3条对角线区域，其他区域的元素都为0。

三对角矩阵 $\mathit{A} $ 也可以采用压缩存储，将3条对角线上的元素按{% wavy 行优先 %}方式存放在一维数组 $B[3n-2]$ 中，且 $a_{1,1}$ 存放于 $B[0]$ 中。

三对角矩阵 $\mathit{A} $ 3对角线上的元素 $a_{i,j} \ (1 \le i,j \le n,\ |i-j| \le 1)$ 在一维数组 $B$ 中存放的下标为 $k=2i+j-3$

反之，若己知三对角线矩阵中某元素 $a_{i,j}$， 存放于一维数组 $B$ 的第 $k$ 个位置，则可得 $i=\left \lfloor {\frac{k+1}{3}+1} \right \rfloor $ ，$j=k-2i+3$

TODO 三对角矩阵压缩实现

### 稀疏矩阵

矩阵中非零元素的个数 $t$，相对矩阵元素的个数 $s$ 来说非常少，即 $s\gg t$ 的矩阵称为稀疏矩阵。例如，一个矩阵的阶为100×100，该矩阵中只有少于100个非零元素。

若采用常规的方法存储稀疏矩阵，则相当浪费存储空间，因此仅存储非零元素。但通常零元素的分布没有规律，所以仅存储非零元素的值是不够的，还要存储它所在的行和列。因此，将非零元素及其相应的行和列构成一个三元组（行标，列标，值）。然后再按照某种规律存储这些三元组。稀疏矩阵压缩存储后便失去了随机存取特性。

稀疏矩阵的三元组既可以采用数组存储，也可以采用十字链表法存储。

TODO 稀疏矩阵两种方法的C++语言实现