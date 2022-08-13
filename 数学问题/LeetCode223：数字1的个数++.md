# LeetCode223：数字1的个数

## 题目

给定一个整数 n，计算所有小于等于 n 的非负整数中数字 1 出现的个数。

 

示例 1：

```
输入：n = 13
输出：6
```

示例 2：

```
输入：n = 0
输出：0
```

提示：

```
0 <= n <= 109
```

## 解法一：模拟 + 分类讨论

建议直接看[官方题解](https://leetcode.cn/problems/number-of-digit-one/solution/shu-zi-1-de-ge-shu-by-leetcode-solution-zopq/)，写的比较好，总体思路就是采用**模拟+分类讨论**。

其核心思想在于: 分别考虑每个位置上的数为1时，`1~n`范围内能出现的数字个数。

代码如下：

```c++
class Solution {
public:
    int countDigitOne(int n) {
        long long base = 1;
        int ans = 0;
        while (n >= base) {
            ans += (n/(base*10)*base) + min(base, max(0LL, n%(base*10) - base + 1));
            base *= 10;
        }
        return ans;
    }
};
```

时间复杂度：O(logn)

空间复杂度：O(1)