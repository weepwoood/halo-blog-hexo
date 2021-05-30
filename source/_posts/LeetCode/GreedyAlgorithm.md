---
title: 贪心算法
date: 2021-05-14 18:56:51
description: LeetCode刷题之贪心算法
# top_img: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面顶部图片
# cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面缩略图
mathjax: true
#katex: true
large: true
highlight_shrink: true
bilibili_banner: spring
categories: LeetCode
tags: 
- LeetCode
- 贪心算法
---

参考资料：

{% link 《LeetCode 101：和你一起你轻松刷题（C++）》, https://github.com/changgyhub/leetcode_101, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/GithubIcon.jpg %}

## 算法解释


顾名思义，贪心算法或贪心思想采用贪心的策略，保证每次操作都是局部最优的，从而使最后得到的结果是全局最优的。

举一个最简单的例子：小明和小王喜欢吃苹果，小明可以吃五个，小王可以吃三个。已知苹果园里有吃不完的苹果，求小明和小王一共最多吃多少个苹果。在这个例子中，我们可以选用的贪心策略为，每个人吃自己能吃的最多数量的苹果，这在每个人身上都是局部最优的。又因为全局结果是局部结果的简单求和，且局部结果互不相干，因此局部最优的策略也同样是全局最优的策略。

## 分配问题

### 分发饼干


{% link 455. 分发饼干, https://leetcode-cn.com/problems/assign-cookies/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}


假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。对每个孩子 `i` ，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]` 。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i` ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

{% tabs 分发饼干 %}
<!-- tab 示例 1 -->

输入：`g = [1,2,3]` ，`s = [1,1]`

输出：`1`

解释：你有三个孩子和两块小饼干，3 个孩子的胃口值分别是：1，2，3。虽然你有两块小饼干，由于他们的尺寸都是 1 ，你只能让胃口值是 1 的孩子满足。所以你应该输出 1 。

<!-- endtab -->

<!-- tab 示例 2 -->

输入：`g = [1,2]`，`s = [1,2,3]`

输出：`2`

解释：你有两个孩子和三块小饼干，2 个孩子的胃口值分别是 1，2。你拥有的饼干数量和尺寸都足以让所有孩子满足。所以你应该输出2。

<!-- endtab -->
{% endtabs %}

因为饥饿度最小的孩子最容易吃饱，所以我们先考虑这个孩子。为了尽量使得剩下的饼干可以满足饥饿度更大的孩子，所以我们应该把大于等于这个孩子饥饿度的、且大小最小的饼干给这个孩子。满足了这个孩子之后，我们采取同样的策略，考虑剩下孩子里饥饿度最小的孩子，直到没有满足条件的饼干存在。

简而言之，这里的贪心策略是，给剩余孩子里最小饥饿度的孩子分配最小的能饱腹的饼干。

至于具体实现，因为我们需要获得大小关系，一个便捷的方法就是把孩子和饼干分别排序。这样我们就可以从饥饿度最小的孩子和大小最小的饼干出发，计算有多少个对子可以满足条件。

```c++
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

class Solution {
   public:
    static int findContentChildren(vector<int>& g, vector<int>& s) {
        // 对胃口值进行升序排序
        sort(g.begin(), g.end());
        // 对饼干进行升序排序
        sort(s.begin(), s.end());
        int child = 0, cookie = 0;
        // 循环遍历，如果其中一个数组长度小于另一个则跳出循环
        while (child < g.size() && cookie < s.size()) {
            if (g[child] <= s[cookie]) {
                // 满足一个孩子
                child++;
            }
            // 无论是否满足都将饼干++
            cookie++;
        }
        // 返回满足的孩子数
        return child;
    }
};

void test1() {
    vector<int> children = {1, 2, 3};
    vector<int> cookies = {1, 1};
    int result = Solution::findContentChildren(children, cookies);
    cout << result << endl;
}
void test2() {
    vector<int> children = {1, 2};
    vector<int> cookies = {1, 2, 3};
    int result = Solution::findContentChildren(children, cookies);
    cout << result << endl;
}

int main() {
    cout << "example 1 " << endl;
    test1();
    cout << "example 2 " << endl;
    test2();
}
```

```python
def findContentChildren(g, s):
    # 升序排序
    g.sort()
    s.sort()
    child = cookie = 0
    while(child < len(g) and cookie < len(s)):
        if (g[child] <= s[cookie]):
            child += 1
        cookie += 1
    return child


test1 = findContentChildren([1, 2, 3], [1, 1])
print(test1)
test2 = findContentChildren([1, 2], [1, 2, 3])
print(test2)
```

```java
import java.util.Arrays;

class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(s);
        Arrays.sort(g);
        int child = 0, cookie = 0;
        while (child < g.length && cookie < s.length) {
            if (g[child] <= s[cookie]) {
                child++;
            }
            cookie++;
        }
        return child;
    }

    static void test(int[] g, int[] s){
        Solution solution = new Solution();
        int result = solution.findContentChildren(g, s);
        System.out.println(result);
    }

    public static void main(String[] args) {
        int[] g1 = {1, 2, 3};
        int[] s1 = {1, 1};
        System.out.println("example 1");
        test(g1,s1);

        int[] g2 = {1, 2};
        int[] s2 = {1, 2,3};
        System.out.println("example 2");
        test(g2,s2);
    }
}
```


 **<font color="#33a3dc">复杂度分析</font>**

时间复杂度：$O(m \log m + n \log n$)，其中 $m$ 和 $n$ 分别是数组 $g$ 和 $s$ 的长度。对两个数组排序的时间复杂度是 $O(m \log m + n \log n)$，遍历数组的时间复杂度是 $O(m+n)$，因此总时间复杂度是 $O(m \log m + n \log n)$。

空间复杂度：$O(\log m + \log n)$，其中 $m$ 和 $n$ 分别是数组 $g$ 和 $s$ 的长度。空间复杂度主要是排序的额外空间开销。

### 分发糖果


{% link 135. 分发糖果, https://leetcode-cn.com/problems/candy/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}

老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。

你需要按照以下要求，帮助老师给这些孩子分发糖果：

+ 每个孩子至少分配到 1 个糖果。
+ 评分更高的孩子必须比他两侧的邻位孩子获得更多的糖果。

那么这样下来，老师至少需要准备多少颗糖果呢？

{% tabs 分发糖果 %}
<!-- tab 示例 1 -->

输入：`[1,0,2]`

输出：`5`

解释：你可以分别给这三个孩子分发 2、1、2 颗糖果。

<!-- endtab -->

<!-- tab 示例 2 -->

输入：`[1,2,2]`

输出：`4`

解释：你可以分别给这三个孩子分发 1、2、1 颗糖果。第三个孩子只得到 1 颗糖果，这已满足上述两个条件。

<!-- endtab -->
{% endtabs %}


虽然这一道题也是运用贪心策略，但我们只需要简单的两次遍历即可：

1. 把所有孩子的糖果数初始化为 1；
2. 先从左往右遍历一遍，如果右边孩子的评分比左边的高，则右边孩子的糖果数更新为左边孩子的糖果数加 1；
3. 再从右往左遍历一遍，如果左边孩子的评分比右边的高，且左边孩子当前的糖果数不大于右边孩子的糖果数，则左边孩子的糖果数更新为右边孩子的糖果数加 1。

通过这两次遍历，分配的糖果就可以满足题目要求了。这里的贪心策略即为，在每次遍历中，只考虑并更新相邻一侧的大小关系。

```c++
#include <vector>
#include <iostream>
#include <numeric>

using namespace std;

class Solution {
public:
    static int candy(vector<int> &ratings) {
        int nums = ratings.size();
        if (nums < 2) {
            return nums;
        }

        // 初始化数组
        vector<int> candies(nums, 1);
        // 从左遍历
        for (int i = 0; i < nums - 1; i++) {
            if (ratings[i + 1] > ratings[i]) {
                candies[i + 1] = candies[i] + 1;
            }
        }
        // 从右遍历
        for (int i = nums - 1; i >= 1; i--) {
            if (ratings[i - 1] > ratings[i] && candies[i - 1] <= candies[i]) {
                candies[i - 1] = candies[i] + 1;
            }
        }

        return accumulate(candies.begin(), candies.end(), 0);
    }
};

int main() {
    vector<int> children = {1, 0, 2};
    int result = Solution::candy(children);
    cout << result << endl;
    return 0;
}
```

```python
def candy(ratings):
    lens = len(ratings)
    if(lens < 2):
        return lens

    # 将糖果数量列表每项值初始化为1
    num_list = [1 for _ in range(0, lens)]

    # 先从左往右遍历一遍
    for i in range(0, lens-1):
        # 如果右边孩子的评分比左边的高
        if(ratings[i+1] > ratings[i]):
            # 右边孩子的糖果数更新为左边孩子的糖果数加 1
            num_list[i+1] = num_list[i]+1

    # 再从右往左遍历一遍
    for j in range(lens-1, 0, -1):
        # 如果左边孩子的评分比右边的高
        # 且左边孩子当前的糖果数不大于右边孩子的糖果数
        if(ratings[j-1] > ratings[j] and num_list[j-1] <= num_list[j]):
            num_list[j-1] = num_list[j]+1

    num = 0
    # 求得总糖果数
    for k in num_list:
        num += k
    return num


print(candy([1, 0, 2]))
print(candy([1, 2, 2]))
print(candy([1, 3, 2, 2, 1]))
print(candy([1, 2]))
print(candy([1]))
```



 **<font color="#33a3dc">复杂度分析</font>**

时间复杂度：$O(n)$，其中 $n$ 是孩子的数量。我们需要遍历两次数组以分别计算满足左规则或右规则的最少糖果数量。

空间复杂度：$O(n)$，其中 $n$ 是孩子的数量。我们需要保存所有的左规则对应的糖果数量。

## 区间问题

### 无重叠区间

{% link 435. 无重叠区间, https://leetcode-cn.com/problems/non-overlapping-intervals/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}

给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

注意:

1. 可以认为区间的终点总是大于它的起点。
2. 区间 `[1,2]` 和 `[2,3]` 的边界相互“接触”，但没有相互重叠。

{% tabs 无重叠区间 %}
<!-- tab 示例 1 -->

输入：`[ [1,2], [2,3], [3,4], [1,3] ]`

输出：`1`

解释：移除 `[1,3]` 后，剩下的区间没有重叠。

<!-- endtab -->

<!-- tab 示例 2 -->

输入：`[ [1,2], [1,2], [1,2] ]`

输出：`2`

解释：你需要移除两个 `[1,2]` 来使剩下的区间没有重叠

<!-- endtab -->

<!-- tab 示例 3 -->

输入：`[ [1,2], [2,3] ]`

输出：`0`

解释：你不需要移除任何区间，因为它们已经是无重叠的了。

<!-- endtab -->
{% endtabs %}


在选择要保留区间时，区间的结尾十分重要：选择的区间结尾越小，余留给其它区间的空间就越大，就越能保留更多的区间。因此，我们采取的贪心策略为，优先保留结尾小且不相交的区间。

具体实现方法为，先把区间按照结尾的大小进行增序排序，每次选择结尾最小且和前一个选择的区间不重叠的区间。我们这里使用 C++ 的 Lambda，结合 `std::sort()` 函数进行自定义排序。

在样例中，排序后的数组为 `[[1,2], [1,3], [2,4]]`。按照我们的贪心策略：

1. 首先初始化为区间`[1,2]`；
2. 由于 `[1,3]` 与 `[1,2]` 相交，我们跳过该区间；
3. 由于 `[2,4]` 与 `[1,2]` 不相交，我们将其保留。
4. 因此最终保留的区间为 `[[1,2], [2,4]]`。

```c++
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;

class Solution {
public:
    static int eraseOverlapIntervals(vector <vector<int>> &intervals) {
        if (intervals.empty()) {
            return 0;
        }
        int n = intervals.size();
        sort(intervals.begin(), intervals.end(), [](vector<int> a, vector<int> b) {
            return a[1] < b[1];
        });
        int total = 0, prev = intervals[0][1];
        for (int i = 1; i < n; ++i) {
            if (intervals[i][0] < prev) {
                total++;
            } else{
                prev = intervals[i][1];
            }
        }
        return total;
    }
};

int main() {
    vector <vector<int>> test = {{1,2},{2,4},{1,3}};
    int result = Solution::eraseOverlapIntervals(test);
    cout << result << endl;
    return 0;
}
```

 **<font color="#33a3dc">复杂度分析</font>**

时间复杂度：$O(n \log n)$，其中 $n$ 是区间的数量。我们需要 $O(n \log n)$ 的时间对所有的区间按照右端点进行升序排序，并且需要 $O(n)$ 的时间进行遍历。由于前者在渐进意义下大于后者，因此总时间复杂度为 $O(n \log n)$。

空间复杂度：$O(\log n)$，即为排序需要使用的栈空间。



### 种花问题

{% link 605. 种花问题, https://leetcode-cn.com/problems/can-place-flowers/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}

<br/>

从一边开始遍历，当这个值为0且左右两边都为0时，将其变成1。最后将结果数组求和与原始值求和值相减即为最大满足值。

```c++
#include <vector>
#include <iostream>
#include <numeric>

using namespace std;

class Solution {
public:
    static bool canPlaceFlowers(vector<int> &flowerbed, int n) {
        // 当n为0时无论什么情况都为true
        if (n == 0) {
            return true;
        }
        int nums = flowerbed.size();
        // 在只有1个位置时，chu了n=0情况下，只有n为1且位置为空才为true
        if (nums == 1) {
            if (n == 1 && flowerbed[0] == 0) {
                return true;
            } else {
                return false;
            }
        }

        // 用于统计未加入花时的，花数目
        int before = accumulate(flowerbed.begin(), flowerbed.end(), 0);

        // 将[0,0,...]情况下时变成[1,0,...]
        if (flowerbed[0] == 0 && flowerbed[1] == 0) {
            flowerbed[0] = 1;
        }
        
        // 将[...,0,0,0,...]情况变成[...,0,1,0,...]
        for (int i = 1; i < flowerbed.size() - 1; ++i) {
            if (flowerbed[i] == 0 && flowerbed[i - 1] == 0 && flowerbed[i + 1] == 0) {
                flowerbed[i] = 1;
            }
        }

        // 将[...,0,0]情况变成[...,0,1]
        if (flowerbed[flowerbed.size() - 1] == 0 && flowerbed[flowerbed.size() - 2] == 0) {
            flowerbed[flowerbed.size() - 1] = 1;
        }

        //统计加入花后的所有花的数目
        int after = accumulate(flowerbed.begin(), flowerbed.end(), 0);
        // 当n小于,可以插入花的最大数目时返回true
        if ((after - before) >= n) {
            return true;
        } else {
            return false;
        }
    }
};

int main() {
    vector<int> test = {1, 0, 0, 0, 1};
    vector<int> test2 = {0, 0, 0, 0, 0};
    vector<int> test3 = {0};
    vector<int> test4 = {1};
    vector<int> test5 = {0, 0};
    bool result = Solution::canPlaceFlowers(test4, 0);
    cout << result << endl;
    return 0;
}
```

## 销售价值减少的颜色球【中等】

{% link 1648. 销售价值减少的颜色球, https://leetcode-cn.com/problems/sell-diminishing-valued-colored-balls/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}

你有一些球的库存 `inventory` ，里面包含着不同颜色的球。一个顾客想要<font color="#faa755">任意颜色</font>总数为 `orders` 的球。

这位顾客有一种特殊的方式衡量球的价值：每个球的价值是目前剩下的<font color="#faa755">同色球</font>的数目。比方说还剩下 6 个黄球，那么顾客买第一个黄球的时候该黄球的价值为 6。这笔交易以后，只剩下 5 个黄球了，所以下一个黄球的价值为 5 （也就是球的价值随着顾客购买同色球是递减的）。

给你整数数组 `inventory`，其中 `inventory[i]` 表示第 `i` 种颜色球一开始的数目。同时给你整数 `orders`，表示顾客总共想买的球数目。你可以按照任意顺序卖球。

请你返回卖了 `orders` 个球以后最大总价值之和。由于答案可能会很大，请你返回答案对 $10^{9} + 7$ 取余数 的结果。

{% tabs 销售价值减少的颜色球 %}
<!-- tab 示例 1 -->

输入：`inventory = [2,5]`，`orders = 4`

输出：14

解释：卖 1 个第一种颜色的球（价值为 2），卖 3 个第二种颜色的球（价值为 5 + 4 + 3）。最大总和为 2 + 5 + 4 + 3 = 14。

<!-- endtab -->

<!-- tab 示例 2 -->

输入：`inventory = [3,5]`，`orders = 6`

输出：19

解释：卖 2 个第一种颜色的球（价值为 3 + 2），卖 4 个第二种颜色的球（价值为 5 + 4 + 3 + 2）。最大总和为 3 + 2 + 5 + 4 + 3 + 2 = 19。
<!-- endtab -->

<!-- tab 示例 3 -->

输入：`inventory = [2,8,4,10,6]`，`orders = 20`

输出：110

<!-- endtab -->

<!-- tab 示例 4 -->

输入：`inventory = [1000000000]`，`orders = 1000000000`

输出：21

解释：卖 1000000000 次第一种颜色的球，总价值为 500000000。 500000000 对 $10^{9} + 7$ 取余为 21
<!-- endtab -->
{% endtabs %}

该题使用贪心算法，很容易想到，对数组进行从大到小排序，每次让值最大的元素加到 `result`，并使 `orders-1`。 

```python
def maxProfit(inventory, orders):
    i = 0
    result = 0
    # 数组元素只有一个时
    if len(inventory) == 1:
        while orders > 0:
            orders = orders-1
            result = result + inventory[0]
            inventory[0] = inventory[0] - 1
        return result % 1000000007
    # 数组元素有多个时
    while orders > 0 and i < len(inventory):
        if inventory[i] < inventory[i + 1]:
            inventory = sorted(inventory, reverse=True)
        result += inventory[i]
        inventory[i] = inventory[i] - 1
        orders = orders - 1
    return result % 1000000007


if __name__ == '__main__':
    print(maxProfit([2, 5], 4))  # 14
    print(maxProfit([3, 5], 6))  # 19
    print(maxProfit([2, 8, 4, 10, 6], 20))  # 110
    print(maxProfit([10], 10))  # 55
    print(maxProfit([1000000000], 1000000000))  # 21
    print(maxProfit([773160767], 252264991))  # 70267492
```

但题目中给的数据比较大，如果不进行优化会超时，[优化思路](https://leetcode-cn.com/problems/sell-diminishing-valued-colored-balls/solution/liang-chong-si-lu-you-hua-tan-xin-suan-fa-you-hua-/)

不需要一次一次的模拟，而是一次性买入一定数量的球，直至该球数量等于至第二多数量。对于示例 3：`inventory = [2,8,4,10,6]`，`orders = 20` 而言：

首先，从大到小排序。`[10, 8, 6, 4, 2]`，然后逐步模拟：

+ `[10, 8, 6, 4, 2]`，`orders = 20` 数量最多的同色球的数量为 10，第二多的为 8，颜色数为 1。此时我们可以销售第一个颜色的球 2 次，获利 10 + 9 = 19。
+ `[8, 8, 6, 4, 2]`，`orders = 18` 数量最多的同色球的数量为 8，第二多（与第一不同）的为 6，颜色数为 2。此时我们可以销售 2 × 2 = 4 次，获利 (8 + 7) × 2 = 30。
+ `[6, 6, 6, 4, 2]`，`orders = 14` 数量最多的同色球的数量为 6，第二多（与第一不同）的为 4，颜色数为 3。此时我们可以销售 2 × 3 = 6 次，获利 (6 + 5) × 3 = 33。
+ `[4, 4, 4, 4, 2]`，`orders = 8` 数量最多的同色球的数量为 4，第二多（与第一不同）的为 2，颜色数 = 4。此时我们可以全部卖完，销售 2 × 4 次，获利（4 + 3）× 4 = 28。
+ 总计收入为 19 + 30 + 33 + 28 = 110.

```python
def maxProfit(inventory, orders):
    inventory = sorted(inventory, reverse=True)
    res = 0
    i = 0
    mod = 1e9+7
    while orders > 0:
        # 找到第二多元素的索引
        while i < len(inventory) and inventory[i] >= inventory[0]:
            i = i + 1
        # 将第二多元素赋值到 nextEle
        nextEle = 0
        if i < len(inventory):
            nextEle = inventory[i]
        # 具有相同个数的元素有多少个
        bucks = i
        # 当前最多元素与第二多元素个数之差
        delta = inventory[0] - nextEle
        # 最多可以一次性销售多少次
        rem = bucks * delta
        # 一次性销售次数大于卖的个数
        if rem > orders:
            dec = orders // bucks
            a1 = inventory[0] - dec + 1
            an = inventory[0]
            res = res + ((((a1 + an) * dec) // 2) * bucks)
            res = res + ((inventory[0] - dec) * (orders % bucks))
        # 一次性销售次数小于卖的个数
        else:
            # 可以卖出的最低价格
            a1 = nextEle + 1
            # 可以卖出的最高价格
            an = inventory[0]
            # 等差数列求和
            res = res + ((((a1 + an) * delta) // 2) * bucks)
            inventory[0] = nextEle
        # orders 减去一次性买入个数
        orders = orders - rem
        # 前取模防止溢出
        res = res % mod
    return int(res)
```

 **<font color="#33a3dc">时间复杂度分析</font>**：$n \log(n)$