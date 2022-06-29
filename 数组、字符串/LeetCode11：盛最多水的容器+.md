# LeetCode11：盛最多水的容器

## 题目

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

 

**示例 1：**

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例 2：**

```
输入：height = [1,1]
输出：1
```

 

**提示：**

- `n == height.length`
- `2 <= n <= 105`
- `0 <= height[i] <= 104`

## 解法一：双指针

这个题目比较特殊，很容易与单调栈类的题搞混，因为它可以横跨比它高的区间构建矩形面积。

而这题的思想也比较巧妙，那就是利用双指针，首先利用两个指针构建左右边界(`left`,`right`)，计算当前面积`curArea`与存储值`ans`比较。如果左边界小于右边界，那么优先移动左边界，否则移动右边界。为什么可以这样呢？其实利用反证法很容易得证：因为当前最小的那条边所能构造最大矩形已经是`curArea`，所以优先移动**小**边界。代码如下：

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int n = height.size(), ans = 0;
        int left = 0, right = n-1;
        while(left < right){
            int curArea = min(height[left], height[right]) * (right-left);
            if(curArea > ans)   ans = curArea;
            if(height[right] > height[left])    left++;
            else right--;
        }
        return ans;
    }
};
```

时间复杂度：O(n)

时间复杂度：O(1)