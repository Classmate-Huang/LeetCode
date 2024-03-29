# LeetCode139：单词拆分

## 题目

给定一个**非空**字符串 *s* 和一个包含**非空**单词的列表 *wordDict*，判定 *s* 是否可以被空格拆分为一个或多个在字典中出现的单词。

**说明：**

- 拆分时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。

**示例 1：**

```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```

**示例 2：**

```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```

**示例 3：**

```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

提示：

```
1 <= s.length <= 300
1 <= wordDict.length <= 1000
1 <= wordDict[i].length <= 20
s 和 wordDict[i] 仅有小写英文字母组成
wordDict 中的所有字符串 互不相同
```



## 解法一：DP

将子问题定义为：`f[i]`表示为`s`中前`i`个字符是否满足单词拆分的条件。

对于子串`temp = s.substr(0, i)`，可以枚举`j∈[0, i)`，判断`f[j]`和剩下的子串`temp.substr(j, i)`是否满足条件即可。代码如下：

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
        
        int n = s.size();
        vector<bool> f(n+1, false);     // f[i]表示s中前i个字符是否可以被拆分成功
        f[0] = true;

        for (int i = 1; i <= n; i++) {
            string temp = s.substr(0, i);
            for (int j = 0; j < i; j++) {
                if (f[j] && (wordSet.find(temp.substr(j, i-j)) != wordSet.end()) ) {
                    f[i] = true;
                    break;
                }
            }
        }

        return f[n];
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n)

> 还可以进一步优化时间复杂度，当`tmp`的长度大于字典中最长单词的长度，即可提前退出循环