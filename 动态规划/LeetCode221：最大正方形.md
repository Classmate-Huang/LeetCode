# LeetCode221：最大正方形

## 题目

在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/26/max1grid.jpg)

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：4
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/11/26/max2grid.jpg)

```
输入：matrix = [["0","1"],["1","0"]]
输出：1
```

**示例 3：**

```
输入：matrix = [["0"]]
输出：0
```

 

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 300`
- `matrix[i][j]` 为 `'0'` 或 `'1'`

## 解法一：DP

考虑子问题为**求当前位置为右下角坐标的最大正方形边长`f[i][j]`**，先说结论，状态转移方程为：`f[i][j]=min(f[i-1][j],f[i][j-1],f[i-1][j-1]) + 1`，图来自1277题的[官方题解](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/solution/tong-ji-quan-wei-1-de-zheng-fang-xing-zi-ju-zhen-2/)。原因如图所示，不懂可以参照官方题解。

① 从下图可得出结论： `min(f[i-1][j],f[i-1][j-1],f[i][j-1) ≥ f[i][j]-1`

![1277-1.png](https://pic.leetcode-cn.com/14aa58be2ea5c9b36a722db76d2e843c4c909e312223a8461a3d2d93bc734b42-1277-1.png)

② 从下图可得出结论： `f[i][j] ≥ min(f[i-1][j],f[i-1][j-1],f[i][j-1)+1`

![1277-2.png](https://pic.leetcode-cn.com/dfcaa5390732f1b9d1d9c71f696957cbc91904c52e23705a061bae727228126d-1277-2.png)

根据①②可取等号。

有了转移方程，DP的题就很好解了，只要注意下边界情况就行。代码如下：

```c++
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size(), n = matrix[0].size(), ans = 0;
        vector<vector<int>> f(m, vector<int>(n, 0));
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(matrix[i][j]=='0'){
                    f[i][j] = 0;
                    continue;
                }
                
                int left = j>0 ? f[i][j-1] : 0;
                int up = i>0 ? f[i-1][j] : 0;
                int corner = (i>0)&&(j>0) ? f[i-1][j-1] : 0;
                f[i][j] = min(left, min(up, corner)) + 1;
                
                ans = max(ans, f[i][j]);
            }
        }

        return ans*ans;
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(MN)

