# LeetCode70：爬楼梯

## 题目

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。

1.  1 阶 + 1 阶
2.  2 阶
```



示例 2：

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。

1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```



来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/climbing-stairs
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 解法一：DP

最简单的动态规划题：每次只能跨一/两个台阶，那么最后一步就有两种情况，f[n]=f[n-1] + f[n-2]。代码如下：

```c++
class Solution {
public:
    int climbStairs(int n) {
        vector<int> f(n+5, 0);
        f[1] = 1; f[2] = 2;
        for(int i=3; i<=n; i++){
            f[i] = f[i-1] + f[i-2];
        }
        return f[n];
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

> 这是最简单的做法，也可以利用递归关系式与矩阵快速幂运算求解，有兴趣请看[官方题解](https://leetcode-cn.com/problems/climbing-stairs/solution/pa-lou-ti-by-leetcode-solution/)

## 解法二：DP+空间压缩

分析状态转移方程`f[n]=f[n-1] + f[n-2]`，在求解后续问题时只与两个子问题有关，因此可以使用变量存储递归这两个子问题的解，从而节省空间复杂度。代码如下：

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n<3) return n;
        // pre2表示f[n-2], pre1表示f[n-1]
        int pre2 = 1, pre1 = 2, ans = 0;
        for(int i=3; i<=n; i++){
            ans = pre1 + pre2;
            pre2 = pre1; pre1 = ans;
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)