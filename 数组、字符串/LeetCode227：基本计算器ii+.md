# LeetCode227：基本计算器ii

## 题目

给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。

整数除法仅保留整数部分。

 

**示例 1：**

```
输入：s = "3+2*2"
输出：7
```

**示例 2：**

```
输入：s = " 3/2 "
输出：1
```

**示例 3：**

```
输入：s = " 3+5 / 2 "
输出：5
```

 

**提示：**

- `1 <= s.length <= 3 * 105`
- `s` 由整数和算符 `('+', '-', '*', '/')` 组成，中间由一些空格隔开
- `s` 表示一个 **有效表达式**
- 表达式中的所有整数都是非负整数，且在范围 `[0, 231 - 1]` 内
- 题目数据保证答案是一个 **32-bit 整数**

## 解法一：双栈

利用两个栈，一个存储操作符，一个存储计算值，并事先设定好运算优先级。几个细节：

① 栈中存储的计算值顺序：比如`/`和`-`，先从栈中取出来的值为操作数，后取出来的为被操作数；

② 存在连续数字：比如`1234`，所以需要利用`t*10+c`来记录完整的数字，并在进行其他操作前入栈；

③ 优先级：`+,-`为0，`*, /`为1，同优先级或者低优先级的不能同时存在栈内。

代码如下：

```c++
class Solution {
public:
    unordered_map<char, int> m = {
        {'+', 0}, {'-', 0}, {'/', 1}, {'*', 1}
    };

    int calculate(char& c, int& a, int& b){
        int ans = 0;
        if(c=='+')  ans = a+b;
        else if(c=='-')  ans = a-b;
        else if(c=='*')  ans = a*b;
        else if(c=='/')  ans = a/b;
        return ans;
    }

    int calculate(string s) {
        stack<char> opts;
        stack<int> nums;
        int t = 0;
        for(char& c:s){
            if(c==' ') continue;
            // 之前的优先级更高
            else if(m.find(c)!=m.end()){
                nums.push(t);
                t = 0;
                while(!opts.empty() && m[c] <= m[opts.top()]){
                    char c = opts.top(); opts.pop();
                    int b = nums.top(); nums.pop();
                    int a = nums.top(); nums.pop();
                    nums.push(calculate(c, a, b));
                }
                opts.push(c);
            }
            else{
                t = t*10 + int(c-'0');
            }
        }
        nums.push(t);
        while(!opts.empty()){
            char c = opts.top(); opts.pop();
            int b = nums.top(); nums.pop();
            int a = nums.top(); nums.pop();
            nums.push(calculate(c, a, b));
        }
        return nums.top();
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

