# LeetCode84：柱状图中最大的矩形

## 题目

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

 

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/01/04/histogram.jpg)

```
输入：heights = [2,1,5,6,2,3]
输出：10
解释：最大的矩形为图中红色区域，面积为 10
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/01/04/histogram-1.jpg)

```
输入： heights = [2,4]
输出： 4
```

 

**提示：**

- `1 <= heights.length <=105`
- `0 <= heights[i] <= 104`

## 解法一：单调栈

遍历每个元素，对于每个元素我们去找以它为高的最大矩阵。首先维护一个单调递增栈，遍历到某个元素如果大于当前栈顶元素`nums[x]`，说明以`num[x]`的右边界`right`已经确定了，如果栈为空，那么左边界`left`为-1，否则为下一个栈元素，那么就可以计算`nums[x]`所对应的最大矩形面积。需要注意，遍历一次数组元素之后，最后仍需要将所有栈元素清空，其中右边界固定为`n`。代码如下：

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n = heights.size();
        stack<pair<int, int>> stk;  // 维护一个递增栈
        int maxArea = heights[0];
        for(int i=0; i<n; i++){
            // 出现右边界
            while(!stk.empty() && heights[i]<stk.top().second){
                int h = stk.top().second; stk.pop();
                int left = stk.empty() ? -1 : stk.top().first;	// 计算左边界
                int curArea = (i - left - 1) * h;	// 计算面积
                maxArea = max(maxArea, curArea);
            }
            stk.push(make_pair(i, heights[i]));
        }
        while(!stk.empty()){
            int h = stk.top().second; stk.pop();	// 
            int right = n, left = stk.empty() ? -1 : stk.top().first; // 固定右边界，计算左边界
            int curArea = (right - left - 1) * h;
            maxArea = max(maxArea, curArea);
        }
        return maxArea;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)