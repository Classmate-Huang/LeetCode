# LeetCode688：“骑士”在棋盘上的概率

## 题目

在一个 `n x n` 的国际象棋棋盘上，一个骑士从单元格 `(row, column)` 开始，并尝试进行 `k` 次移动。行和列是 **从 0 开始** 的，所以左上单元格是 `(0,0)` ，右下单元格是 `(n - 1, n - 1)` 。

象棋骑士有8种可能的走法，如下图所示。每次移动在基本方向上是两个单元格，然后在正交方向上是一个单元格。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/knight.png)

每次骑士要移动时，它都会随机从8种可能的移动中选择一种(即使棋子会离开棋盘)，然后移动到那里。

骑士继续移动，直到它走了 `k` 步或离开了棋盘。

返回 *骑士在棋盘停止移动后仍留在棋盘上的概率* 。

 

**示例 1：**

```
输入: n = 3, k = 2, row = 0, column = 0
输出: 0.0625
解释: 有两步(到(1,2)，(2,1))可以让骑士留在棋盘上。
在每一个位置上，也有两种移动可以让骑士留在棋盘上。
骑士留在棋盘上的总概率是0.0625。
```

**示例 2：**

```
输入: n = 1, k = 0, row = 0, column = 0
输出: 1.00000
```

 

**提示:**

- `1 <= n <= 25`
- `0 <= k <= 100`
- `0 <= row, column <= n`

## 解法一：记忆化搜索

首先整个题目有两种主要思路，一种是求出所有的可行路径，然后除以$8^k$，而另一种是每一步都求出对应的概率，进行累积，这里的代码采用第二种思路。

这个题目一种很常规的思路就是执行搜索，`BFS` or `DFS`遍历完所有情况，但这种**概率累积最好是采用DFS**，因为BFS在处理上需要注意很多细节。但直接暴力搜索会导致超时，这是因为会出现很多重复的情况。因此构造一个数组`f[n][n][k]`，让其存储每个点在k步移动范围仍停留在棋盘上的概率。

代码如下：

```c++
class Solution {
public:
    vector<vector<vector<double>>> f;

    vector<vector<int>> offset = {
        {1, 2}, {1, -2}, {-1, 2}, {-1, -2},
        {2, 1}, {2, -1}, {-2, 1}, {-2, -1}};

    // 记忆化搜索
    double dfs(int x, int y, int n, int k){
        // 超出棋盘界限
        if(x<0 || x>=n || y<0 || y>=n)  return 0.;
        // 原地不动
        if(k == 0)  return 1.;
        // 已存在答案
        if(f[x][y][k])  return f[x][y][k];
        // 未遍历过
        for(auto& t:offset){
            f[x][y][k] += 1./8 * dfs(x+t[0], y+t[1], n, k-1);
        }
        return f[x][y][k];

    }

    double knightProbability(int n, int k, int row, int column) {
        f.resize(n, vector<vector<double>>(n, vector<double>(k+1, 0)));
        return dfs(row, column, n, k);
    }
};
```

时间复杂度：O($n*n*k$)

空间复杂度：O($n*n*k$)

## 解法二：动态规划

有了记忆化搜索的思路，很容易就能将其转换为动态规划代码。实现如下：

```c++
class Solution {
public:
    vector<vector<int>> offset = {
        {1, 2}, {1, -2}, {-1, 2}, {-1, -2},
        {2, 1}, {2, -1}, {-2, 1}, {-2, -1}};

    double knightProbability(int n, int k, int row, int column) {
        vector<vector<vector<double>>> f(n, vector<vector<double>>(n, vector<double>(k+1, 0)));
        // 最外层循环为k
        for(int step=0; step<=k; step++){
            for(int i=0; i<n; i++){
                for(int j=0; j<n; j++){
                    if(step==0){
                        f[i][j][step] = 1;
                        continue;
                    }
                    for(auto& t:offset){
                        int x = i+t[0], y = j+t[1];
                        if(x>=0 && x<n && y>=0 && y<n){
                            f[i][j][step] += 1./8 * f[x][y][step-1];
                        }
                    }
                }
            }
        }

        return f[row][column][k];
    }
};
```

时间复杂度：O($n*n*k$)

空间复杂度：O($n*n*k$)

> 动态规划只依赖前一个状态的值，可以优化到O(n^2)，不过这题k值较小，必要性不是很大
