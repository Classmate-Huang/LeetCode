# LeetCode316：去除重复字母

## 题目

给你一个字符串 `s` ，请你去除字符串中重复的字母，使得每个字母只出现一次。需保证 **返回结果的字典序最小**（要求不能打乱其他字符的相对位置）。

 

**示例 1：**

```
输入：s = "bcabc"
输出："abc"
```

**示例 2：**

```
输入：s = "cbacdcbc"
输出："acdb"
```

 

**提示：**

- `1 <= s.length <= 104`
- `s` 由小写英文字母组成

## 解法一：单调栈

> 详情参照官方题解视频：https://leetcode-cn.com/problems/remove-duplicate-letters/solution/qu-chu-zhong-fu-zi-mu-by-leetcode-soluti-vuso/

对于这种需要记录单调性质的一维数组遍历，我们不难想到单调栈。要解决这个问题，首先明确几点：① 按字典序依次递增的一定是最优的；② 每个字符都需要记录在结果中；③ 如果当前元素已经出现在栈中，那么可以跳过此元素的判断。

我们首先构造两个vector，一个用于记录每个字符最后出现的位置，主要针对②；另一个用于记录某个字符是否出现在栈中，主要针对③。现在可以以如下规则进行判断：遍历每个元素，如果当前元素已经存在栈中，直接跳过；如果不存在，那么需要将其加入栈，如果元素大于栈顶元素，直接入栈即可；否则需要判断是否能将栈顶元素弹出（针对①，需要递归进行，直到满足条件），如果栈顶元素在后续的遍历过程中还会出现，那么可以弹出栈顶元素，否则无法弹出。代码如下：

```c++
class Solution {
public:
    string removeDuplicateLetters(string s) {

        string stk;    // 单调递增栈 (用string模拟)
        vector<bool> inStack(26, false);   // 记录字母是否出现在栈中
        vector<int> lastIdx(26);    // 字母最后出现的索引位置
        for(int i=0; i<s.size(); i++){
            lastIdx[s[i]-'a'] = i;
        }

        for(int i=0; i<s.size(); i++){
            // s[i]已经存在栈中
            if(inStack[s[i]-'a'])   continue;
            // 栈不为空 && 小于当前栈顶元素
            while(!stk.empty() && s[i]<stk.back()){
                if(i > lastIdx[stk.back()-'a']) break;  // 后面是否还存在栈顶元素
                inStack[stk.back()-'a'] = false; // 出栈
                stk.pop_back();
            }
            stk.push_back(s[i]);
            inStack[s[i]-'a'] = true;
        }

        return stk;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)