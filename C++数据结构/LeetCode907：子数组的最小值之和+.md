# LeetCode907：[子数组的最小值之和](https://leetcode.cn/problems/sum-of-subarray-minimums/)

## 题目

给定一个整数数组 arr，找到 min(b) 的总和，其中 b 的范围为 arr 的每个（连续）子数组。

由于答案可能很大，因此 返回答案模 10^9 + 7 。

 

示例 1：

```
输入：arr = [3,1,2,4]
输出：17
解释：
子数组为 [3]，[1]，[2]，[4]，[3,1]，[1,2]，[2,4]，[3,1,2]，[1,2,4]，[3,1,2,4]。 
最小值为 3，1，2，4，1，1，2，1，1，1，和为 17。
```

示例 2：

```
输入：arr = [11,81,94,43,3]
输出：444
```


提示：

```
1 <= arr.length <= 3 * 104
1 <= arr[i] <= 3 * 104
```

## 解法一：单调栈

我们要找到每个元素最为最小值时的数组数量，其核心就在于找到每一个元素的"左边第一个比它小的元素位置"+"右边第一个比它小的元素位置"，这显然是单调栈的题型。在本题中，我们维护一个严格递增的单调栈。

假设元素`i`，左边有`x`个大于它的值，右边有`y`个小于它的值，它所占的子数组数量即为`(x+1)*(y+1)`。

如果存在相等元素呢？比如`[3, 1, 1, 2, 4]`，由于我们维护的是严格的递增栈，所以我们会避免重复计算，对于第1次出现的1，我们只计算其在`[3,1]`中的贡献，再往后由第二个1贡献。最终代码如下：

```c++
class Solution {
public:
    
    int mod = 1e9 + 7;
    
    int sumSubarrayMins(vector<int>& arr) {
        int n = arr.size();
        stack<int> stk;  // 递增栈 记录idx
        
        long long ans = 0;	// 为了防止溢出 都采用long long类型
        for (int i = 0; i < n; i++) {
            while (!stk.empty() && arr[i] < arr[stk.top()]) {
                long long x = stk.top(), v = arr[stk.top()];
                stk.pop();
                long long pre_idx = stk.empty() ? -1 : stk.top();	// 如果为空,左边全部可以计算
                ans = (ans + v * (x - pre_idx) * (i - x)) % mod;	// 更新ans
            }
            stk.push(i);
        }
        
        while (!stk.empty()) {	// 清算阶段
            long long x = stk.top(), v = arr[stk.top()];
            stk.pop();
            long long pre_idx = stk.empty() ? -1 : stk.top();
            ans = (ans + v * (x - pre_idx) * (n - x)) % mod;
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)