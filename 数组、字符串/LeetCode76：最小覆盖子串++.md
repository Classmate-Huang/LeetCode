# LeetCode76：最小覆盖子串

## 题目

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

 

**注意：**

- 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

 

**示例 1：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
```

**示例 2：**

```
输入：s = "a", t = "a"
输出："a"
```

**示例 3:**

```
输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串。
```

 

**提示：**

- `1 <= s.length, t.length <= 105`
- `s` 和 `t` 由英文字母组成

 

**进阶：**你能设计一个在 `o(n)` 时间内解决此问题的算法吗？

## 解法一：滑动窗口

根据题意，首先需要确定字符串`t`中各出现的频次`cnt`，并用unordered_map存下来(也可以用数组)。

接着构造滑动窗口`[l, r]`，判断当前窗口内`s`的子串是否满足要求(字母出现的频次`cur`≥`cnt`)。为了高效地判断是否满足要求，这里利用`diff`来判断窗口内子串与`t`之间的差距，初始化为`t`的长度 (参考自**汉明距离**)。

> 采用滑动窗口模板进行解题

具体参考代码，如下：

```c++
class Solution {
public:
    unordered_map<char, int> cnt;  // 存储t中每个字母出现次数
    unordered_map<char, int> cur;    // 当前窗口内每个字母出现次数
    
    string minWindow(string s, string t) {
        for (auto c : t)    cnt[c]++;
        string ans = "";
        // 滑动窗口
        int n = s.size(), diff = t.size();
        int l = 0, r = 0;
        while (r < n) { // 优先移动右指针
            if (!cur.count(s[r]))    cur[s[r]] = 0;
            if (cnt.count(s[r]) && cnt[s[r]] > cur[s[r]])  diff--;  // diff--
            cur[s[r]]++;
            
            while (diff == 0) { // 满足条件移动左指针
                if (ans.size() == 0 || ans.size() > (r-l+1))    ans = s.substr(l, r-l+1);
                if (cnt.count(s[l]) && cur[s[l]] <= cnt[s[l]]) diff++;  // diff++
                cur[s[l]]--;
                l++;
            }
            
            r++;
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)