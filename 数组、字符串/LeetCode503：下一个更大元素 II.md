# LeetCode503：下一个更大元素 II

## 题目

给定一个循环数组 `nums` （ `nums[nums.length - 1]` 的下一个元素是 `nums[0]` ），返回 *`nums` 中每个元素的 **下一个更大元素*** 。

数字 `x` 的 **下一个更大的元素** 是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 `-1` 。

 

**示例 1:**

```
输入: nums = [1,2,1]
输出: [2,-1,2]
解释: 第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```

**示例 2:**

```
输入: nums = [1,2,3,4,3]
输出: [2,3,4,-1,4]
```

 

**提示:**

- `1 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`

## 解法一：单调栈+两次循环

和496的思路非常一致，维护一个单调递减栈，遍历一遍数组元素，每遇到元素比栈顶更高，就进行弹出和赋值操作。只不过需要再进行一次循环，第二次遍历时，只进行弹出而不压栈。代码如下：

```c++
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        stack<pair<int, int>> stk;
        vector<int> ans(n, -1);
        
        for(int i=0; i<nums.size(); i++){
            while(!stk.empty() && nums[i]>stk.top().second){	// 弹出+赋值操作
                ans[stk.top().first] = nums[i];
                stk.pop();
            }
            stk.push(make_pair(i, nums[i]));	// 压入操作
        }
        // 再遍历一遍
        for(int i=0; i<nums.size(); i++){
            if(stk.empty()) break;
            while(!stk.empty() && nums[i]>stk.top().second){	// 弹出+赋值操作
                ans[stk.top().first] = nums[i];
                stk.pop();
            }
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)