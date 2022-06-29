# LeetCode42：接雨水

## 题目

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

 

**提示：**

- `n == height.length`
- `1 <= n <= 2 * 104`
- `0 <= height[i] <= 105`



## 解法一：提前存储

首先明确最简单的暴力思想，要统计数组中每个元素的积水量，需要找到其两边的最大值再减去当前高度获得`min(leftmax，rightmax)-hegit[i]`。但这样时间复杂度是O($n^2$)，导致超时。

借助于这个想法，我们可以将需要的数据提前存储起来，用两个`vector`保存每个点的最大左边界和最大右边界，而这个问题可以通过两次循环解决。在求积水量的时候，根据`vector`的值直接调用就行了。代码如下：

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        vector<int> leftMargin(n, 0), rightMargin(n, 0);
        // 左边界
        int tempMax = 0;
        for(int i=0; i<n; i++){
            tempMax = max(tempMax, height[i]);
            leftMargin[i] = tempMax;
        }
        // 右边界
        tempMax = 0;
        for(int i=n-1; i>=0; i--){
            tempMax = max(tempMax, height[i]);
            rightMargin[i] = tempMax;
        }
        // 得到答案
        int ans = 0;
        for(int i=0; i<n; i++){
            ans += min(leftMargin[i], rightMargin[i]) - height[i];
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：单调栈

采用单调栈可以在一次遍历过程中解决这个问题，其核心思想是如果向右遍历，直到出现了高点。如果存在高点，则更新其能导致的低洼积水。

在这个过程中，维护一个单调递减栈，方便找到左右边界。代码如下：

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size(), ans = 0, current = 0;
        stack<int> stk;
        // 从左到右遍历
        while(current < n){
            // 栈不为空 且 当前柱大于前一个最大值
            while(!stk.empty() && height[current] > height[stk.top()]){
                int t = stk.top();
                stk.pop();
                if(stk.empty()) break; // 没有左边界，漏了
                int dist = current - stk.top() - 1;   // 距离
                int bounded_height = min(height[current], height[stk.top()]) - height[t];   // 目前的容量
                ans += dist*bounded_height;
            }
            stk.push(current++);
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法三：双指针(木桶效应)

木桶效应：一个木桶能存储的最大水量由其最短的那块木板决定。

构造两个指针`l,r`以及两个变量`lBound,rBound`来表示当前左右边界值。`l,r`一起向内移动（优先移动较短边界的方向），对于某个位置`i`，我们可以知道目前它的左右边界值，直接更新答案即可。

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        int l = 0, r = n-1;
        
        int ans = 0, lBound = height[l], rBound = height[r];
        while (l < r) {
            // 移动左边界
            if (lBound <= rBound) {
                l++;
                if (lBound > height[l]) ans += lBound - height[l];
                lBound = max(lBound, height[l]);
            }
            else {
                r--;
                if (rBound > height[r]) ans += rBound - height[r];
                rBound = max(rBound, height[r]);
            }
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)