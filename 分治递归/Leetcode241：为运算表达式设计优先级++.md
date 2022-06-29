# Leetcode241：为运算表达式设计优先级

## 题目

给定一个含有数字和运算符的字符串，为表达式添加括号，改变其运算优先级以求出不同的结果。你需要给出所有可能的组合的结果。有效的运算符号包含 `+`, `-` 以及 `*` 。

**示例 1:**

```
输入: "2-1-1"
输出: [0, 2]
解释: 
((2-1)-1) = 0 
(2-(1-1)) = 2
```

**示例 2:**

```
输入: "2*3-4*5"
输出: [-34, -14, -10, -10, 10]
解释: 
(2*(3-(4*5))) = -34 
((2*3)-(4*5)) = -14 
((2*(3-4))*5) = -10 
(2*((3-4)*5)) = -10 
(((2*3)-4)*5) = 10
```

## 解法一：分治

初看这个题目不太好找到思路，但我们把“加括号”的操作转换一下：对于字符串`experssion`，每遇到一个运算符，就把左边表达式的结果与右边表达式的结果进行相应操作即可。【注意没有运算符，只含数字的情况】

```c++
class Solution {
public:
    // 合并运算结果
    void merge(vector<int>& L, vector<int>& R, vector<int>& ans, char c){
        for(auto& i:L){
            for(auto& j:R){
                if(c=='+') ans.push_back(i+j);
                else if(c=='-') ans.push_back(i-j);
                else ans.push_back(i*j);
            }
        }
    }

    vector<int> divide(string s){	// 分治
        vector<int> ans;
        int n = s.size();
        for(int i=0; i<n; i++){
            if(s[i]=='+' || s[i]=='-' || s[i]=='*'){ // 遇到运算符即进行一次操作
                vector<int> L = divide(s.substr(0, i));
                vector<int> R = divide(s.substr(i+1));
                merge(L, R, ans, s[i]);
            }
        }
        if(ans.empty()) ans.push_back(atoi(s.c_str())); // 边界条件
        return ans;
    }
    
    vector<int> diffWaysToCompute(string expression) {
        return divide(expression);
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n)

## 解法二：分治法的Memorization优化

可以看到解法一很多时候会重复计算一些表达式，从而引入`Memorization`优化，存储每个表达式的运算结果，到后续用到的时候直接返回即可。

```c++
class Solution {
public:
    unordered_map<string, vector<int>> m;
    void merge(vector<int>& L, vector<int>& R, vector<int>& ans, char c){
        for(auto& i:L){
            for(auto& j:R){
                if(c=='+')  ans.push_back(i+j);
                else if(c=='-') ans.push_back(i-j);
                else ans.push_back(i*j);
            }
        }
    }
    vector<int> divide(string s){
        if(m.find(s)!=m.end())  return m[s];
        int n = s.size();
        vector<int> ans;
        for(int i=0; i<n; i++){
            if(s[i]=='+' || s[i]=='-' || s[i]=='*'){
                vector<int> L = divide(s.substr(0, i));
                vector<int> R = divide(s.substr(i+1));
                merge(L, R, ans, s[i]);
            }
        }
        if(ans.empty()) ans.push_back(atoi(s.c_str()));
        m[s] = ans;
        return ans;
    }
    vector<int> diffWaysToCompute(string expression) {
        return divide(expression);
    }
};
```

