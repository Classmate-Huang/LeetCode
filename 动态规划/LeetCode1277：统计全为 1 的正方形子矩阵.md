# LeetCode1277：统计全为 1 的正方形子矩阵

## 题目

给你一个 `m * n` 的矩阵，矩阵中的元素不是 `0` 就是 `1`，请你统计并返回其中完全由 `1` 组成的 **正方形** 子矩阵的个数。

 

**示例 1：**

```
输入：matrix =
[
  [0,1,1,1],
  [1,1,1,1],
  [0,1,1,1]
]
输出：15
解释： 
边长为 1 的正方形有 10 个。
边长为 2 的正方形有 4 个。
边长为 3 的正方形有 1 个。
正方形的总数 = 10 + 4 + 1 = 15.
```

**示例 2：**

```
输入：matrix = 
[
  [1,0,1],
  [1,1,0],
  [1,1,0]
]
输出：7
解释：
边长为 1 的正方形有 6 个。 
边长为 2 的正方形有 1 个。
正方形的总数 = 6 + 1 = 7.
```

 

**提示：**

- `1 <= arr.length <= 300`
- `1 <= arr[0].length <= 300`
- `0 <= arr[i][j] <= 1`

## 解法一：DP

思路和221题一模一样（详见221题），找到以每个位置为右下角的正方形最大边长`f[i][j]`，**这也是以该节点为右下角的正方形子矩阵个数（边长=子矩阵个数）**。代码如下：

```c++
class Solution {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size(), ans = 0;
        vector<vector<int>> f(m, vector<int>(n, 0));
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(matrix[i][j]==1){
                    int left = j>0 ? f[i][j-1] : 0;
                    int up = i>0 ? f[i-1][j] : 0;
                    int corner = (i>0)&&(j>0) ? f[i-1][j-1] : 0;
                    f[i][j] = min(left, min(up, corner))+1;
                    ans += f[i][j];
                }
            }
        }
        return ans;
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(MN)