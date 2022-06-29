# LeetCode438：找到字符串中所有字母异位词

## 题目

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。

 

**示例 1:**

```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

 **示例 2:**

```
输入: s = "abab", p = "ab"
输出: [0,1,2]
解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

 

**提示:**

- `1 <= s.length, p.length <= 3 * 104`
- `s` 和 `p` 仅包含小写字母



## 解法一：滑动窗口

为76题的简化版，具体解法见76题，代码如下：

```c++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int lenS = s.size(), lenP = p.size();
        vector<int> ans;
        if(lenS < lenP) return ans;
        // 记录p出现的次数
        int fs[26] = {0}, fp[26] = {0};
        for(auto& i:p){
            fp[i-'a']++;
        }
        // 滑动窗口[left, right)
        int left = 0, right = 0;
        int distance = lenP;    // distance用于衡量窗口内与gt间的差异
        while(right <= lenS){
            while(right-left < lenP){   // 初始化窗口
                int c = s[right] - 'a';
                if(fs[c] < fp[c])   distance--;
                fs[c]++;
                right++;
            }
            // 记录
            if(distance==0) ans.push_back(left);
            // 移动窗口
            if(right == lenS)    break;
            // 左边界右移
            int c = s[left]-'a';
            fs[c]--;
            if(fs[c] < fp[c])   distance++;
            // 右边界右移
            c = s[right]-'a';
            if(fs[c] < fp[c]) distance--;
            fs[c]++;

            left++; right++;
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)