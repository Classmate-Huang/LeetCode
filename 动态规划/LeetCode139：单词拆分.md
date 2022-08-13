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

## 解法一：DP

将子问题定义为：`f[i]`表示为`s[0:i]`是否满足单词拆分的条件(`True` or `False`)。设置一个临时字符串`tmp`，从位置i向前累积，得到子串`s[i-j:i]`，如果`tmp`存在于字典中且`f[i-j]`剩余的字符可以被拆分为单词，则设置`f[i]=True`。代码如下：

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> dict = {wordDict.begin(), wordDict.end()};
        int n = s.size();
        vector<bool> f(n+1, false);
        // DP
        f[0] = true;
        for(int i=1; i<=n; i++){
            string tmp = "";
            for(int j=1; j<=i; j++){  // 累积字符串
                tmp = s[i-j]+tmp;
                if(dict.find(tmp)!=dict.end() && f[i-j]){
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