# Leetcode51/52：N-皇后

## 题目

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

```
输入：n = 4
输出：[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例 2：**

```
输入：n = 1
输出：[["Q"]]
```

 

**提示：**

- `1 <= n <= 9`
- 皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

## 解法一：回溯

`DFS`+回溯，按行遍历，每次判断当前位置是否满足要求(同一列无Q，对角线上无Q)，若满足要求，则往下遍历，不满足则回溯。代码如下：

```c++
class Solution {
public:
    vector<vector<string>> ans; 
    vector<int> record; // 记录每一行皇后所在列
    vector<string> cur; // 目前存储的结果
    
    // 判断放在(row, j)位置上是否合法
    bool isOK(int row, int j) {
        for (int k = 0; k < row; k++) {
            // 共列 或者 共斜线（通过判断行差是否等于列差来确定）
            if (record[k] == j || abs(k - row) == abs(record[k] - j)) return false;
        }
        return true;
    }
    
    void dfs(int row, int n) {
        if (row == n) {
            ans.push_back(cur);
            return ;
        }
        string s = string(n, '.');
        for (int j = 0; j < n; j++) {
            if (isOK(row, j)) {
                // 改变状态
                s[j] = 'Q';
                cur.push_back(s);
                record[row] = j;
                
                dfs(row+1, n);
                
                // 恢复状态
                s[j] = '.';
                cur.pop_back();
                record[row] = -1;
            }
        }
    }
    
    
    vector<vector<string>> solveNQueens(int n) {
        record = vector<int> (n, -1);
        dfs(0, n);
        return ans;
    }
};
```

时间复杂度：O(N!)

空间复杂度：O(N)

## 解法二：利用位运算优化

如果不需要输出具体解的形式(52题)，使用位运算能够提升常数级别的时间复杂度。

主要思路就是记录三个限制状态：① 列冲突`limitCol`；② 左斜线冲突`limitDia1`；③ 右斜线冲突`limitDia2`。对于当前行`row`，我们可以根据当前的三个状态来很快地判断这一行有哪些列能够放，然后更新状态送入下一层。代码如下：

```c++
class Solution {
public:
    int ans = 0;
    
    void dfs(int target, int limitCol, int limitDia1, int limitDia2) {
        if (limitCol == target) ans += 1;   // 达到目标
        
        int pos = target & (~(limitCol | limitDia1 | limitDia2));   // 不冲突的位置
        
        while (pos != 0) {
            int right = pos & (-pos);   // 选择最右端的可行位置
            pos -= right;
            dfs(target, (limitCol | right), (limitDia1 | right) << 1, (limitDia2 | right) >> 1);
        }
    }
    
    int totalNQueens(int n) {
        int target = (1 << n) - 1;  // 目标状态，全占满 如果n=8，那么就是 0000 ...0 1111 1111
        dfs(target, 0, 0, 0);
        return ans;
    }
};
```

时间复杂度：O(N!)

空间复杂度：O(N)