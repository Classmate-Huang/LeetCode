# LeetCode79：单词搜索

## 题目

给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/11/04/word-1.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
输出：true
```

**示例 3：**

![img](https://assets.leetcode.com/uploads/2020/10/15/word3.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
输出：false
```

 

**提示：**

- `m == board.length`
- `n = board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board` 和 `word` 仅由大小写英文字母组成

 

**进阶：**你可以使用搜索剪枝的技术来优化解决方案，使其在 `board` 更大的情况下可以更快解决问题？

## 解法一：回溯

简单的回溯法解决，遍历所有节点，如果其等于`word[0]`，则使用DFS继续向下遍历，遇到`board[xt][yt]!=word[i]`的情况剪枝，如果遍历完所有word字符则返回`True`。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> site = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
	// 回溯
    bool backstracking(vector<vector<char>>& board, vector<vector<int>>& unseen,  int x, int y, string& word, int i){
        if(i==word.size()){	// 满足条件
            return true;
        }
        for(auto& v:site){	// 遍历周围节点
            int xt = x+v[0], yt = y+v[1];
            if(xt>=0 && xt<board.size() && yt>=0 && yt<board[0].size()){
                if(board[xt][yt]==word[i] && unseen[xt][yt]){ // 满足要求，向下递归
                    unseen[xt][yt] = 0; // 更改状态
                    bool flag = backstracking(board, unseen, xt, yt, word, i+1);
                    if(flag)
                        return true;
                    unseen[xt][yt] = 1; // 恢复状态
                }
            }
        }
        return false;
    }

    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size(), n = board[0].size();
        vector<vector<int>> unseen(m, vector<int>(n, 1));
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(word[0]==board[i][j]){ // 如果遍历到符合要求的节点，进入回溯函数
                    unseen[i][j] = 0;
                    bool flag = backstracking(board, unseen, i, j, word, 1);	// 存在解
                    if(flag)
                        return true;
                    unseen[i][j] = 1;
                }
            }
        }
        return false; // 不存在解
    }
};
```

时间复杂度：O($MN·L^3$)，宽松上界，MN为矩阵长宽，L为word长度；

空间复杂度：O(MN)