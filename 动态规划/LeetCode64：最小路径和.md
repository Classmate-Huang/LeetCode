# LeetCode64：最小路径和

## 题目

给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**示例 2：**

```
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

 

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 200`
- `0 <= grid[i][j] <= 100`

## 解法一：DP

对于通往某个结点的路径只有两条，一条从左结点过来，一条从上结点过来（除左上角结点以外），因此状态转移方程为`f[i]=min(f[i-1]f[j], f[i][j-1])+grid[i][j]`，但是实现时要注意细节。代码如下：

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> f(m, vector<int>(n));
        
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(!i && !j){
                    f[i][j] = grid[i][j];
                    continue;
                }
                int up = i>0?f[i-1][j]:INT_MAX;
                int left = j>0?f[i][j-1]:INT_MAX;
                f[i][j] = grid[i][j] + min(left, up);
            }
        }

        return f[m-1][n-1];
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(MN)

## 解法二：DP+空间压缩

状态转移方程只与上下两行的结果有关，因此可以采用空间压缩。代码如下（注意细节）：

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<int> f(n);
        
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(!i && !j){
                    f[j] = grid[i][j];
                    continue;
                }
                int up = i>0?f[j]:INT_MAX;
                int left = j>0?f[j-1]:INT_MAX;
                f[j] = grid[i][j] + min(left, up);
            }
        }

        return f[n-1];
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(N)