# LeetCode74：搜索二维矩阵

## 题目

编写一个高效的算法来判断 `m x n` 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

- 每行中的整数从左到右按升序排列。
- 每行的第一个整数大于前一行的最后一个整数。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/05/mat.jpg)

```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/25/mat2.jpg)

```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
输出：false
```

 

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 100`
- `-104 <= matrix[i][j], target <= 104`

## 解法一：O(m+n)

由于矩阵的两个特征：① 每行中的整数从左到右按升序排列；② 每行的第一个整数大于前一行的最后一个整数。（将矩阵展平后就是一个递增序列）

整体步骤可以两步走：先确定`target`所在的行，再在这一行中搜索`target`。

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int i = m-1, j = n-1;  // 根据最后一列找行号

        // select row index
        while(i>=0){
            if(matrix[i][j]==target)    return true;
            else if(matrix[i][j]<target)    break;
            else    i--;
        }
        i = i+1;
        if(i>=m) return false;

        // select col index
        while(j>=0){
            if(matrix[i][j]==target)    return true;
            else if(matrix[i][j]<target)    break;
            else    j--;
        }
        return false;
    }           
};
```

时间复杂度：O(m+n)

空间复杂度：O(1)

## 解法二：O(log(m)+log(n))

基于解法一，在寻找行号时可以使用二分法；以及在确定了某一行后，在这一行中寻找`target`也可以使用二分法。

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();

        // select row index
        int left = 0, right = m-1;
        while(left<=right){
            int mid = (left+right)/2;
            if(matrix[mid][n-1]==target)    return true;
            else if(matrix[mid][n-1]>target)    right=mid-1;
            else    left=mid+1;
        }
        int row = left;
        if(row>=m)  return false;

        // select col index
        left = 0, right = n-1;
        while(left<=right){
            int mid = (left+right)/2;
            if(matrix[row][mid]==target)    return true;
            else if(matrix[row][mid]>target)    right=mid-1;
            else    left=mid+1;
        }
        return false;
    }           
};
```

时间复杂度：O($logm+logn$)

空间复杂度：O(1)

## 解法三：O($log(mn)$)

既然整体就是一个递增序列，那我们直接在这个递增序列上二分就好了。但在代码实现上，需要将`mid`映射到二维数组中的位置。

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        
        int left = 0, right = m*n-1, mid, i, j;
        while(left<=right){
            mid = (left+right)/2;
            i = mid/n; j = mid%n;
            if(matrix[i][j]==target)    return true;
            else if(matrix[i][j]<target)    left=mid+1;
            else right=mid-1;
        }

        return false;
    }           
};
```

