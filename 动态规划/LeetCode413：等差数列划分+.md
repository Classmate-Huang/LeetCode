# Leetcode413：等差数列划分

## 题目

如果一个数列至少有三个元素，并且任意两个相邻元素之差相同，则称该数列为等差数列。

例如，以下数列为等差数列:

```
1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9
```

以下数列不是等差数列。

```
1, 1, 2, 5, 7
```

 

数组 A 包含 N 个数，且索引从0开始。数组 A 的一个子数组划分为数组 (P, Q)，P 与 Q 是整数且满足 0<=P<Q<N 。

如果满足以下条件，则称子数组(P, Q)为等差数组：

元素 A[P], A[p + 1], ..., A[Q - 1], A[Q] 是等差的。并且 P + 1 < Q 。

函数要返回数组 A 中所有为等差数组的子数组个数。

 

**示例:**

```
A = [1, 2, 3, 4]

返回: 3, A 中有三个子等差数组: [1, 2, 3], [2, 3, 4] 以及自身 [1, 2, 3, 4]。
```

## 解法一：DP

想直接求解最终问题不太好操作，不太好写出状态转移方程。考虑求解**以每个位置结尾的等差数列的子数组个数**，如果满足等差数组条件，那么**当前位置上的子数组个数等于前一个位置上的子数组个数+1**。写出状态转移方程，当`nums[i]-nums[i-1]==nums[i-1]-nums[i-2]`条件成立时，`f[i]=f[i-1]+1`状态转移成立。代码如下：

```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int n = nums.size();
        if(n<3) return 0;

        vector<int> f(n+2, 0);
        for(int i=2; i<n; i++){
            if(nums[i]-nums[i-1]==nums[i-1]-nums[i-2]){
                f[i] = f[i-1] + 1;
            }
        }

        return accumulate(f.begin(), f.end(), 0);
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：DP+空间压缩

使用空间压缩，代码如下：

```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int n = nums.size();
        if(n<3) return 0;

        int ans = 0, pre = 0;
        for(int i=2; i<n; i++){
            if(nums[i]-nums[i-1]==nums[i-1]-nums[i-2]){
                pre = pre + 1;
                ans += pre;
            }
            else 
                pre = 0;
        }

        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)