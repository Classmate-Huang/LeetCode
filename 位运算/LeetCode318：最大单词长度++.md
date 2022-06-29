# LeetCode318：最大单词长度

## 题目

给定一个字符串数组 `words`，找到 `length(word[i]) * length(word[j])` 的最大值，并且这两个单词不含有公共字母。你可以认为每个单词只包含小写字母。如果不存在这样的两个单词，返回 0。

 

**示例 1:**

```
输入: ["abcw","baz","foo","bar","xtfn","abcdef"]
输出: 16 
解释: 这两个单词为 "abcw", "xtfn"。
```

**示例 2:**

```
输入: ["a","ab","abc","d","cd","bcd","abcd"]
输出: 4 
解释: 这两个单词为 "ab", "cd"。
```

**示例 3:**

```
输入: ["a","aa","aaa","aaaa"]
输出: 0 
解释: 不存在这样的两个单词。
```

 

**提示：**

- `2 <= words.length <= 1000`
- `1 <= words[i].length <= 1000`
- `words[i]` 仅包含小写字母



## 解法一：利用位运算标记字母是否出现

确定思路：首先需要两两比较字符串，如果两个字符串中不包含相同的字母，那么再计算长度之和。问题就在于如何高效地记录每个字符串出现的字母，以及判断两个字符串是否含有相同字母。

利用位运算操作：一个`int`类型为32位，可以用将每一位用于记录对应的字母是否出现，假设一个字符串为`ace或者aacee`可以记录为`0b00...0010101`。这样我们可以将每个字符串都用一个`int`变量(mask)来表示。比较两个字符串是否含有相同字符，直接将对应的`mask`做**与操作**即可。

最后，采用一个hash map来记录mask与对应size的关系，并且每个mask都只保留最长的size，进一步提高效率。

```c++
class Solution {
public:
    int maxProduct(vector<string>& words) {
        int ans = 0;
        unordered_map<int, int> hash;
        for(string& s:words){
            int mask = 0, len = s.size();
            for(char& c:s){ // 对字符串对应的mask
                mask = mask | 1<<(c-'a');  // 字母对应位置为1
            }
            // 只有之前没有出现这个mask 或者 之前的size比当前小才进行比较
            if(hash[mask]<len){
                for(auto& [h_mask, h_size]:hash){
                    if((h_mask&mask)==0){
                        ans = max(ans, h_size*len);
                    }
                }
                hash[mask] = len;
            }
        }
        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n)