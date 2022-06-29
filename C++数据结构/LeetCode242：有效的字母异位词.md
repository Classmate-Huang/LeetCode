# LeetCode242：有效的字母异位词

## 题目

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

示例 1:

> 输入: s = "anagram", t = "nagaram"
> 输出: true

示例 2:

> 输入: s = "rat", t = "car"
> 输出: false

说明:

> 你可以假设字符串只包含小写字母。

进阶:

> 如果输入字符串包含 unicode 字符怎么办？你能否调整你的解法来应对这种情况？

## 解法一：哈希表

如果字母只包含26个字母，并且只包含小写，我们可以直接利用一个长度26的数组存储各字母出现次数。但为了解决进阶问题，使用哈希表是更通用的解，这里使用`unordered_map`，代码如下：

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size()!=t.size())  return false;
        unordered_map<char, int> m;

        for(char& c:s){
            m[c]++;
        }

        for(char& c:t){
            if(m.find(c)==m.end() || m[c]<=0)
                return false;
            m[c]--;
        }

        return true;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)