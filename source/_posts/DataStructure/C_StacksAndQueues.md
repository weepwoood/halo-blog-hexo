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

TODO 制作脑图

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

栈的数学性质：$n$ 个不同元素进栈，出栈元素不同排列的个数为 $\frac{1}{n+1}C_{2n}^{n}  $。上述公式称为卡特兰（Catalan）数，可采用数学归纳法证明。

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
typedef struct LinkNode{  // 链式队列结点
    int data;
    struct LinkNode *next;
} LinkNode;

typedef struct{  // 链式队列
    LinkNode *front,*rear;
}LinkQueue;
```

```c++
// 初始化（带头结点）
void InitQueue(LinkQueue &Q){
    // 初始时 front、rear 都指向头节点
    Q.front = Q.rear = (LinkNode*)malloc(sizeof(LinkNode));
    Q.front->next = NULL;
}

// 判断队列是否为空
bool IsEmpty(LinkQueue Q){
    if(Q.front==Q.rear){
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
    Q.rear->next = s;
    Q.rear = s
}
```

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
```




















