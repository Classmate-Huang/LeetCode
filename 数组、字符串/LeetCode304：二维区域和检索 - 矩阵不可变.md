# LeetCode304：二维区域和检索 - 矩阵不可变

## 题目

给定一个二维矩阵 `matrix`，以下类型的多个请求：

- 计算其子矩形范围内元素的总和，该子矩阵的 **左上角** 为 `(row1, col1)` ，**右下角** 为 `(row2, col2)` 。

实现 `NumMatrix` 类：

- `NumMatrix(int[][] matrix)` 给定整数矩阵 `matrix` 进行初始化
- `int sumRegion(int row1, int col1, int row2, int col2)` 返回 **左上角** `(row1, col1)` 、**右下角** `(row2, col2)` 所描述的子矩阵的元素 **总和** 。

 

**示例 1：**

![img](LeetCode304：二维区域和检索 - 矩阵不可变.assets/1626332422-wUpUHT-image.png)

```
输入: 
["NumMatrix","sumRegion","sumRegion","sumRegion"]
[[[[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]],[2,1,4,3],[1,1,2,2],[1,2,2,4]]
输出: 
[null, 8, 11, 12]

解释:
NumMatrix numMatrix = new NumMatrix([[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]]);
numMatrix.sumRegion(2, 1, 4, 3); // return 8 (红色矩形框的元素总和)
numMatrix.sumRegion(1, 1, 2, 2); // return 11 (绿色矩形框的元素总和)
numMatrix.sumRegion(1, 2, 2, 4); // return 12 (蓝色矩形框的元素总和)
```

 

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 200`
- `-105 <= matrix[i][j] <= 105`
- `0 <= row1 <= row2 < m`
- `0 <= col1 <= col2 < n`
- 最多调用 `104` 次 `sumRegion` 方法

## 解法一：动态规划

维护一个二维矩阵，对于`pre_sum[i][j]`表示`nums[0:i-1, 0:j-1]`的和【积分图】。当我们要求`nums[x0:x1, y0:y2]`的和时只需要计算`pre_sum[x1+1][y1+1]-pre_sum[x0][y1+1]-pre_sum[x1+1][y0]+pre_sum[x0][y0]`即可。

> 引用评论区一个老哥的回答：小学数学，田字形，已知整体面积，上面面积，左边面积，左上面积，求右下角矩形的面积。 右下角矩形的面积=整体面积-上面面积-左边面积+左上面积

```c++
class NumMatrix {
public:
    vector<vector<int>> pre_sum;  // pre_sum[i,j]表示nums[0:i-1, 0:j-1]的和

    NumMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        pre_sum.resize(m+1, vector<int>(n+1, 0));
        // 计算积分图
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                pre_sum[i+1][j+1] = pre_sum[i][j+1] + pre_sum[i+1][j] 
                    - pre_sum[i][j] + matrix[i][j];
            }
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        return pre_sum[row2+1][col2+1] - pre_sum[row1][col2+1] - 
            pre_sum[row2+1][col1] + pre_sum[row1][col1];
    }
};
```

时间复杂度：O(1)，查询为O(1)

空间复杂度：O(mn)

