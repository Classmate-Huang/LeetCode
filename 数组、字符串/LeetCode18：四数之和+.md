# LeetCode18：四数之和

## 题目

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

 

**示例 1：**

```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**示例 2：**

```
输入：nums = [2,2,2,2,2], target = 8
输出：[[2,2,2,2]]
```

 

**提示：**

- `1 <= nums.length <= 200`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`

## 解法一： 双指针

解法思想与`三数之和`一样，**排序+双指针**，不同的是，这里固定的是两个端点`[i, j]`，然后从`[i+1, j-1]`中去找两个和为**target-nums[i]-nums[j]**的数。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        int n = nums.size();
        sort(nums.begin(), nums.end());	// 排序
        vector<vector<int>> ans;

        for(int i=0; i<n; i++){
            if(i>0 && nums[i]==nums[i-1])   continue;	// 避免重复解
            for(int j=n-1; j>i; j--){
                if(j<n-1 && nums[j]==nums[j+1])   continue;	// 避免重复解
                int left = i+1, right = j-1;
                while(left < right){
                    long long curTarget = 0LL + nums[i] + nums[j] + nums[left] + nums[right];
                    if(curTarget == target){
                        ans.push_back({nums[i], nums[left], nums[right], nums[j]});
                        right--; left++;
                        while(nums[right]==nums[right+1] && right>left) right--; 	// 避免重复解
                        while(nums[left]==nums[left-1] && right>left)   left++; 	// 避免重复解
                    }
                    else if(curTarget > target){
                        right--;
                        while(nums[right]==nums[right+1] && right>left) right--; 	// 避免重复解
                    }
                    else{
                        left++;
                        while(nums[left]==nums[left-1] && right>left)   left++; 	// 避免重复解
                    }
                }
            }
        }
        return ans;
    }
};
```

时间复杂度：O($n^3$)

空间复杂度：O(1)