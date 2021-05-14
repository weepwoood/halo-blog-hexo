---
title: 贪心算法
date: 2021-05-14 18:56:51
description: LeetCode刷题之贪心算法
# top_img: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面顶部图片
# cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面缩略图
mathjax: true
#katex: true
large: true
bilibili_banner: spring
categories: LeetCode
tags: 
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
    static int findContentChildren(vector<int> &g, vector<int> &s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int child = 0, cookie = 0;
        while (child < g.size() && cookie < s.size()) {
            if (g[child] <= s[cookie]) {
                child++;
            }
            cookie++;
        }
        return child;
    }
};

int main() {
    vector<int> children = {1, 2};
    vector<int> cookies = {1, 2, 3};
    int result = Solution::findContentChildren(children, children);
    cout << result << endl;
}
```

 **<font color="#33a3dc">复杂度分析</font>**

时间复杂度：$O(m \log m + n \log n$)，其中 $m$ 和 $n$ 分别是数组 $g$ 和 $s$ 的长度。对两个数组排序的时间复杂度是 $O(m \log m + n \log n)$，遍历数组的时间复杂度是 $O(m+n)$，因此总时间复杂度是 $O(m \log m + n \log n)$。

空间复杂度：$O(\log m + \log n)$，其中 $m$ 和 $n$ 分别是数组 $g$ 和 $s$ 的长度。空间复杂度主要是排序的额外空间开销。

### 分发糖果


{% link 135. 分发糖果, https://leetcode-cn.com/problems/candy/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}


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

 **<font color="#33a3dc">复杂度分析</font>**

时间复杂度：$O(n)$，其中 $n$ 是孩子的数量。我们需要遍历两次数组以分别计算满足左规则或右规则的最少糖果数量。

空间复杂度：$O(n)$，其中 $n$ 是孩子的数量。我们需要保存所有的左规则对应的糖果数量。

## 区间问题

### 无重叠区间

{% link 435. 无重叠区间, https://leetcode-cn.com/problems/non-overlapping-intervals/, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/leetcodeicon.svg%}

<br/>

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


 **<font color="#33a3dc">复杂度分析</font>**

