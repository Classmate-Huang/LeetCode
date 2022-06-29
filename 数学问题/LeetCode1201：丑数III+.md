# LeetCode1201：丑数III

## 题目

给你四个整数：n 、a 、b 、c ，请你设计一个算法来找出第 n 个丑数。

丑数是可以被 a 或 b 或 c 整除的 正整数 。

 

示例 1：

```
输入：n = 3, a = 2, b = 3, c = 5
输出：4
解释：丑数序列为 2, 3, 4, 5, 6, 8, 9, 10... 其中第 3 个是 4。
```

示例 2：

```
输入：n = 4, a = 2, b = 3, c = 4
输出：6
解释：丑数序列为 2, 3, 4, 6, 8, 9, 10, 12... 其中第 4 个是 6。
```

示例 3：

```
输入：n = 5, a = 2, b = 11, c = 13
输出：10
解释：丑数序列为 2, 4, 6, 8, 10, 11, 12, 13... 其中第 5 个是 10。
```

示例 4：

```
输入：n = 1000000000, a = 2, b = 217983653, c = 336916467
输出：1999999984
```


提示：

```
1 <= n, a, b, c <= 10^9
1 <= a * b * c <= 10^18
本题结果在 [1, 2 * 10^9] 的范围内
```

## 解法一：容斥原理 + 二分

这个题目和"第N个神奇数字"的解法一致，只不过从能被两个数整除换成了三个数整除。

给定一个数`x`，判断`<=x`的数中有多少个能被`a,b,c`整除的丑数可以利用容斥原理：**能被a整除 + 能被b整除 + 能被c整除 - 能被a,b整除 - 能被a,c整除 - 能被b,c整除 + 能被a,b,c整除**。

<img src="https://bkimg.cdn.bcebos.com/pic/503d269759ee3d6d55fb0fef1d5c7a224f4a20a470f6?x-bce-process=image/resize,m_lfit,w_536,limit_1/format,f_jpg" alt="容斥原理" style="zoom: 50%;" />

现在能够在O(1)时间判断f(x) - “给定x，找到`<=x`的数中有多少个能被`a,b,c`整除的丑数”，并且随着x的增大，f(x)也随之增大，所以可以利用二分法进行搜索，代码如下：

```c++
class Solution {
public:
    long long Lab, Lbc, Lac, Labc;	// ab公倍数 bc公倍数 ac公倍数 abc公倍数
    
    long long gcd(long long x, long long y) {	// 最大公因数
        return x % y == 0 ? y : gcd(y, x % y);
    }
    
    long long lcm(long long x, long long y) {	// 最小公倍数
        return x * y / gcd(x, y);
    }
    // <=x 的数中存在多少个能被a/b/c整除的丑数
    long long count(long long x, long long a, long long b, long long c) {
        return x / a + x / b + x / c - x / Lab - x / Lbc - x / Lac + x / Labc;
    }
    
    int nthUglyNumber(int n, int a, int b, int c) {
        long long l = 1, r = 2e9;
        long long la = a, lb = b, lc = c;
        // 求解最小公倍数
        Lab = lcm(la, lb), Lbc = lcm(lb, lc), Lac = lcm(la, lc);
        Labc = lcm(Lab, lc);
        
        while (l < r) {	// 二分
            long long mid = (r - l) / 2 + l;
            if (count(mid, la, lb, lc) < n)    l = mid + 1;
            else r = mid;
        }
        
        return l;
    }
};
```

时间复杂度：O(logn)

空间复杂度：O(1)