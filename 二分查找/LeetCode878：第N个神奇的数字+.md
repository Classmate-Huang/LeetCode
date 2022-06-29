# LeetCode878：第N个神奇的数字

## 题目

一个正整数如果能被 a 或 b 整除，那么它是神奇的。

给定三个整数 n , a , b ，返回第 n 个神奇的数字。因为答案可能很大，所以返回答案 对 109 + 7 取模 后的值。

 

示例 1：

```
输入：n = 1, a = 2, b = 3
输出：2
```

示例 2：

```
输入：n = 4, a = 2, b = 3
输出：6
```


提示：

```
1 <= n <= 109
2 <= a, b <= 4 * 104
```

## 解法一：二分法

首先看到这个数据范围，我们就应该想到用二分法来做。此外，也可以发现对于某个数x，f(x)的求解是递增的【f(x)表示x之前有多少个神奇数字】，所以采用二分法是可行的。

给定正整数`n`和正整数`k`，判断`<=n`的正整数中能被`k`整除的有多少个，只需要`n/k`即可。对于本题，给定`x`和`a,b`，`<=x`的神奇数字个数就是`x/a + x/b - x/lcm(a,b)`，`lcm()`表示最小公倍数。

```c++
class Solution {
public:
    int mod = 1e9 + 7;
    
    int gcd(int a, int b) {
        return a % b == 0 ? b : gcd(b, a%b);
    }
    
    int lcm(int a, int b) {
        return a * b / gcd(a, b);
    }
    
    int nthMagicalNumber(int n, int a, int b) {
        int c = lcm(a, b);  // 最小公倍数
        
        long long l = 1, r = 1e15;
        while (l < r) {	// 二分
            long long mid = (r - l) / 2 + l;
            // 统计 <=mid 的神奇数字个数
            long long cnt = mid / a + mid / b - mid / c;
            if (cnt < n)    l = mid + 1;
            else    r = mid;
        }
        
        return int(l % mod);
    }
};
```

时间复杂度：O(logn)

空间复杂度：O(1)