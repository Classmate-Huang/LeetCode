# LeetCode739：每日温度

## 题目

请根据每日 `气温` 列表 `temperatures` ，请计算在每一天需要等几天才会有更高的温度。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

**示例 1:**

```
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

**示例 2:**

```
输入: temperatures = [30,40,50,60]
输出: [1,1,1,0]
```

**示例 3:**

```
输入: temperatures = [30,60,90]
输出: [1,1,0]
```

 

**提示：**

- `1 <= temperatures.length <= 105`
- `30 <= temperatures[i] <= 100`



## 解法一：构建单调栈

简单的思路就是暴搜，但是会超时。而且根据题目本身的特性，可以利用栈来**存储还未找到更高温的元素序列**。每次遍历到新的一天，只需要处理`stack`中的元素即可。

得到当前第`i`天的温度，会把`stack`内所有低于`temperatures[i]`的元素都会被弹出栈。因此维护的`stack`实际上是一个单调栈。

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        stack<int> stk; // 维护递减栈 idx
        vector<int> ans(n, 0);
        
        for (int i = 0; i < n; i++) {
            while (!stk.empty() && temperatures[i] > temperatures[stk.top()]) {
                ans[stk.top()] = i - stk.top();
                stk.pop();
            }
            stk.push(i);
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)