# LeetCode1411：给Nx3网格图涂色的方案数

## 题目

你有一个 n x 3 的网格图 grid ，你需要用 红，黄，绿 三种颜色之一给每一个格子上色，且确保相邻格子颜色不同（也就是有相同水平边或者垂直边的格子颜色不同）。

给你网格图的行数 n 。

请你返回给 grid 涂色的方案数。由于答案可能会非常大，请你返回答案对 10^9 + 7 取余的结果。

 

示例 1：

```
输入：n = 1
输出：12
解释：总共有 12 种可行的方法：
```

示例 2：

```
输入：n = 2
输出：54
```

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/04/12/e1.png" alt="img" style="zoom:67%;" />

示例 3：

```
输入：n = 3
输出：246
```

示例 4：

```
输入：n = 7
输出：106494
```

示例 5：

```
输入：n = 5000
输出：30228214
```


提示：

```
n == grid.length
grid[i].length == 3
1 <= n <= 5000
```

## 解法一：动态规划

由于每一行只有3个网格，**当前网格只受到上一层网格的影响**，根据示例1的提示，每一层的有效状态数只有12种，因此可以直接编码：让每个状态[**0~12**]对应到每种可行的涂色方案上（详见代码）。我们可以确定边界条件，只有一行的时候，其可行解能够确定，继续往下遍历。

当前有12种状态，分别判断每种状态与其相容的状态有多少种，记录每种相容状态的次数，最后统计所有结果即可，代码如下：

```c++
class Solution {
public:
    // 状态编码 每个状态对应一种颜色方式
    vector<vector<int>> states = {{0, 1, 2}, {0, 2, 1}, {0, 1, 0}, {0, 2, 0},
                                  {1, 0, 2}, {1, 2, 0}, {1, 0, 1}, {1, 2, 1},
                                  {2, 0, 1}, {2, 1, 0}, {2, 0, 2}, {2, 1, 2}};

    int Num = 1e9 + 7;

    bool isOK(vector<int>& a1, vector<int>& a2){
        // 判断两种状态是否相容
        for(int i=0; i<3; i++){
            if(a2[i] == a1[i])  return false;
        }
        return true;
    }

    int numOfWays(int n) {
        vector<vector<int>> f(n+1, vector<int>(12, 0));
        // 初始状态
        for(int k=0; k<12; k++) f[1][k] = 1;
        // 往下递归
        for(int i=2; i<=n; i++){
            for(int k=0; k<12; k++){
                for(int lastK=0; lastK<12; lastK++){
                    if(isOK(states[lastK], states[k]))  f[i][k] = (f[i][k] + f[i-1][lastK]) % Num;
                }
            }
        }
        
        int ans = 0;
        for(int k=0; k<12; k++) ans = (ans + f[n][k]) % Num;
        return ans;
    }
};
```

时间复杂度：O(n^2)

空间复杂度：O(n)

