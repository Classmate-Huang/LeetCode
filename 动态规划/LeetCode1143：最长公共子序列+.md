# LeetCode1143：最长公共子序列

## 题目

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。

一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

- 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。

两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。

 

**示例 1：**

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace" ，它的长度为 3 。
```

**示例 2：**

```
输入：text1 = "abc", text2 = "abc"
输出：3
解释：最长公共子序列是 "abc" ，它的长度为 3 。
```

**示例 3：**

```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0 。
```

 

**提示：**

- `1 <= text1.length, text2.length <= 1000`
- `text1` 和 `text2` 仅由小写英文字符组成。



## 解法一：DP

子问题`f[i][j]`表示`text1`**前i个字符**与`text2`**前j个字符**的最大公共子序列长度，初始时`f`为0。在不考虑text[i-1]与text[j-1]是否相等的情况下，`f[i][j]`其必然为`max(f[i-1][j], f[i][j-1])`。若相等，再进行一次比较，`f[i][j]=max(f[i-1][j-1]+1, f[i][j])`。

代码如下：

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n1 = text1.size(), n2 = text2.size();
        vector<vector<int>> f(n1+1, vector<int>(n2+1,0)); // 初始为0
        
        for(int i=0; i<n1; i++){
            for(int j=0; j<n2; j++){
                f[i+1][j+1] = max(f[i+1][j], f[i][j+1]);
                if(text1[i]==text2[j]){
                    f[i+1][j+1] = max(f[i][j]+1, f[i+1][j+1]);
                }
            }
        }

        return f[n1][n2];
    }
};
```

时间复杂度：O($mn$)

空间复杂度：O($mn$)