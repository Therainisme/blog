---
layout: default
title: "Basic | 动态规划"
description: "记住你之前做过的事"
---

### 什么是动态规划

![Quora](https://mmbiz.qpic.cn/mmbiz_png/D67peceibeIT85Ko9IG4Wfk4fNLFQLUru54PZlj8kiaMziaXGRXbEfDApPTTcyibfsB2MgopKnLFricPXdiaxxkjtQOQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

动态规划就是，记住你之前做过的事

> 我们每个人在备战高考的时候，都是在解决动态规划问题

在枯燥的刷题过程中，我们经常会遇到一些不太懂的问题，我们正常的选择是问老师或者问同学，然后把它记到错题本上。

过了一段时间，如果你又遇到了这样的一个问题，而这个问题和上次遇到的很相像。但是呢，你已经记不起来了。这时候你可以选择翻之前的错题本，或者是再去问老师或者问同学

> 翻错题本这个动作，就是回顾我们之前做过的事情，也就是动态规划

翻错题本可能会花费一点时间，但是问老师或者同学花费的时间更多

### 解动态规划题的步骤

> 找到问题之间的联系

1+1+1+1+1+1+1+1得出的答案是8

快速计算1+1+1+1+1+1+1+1+1，可以看成是8+1

也就是说 [ 当前这个问题的答案 ] = [ 上一个问题的答案 ] + 1

> 递推方程的推导

我们可以设一个数组`dp[i] = res`，表示在`1`加`i`此的情况下所得的答案为`res`

那么这道题的递推方程就是`dp[i] = dp[i-1] + 1`

### 例1 爬楼梯

LeetCode.70

假设你正在爬楼梯。需要`n`阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定`n`是一个正整数。

```
输入：2
输出：2
解释： 有两种方法可以爬到楼顶。

1. 1 阶 + 1 阶
2. 2 阶
```
```
输入：3
输出：3
解释： 有三种方法可以爬到楼顶。

1. 1 阶 + 1 阶 + 1 阶
2. 1 阶 + 2 阶
3. 2 阶 + 1 阶
```

#### 现在开始分析

爬楼梯可以看成走一步或者走两步，那么它就是有两种状态

> 走一步

> 走两步

先设定一个数组`dp[i] = res`，`dp[]`初始化为0，`dp[0] = 0, dp[1] = 1`

表示在第`i`个阶梯上，走到这个阶梯的方案数`res`

假如说我要走到第`i`个阶梯，那么我可以从`i - 1`上的阶梯上走过来，也可也从`i - 2`上的阶梯走过来

假设每次只能走一步的时候，永远只有一种方案数`dp[i] += dp[i - 1]`，最终答案等于`1`

当我走到第`i`个阶梯，我要么从`dp[i - 1]`走过来，要么从`dp[i - 2]`

如果我只从`dp[i - 1]`走过来，那么`dp[i] += dp[i - 1]`，方案数不变。

但是我又要从`dp[i - 2]`走过来，那么`dp[i] += dp[i - 2]`，方案数在之前的基础上又变动了

> 即`dp[i] = dp[i - 1] + dp[i - 2]`

参考代码：
```c++
class Solution {
   public:
    int climbStairs(int n) {
        //初始化数组
        int* dp = new int[n + 10];
        memset(dp, 0, sizeof(dp));

        dp[0] = 1;
        for (int i = 1; i <= n; ++i) {
            dp[i] += dp[i - 1];
            if (i > 1) dp[i] += dp[i - 2]; // 这里是为了防止数组越界
        }
        return dp[n];
    }
};
```

### 例2 三角形最小路径和

LeetCode.120

给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

例如，给定三角形：

```c++
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

#### 解题思路：

> 自顶向下越界问题很麻烦，建议直接自低向上

设`dp[i][j] = res`表示在第`i`行`j`列上，最小的路径和是`res`

> 先把最后一层赋值

```c++
for (int i = 0; i < triangle[high].size(); ++i)
    dp[high][i] = triangle[high][i];
```

> 从倒数第二层向第一层遍历，从下往上走，要么从`dp[i + 1][j]`来，要么从`dp[i + 1][j + 1]`来，可以直接通过`dp[i][j] = min(x, y)`函数来选取最小路径的那个值，然后再加上当前三角形的路径`+ triangle[i][j]`

```c++
for (int i = triangle.size() - 2; i >= 0; --i) {
    for (int j = 0; j < triangle[i].size(); ++j) {
        dp[i][j] = min(dp[i + 1][j], dp[i + 1][j + 1]) + triangle[i][j];
    }
}
```

参考代码：
```c++
class Solution {
   public:
    int minimumTotal(vector<vector<int>>& triangle) {
        // 初始化数组，这样写暴力一点
        const int N = 1000;
        int dp[N][N];
        memset(dp, 0, sizeof(dp));
        
        int high = triangle.size() - 1;

        for (int i = 0; i < triangle[high].size(); ++i)
            dp[high][i] = triangle[high][i];

        for (int i = triangle.size() - 2; i >= 0; --i) {
            for (int j = 0; j < triangle[i].size(); ++j) {
                dp[i][j] = min(dp[i + 1][j], dp[i + 1][j + 1]) + triangle[i][j];
            }
        }
        return dp[0][0];
    }
};
```

### 例3 最大子序和

LeetCode.53

给定一个整数数组`nums`，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例：
```
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

进阶:

如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

#### 解题思路：

设`dp[i] = res`，为在遍历到第`i`这个数时，前面的`0 ~ i-1`的子序列的最大值

> 序列必须是连续的，如果遍历到`nums[i]`这个数，前面连续的序列之和加上了`nums[i]`，还没有`nums[i]`它自己大，那么数列就从头开始即可

> 即`dp[i] = max(dp[i - 1] + nums[i], nums[i]);`

```c++
class Solution {
   public:
    int maxSubArray(vector<int>& nums) {
        // 初始化数组
        int* dp = new int[nums.size() + 10];
        memset(dp, 0, sizeof(dp));

        int res = 0;

        for (int i = 0; i < nums.size(); ++i) {
            // 防止数组越界
            if (i == 0) {
                dp[i] = nums[0];
                res = dp[i];
                continue;
            }
            // 正常动态规划
            dp[i] = max(dp[i - 1] + nums[i], nums[i]);
            res = max(res, dp[i]);
        }

        return res;
    }
};
```