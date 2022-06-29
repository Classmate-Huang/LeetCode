# LeetCode224：基本计算器

## 题目

给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。

 

**示例 1：**

```
输入：s = "1 + 1"
输出：2
```

**示例 2：**

```
输入：s = " 2-1 + 2 "
输出：3
```

**示例 3：**

```
输入：s = "(1+(4+5+2)-3)+(6+8)"
输出：23
```

 

**提示：**

- `1 <= s.length <= 3 * 105`
- `s` 由数字、`'+'`、`'-'`、`'('`、`')'`、和 `' '` 组成
- `s` 表示一个有效的表达式



## 解法一：通用解

细节非常多，详见链接：https://leetcode-cn.com/problems/basic-calculator/solution/shuang-zhan-jie-jue-tong-yong-biao-da-sh-olym/

```c++
class Solution {
public:
    void replace(string& s){
        int pos = s.find(" ");
        while(pos!=-1){
            s.replace(pos, 1, "");
            pos = s.find(" ");
        }
    }

    void calculate(stack<char>& opts, stack<int>& nums){
        char op = opts.top();   opts.pop();
        int b = nums.top(); nums.pop();
        int a = nums.top(); nums.pop();
        if(op=='+')  
            nums.push(a+b);
        else 
            nums.push(a-b);
    }

    int calculate(string s) {
        // 先去掉所有空格
        replace(s);
        // 构造两个栈存储操作符和操作数
        stack<char> opts;
        stack<int> nums;  
        nums.push(0);   // 初始为0, 避免-1的出现
        
        int t = 0, i = 0;
        while(i<s.size()){
            char c = s[i];
            // 数字
            if(c>='0' && c<='9'){
                while(c>='0' && c<='9' && i<s.size()){
                    t = t*10 + int(c-'0');
                    i++;
                    c = s[i];
                }
                nums.push(t);
                t = 0;
            }
            // 运算符 or 括号
            else{
                // 如果遇到 ')' - 一直计算到（
                if(c == ')'){
                    while(opts.top()!='('){
                        calculate(opts, nums);
                    }
                    opts.pop(); //弹出（
                }
                // 遇到'(' - 直接入栈
                else if(c=='(') opts.push(c);
                // 遇到其他运算符 - 算完能算的
                else{
                    // 如果操作符前面是`(`,则需要加入0，避免`(-1 和 (+1`的情况
                    if(i>0 && s[i-1] == '(')   nums.push(0);
                    while(!opts.empty() && opts.top()!='('){
                        calculate(opts, nums);
                    }
                    opts.push(c);
                }
                i++;
            }
        }

        while(!opts.empty()){
            calculate(opts, nums);
        }

        return nums.top();
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)