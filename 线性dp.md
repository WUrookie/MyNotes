### 线性$dp$

****

> 线性$dp$顾名思义状态转移方程是线性的，典型的有  $LCS$（最长公共子序列）  $LIS$（最长上升子序列）问题。
>
> 好好研究一下可以获得许多线性$dp$ 的状态定义方式。 

#### 题目

##### 1.[最大子数组和.LeetCode](https://leetcode-cn.com/problems/maximum-subarray/)

> 给你一个整数数组 $nums$ ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
>
> 子数组 是数组中的一个连续部分。
>
>  
>
> 示例 1：
>
> 输入：$nums = [-2,1,-3,4,-1,2,1,-5,4]$
> 输出：6
> 解释：连续子数组 $[4,-1,2,1] $的和最大，为 6 。
> 示例 2：
>
> 输入：$nums = [1]$
> 输出：$1$
> 示例 3：
>
> 输入：$nums = [5,4,-1,7,8]$
> 输出：$23$
>
>
> 提示：
>
> $1 <= nums.length <= 10^5$
> $-10^4 <= nums[i] <= 10^4$

`分析`：这道题算是入门题。

状态定义：$f[i]$表示以$nums[i]$结尾的连续子数组的最大值

状态计算：因为是连续的子数组，所以对于$nums[i]$而言，其本事是一定被包括的，所以就考虑前一段的长度，如果不为$0$，那么一定以$nums[i - 1]$结尾，如果为$0$,就直接计算。

得到转移方程：$f[i] = max(f[i - 1],0) + nums[i]$

``` c++
const int N = 1E5 + 10;
class Solution {
public:
    int f[N];
    int maxSubArray(vector<int>& nums) {
        memset(f, 0, sizeof f);
        int n = nums.size();
        int ans = -1e9;
        for(int i = 1; i <= n; i ++ )
        {
             f[i] = max(f[i - 1], 0) + nums[i - 1];
             ans = max(ans, f[i]);
        }
        return ans;
    }
};
```

##### 2.[]()
