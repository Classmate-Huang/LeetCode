# LeetCode34：在排序数组中查找元素的第一个和最后一个位置

## 题目

给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

**进阶：**

- 你可以设计并实现时间复杂度为 `O(log n)` 的算法解决此问题吗？

 

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

 

**提示：**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` 是一个非递减数组
- `-109 <= target <= 109`

## 解法一：双指针遍历

利用双指针遍历数组即可，代码如下：

```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0, right = n-1;
        vector<int> ans(2, -1);		// 初始化
        
        while(left<n && right>=0 && left<=right){
            
            if(nums[left]==target && nums[right]==target){
                ans[0] = left; ans[1] = right;
                break;
            }

            if(nums[left]!=target)
                left++;

            if(nums[right]!=target)
                right--;
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)

## 解法二：二分法

分别使用二分法找到左右边界【找到第一个满足条件的位置 / 找到第一个不满足条件的位置-1】，代码如下，利用`lower_bound`和`upper_bound`的简化代码：

```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if(nums.size() == 0)    return {-1, -1};

        int l = lower_bound(nums.begin(), nums.end(), target) - nums.begin();
        int r = upper_bound(nums.begin(), nums.end(), target) - nums.begin();

        if(l < nums.size() && nums[l] == target)   return {l, r-1};

        return {-1, -1};
    }
};
```

使用自己的二分实现：

```c++
class Solution {
public:

    vector<int> searchRange(vector<int>& nums, int target) {
        if(nums.size() == 0)    return {-1, -1};

        // 找到第一个满足条件的位置
        int l = 0, r = nums.size()-1;
        while(l < r){
            int mid = (r - l) / 2 + l;
            if(nums[mid] < target)  l = mid+1;
            else r = mid;
        }
        if(nums[l] != target)   return {-1, -1};
        int ans1 = l;

        // 找到第一个不满足条件的位置 [ >target ], 再 -1 
        l = 0, r = nums.size();
        while(l < r){
            int mid = (r - l) / 2 + l;
            if(nums[mid] <= target) l = mid + 1;
            else  r = mid;
        }
        return {ans1, l-1};
    }
};
```

时间复杂度：O(logn)

空间复杂度：O(1)

