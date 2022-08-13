s# LeetCode15：三数之和

## 题目

给你一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？请你找出所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

 

**示例 1：**

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

**示例 2：**

```
输入：nums = []
输出：[]
```

**示例 3：**

```
输入：nums = [0]
输出：[]
```

 

**提示：**

- `0 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`



## 解法一：排序 + 双指针

首先明确一点，该题的核心思想其实就是列举出所有可能的答案。但要求我们给出的答案中是没有重复的，这就有一个关键性的操作：排序。在有序数组中我们就很容易很够控制答案不重复，因为答案中的每个元素`[a,b,c]`严格递增。

> 当然也可以不使用排序，而是使用哈希表之类的操作进行去重，但这样并不会带来整体时间复杂度的优化，反而将操作变得复杂。

排序后，就可以执行遍历，首先用指针`i`确定第一个元素`a`，然后在`[i+1,n-1]`中找到`b,c`使得`a+b+c=0`。在找b,c的过程中，使用双指针，找到`b+c=0-a`。值得注意的是：在整个过程，注意控制重复解（数组中可能存在重复元素），因此每次遍历完之后，下一个指向的元素不能是相同的元素。代码如下：

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        
        for (int i = 0; i < n; i++) {
            if (i > 0 && nums[i] == nums[i-1])  continue;
            int l = i+1, r = n-1, target = -1 * nums[i];
            while (l < r) {
                if (nums[l] + nums[r] == target) {
                    ans.push_back({nums[i], nums[l], nums[r]});
                    l++; r--;
                    while (nums[l] == nums[l-1] && l < r)   l++;
                    while (nums[r] == nums[r+1] && l < r)   r--;
                }
                else if (nums[l] + nums[r] < target) l++;
                else    r--;
            }
        }
        
        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O($log(n)$)，快速排序所占用的空间。

