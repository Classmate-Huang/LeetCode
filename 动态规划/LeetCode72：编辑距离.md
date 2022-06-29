# LeetCode72：编辑距离

## 题目

给你两个单词 `word1` 和 `word2`，请你计算出将 `word1` 转换成 `word2` 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

 

**示例 1：**

```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

 

**提示：**

- `0 <= word1.length, word2.length <= 500`
- `word1` 和 `word2` 由小写英文字母组成

## 解法一：DP

一个经典的动态规划问题：编辑距离（也叫汉明距离），构造一个二维矩阵`f[i][j]`表示长度为i的串s1和长度为j的串s2之间的最小编辑距离。现在考虑，当`s1[i-1]==s2[j-1]`时，那么`f[i][j]==f[i-1][j-1]`；否则，存在三种情况：① 修改`s1[i-1]`或者`s2[j-1]`使其相等，此时`f[i][j]==f[i-1][j-1]+1`，② 在`s1`后增加一个字符使其与`s2[j-1]`相等，或者删除`s2[j-1]`，此时`f[i][j]==f[i][j-1]+1`，③ 在`s2`后增加一个字符使其与`s1[i-1]`相等，或者删除`s1[i-1]`，此时`f[i][j]==f[i-1][j]+1`。

此外还需要注意边界条件的设置，最终代码实现如下： 

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size(), n = word2.size();
        vector<vector<int>> f(m+1, vector<int>(n+1, 0));
        // 边界条件设置
        for(int i=1; i<=m; i++) f[i][0] = i;
        for(int j=1; j<=n; j++)  f[0][j] = j;

        for(int i=1; i<=m; i++){
            for(int j=1; j<=n; j++){
                if(word1[i-1]==word2[j-1])
                    f[i][j] = f[i-1][j-1];
                else{
                    f[i][j] = min(f[i-1][j-1]+1, min(f[i-1][j]+1, f[i][j-1]+1));
                }
            }
        }

        return f[m][n];
    }
};
```

时间复杂度：O($mn$)

空间复杂度：O($mn$)