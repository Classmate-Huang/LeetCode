# LeetCode16：最接近的三数之和

## 题目

给你一个长度为 `n` 的整数数组 `nums` 和 一个目标值 `target`。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。

返回这三个数的和。

假定每组输入只存在恰好一个解。

**示例 1：**

```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

**示例 2：**

```
输入：nums = [0,0,0], target = 1
输出：0
```

**提示：**

- `3 <= nums.length <= 1000`
- `-1000 <= nums[i] <= 1000`
- `-104 <= target <= 104`

## 解法一：双指针

先对数据进行排序，确定`nums[i]`，只需要去找[i+1, n-1]中选两个数，其和最接近`target-nums[i]`即可。因为数组已经经过排序，所以在找剩下的两个数时可以用双指针。代码如下：

```c++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        int n = nums.size(), ans = nums[0]+nums[1]+nums[2];
        sort(nums.begin(), nums.end());
        for(int i=0; i<n; i++){
            int left = i+1, right = n-1;
            while(left < right){
                int temp = nums[left] + nums[right];
                // 更新ans
                if(abs(target-ans) > abs(target-nums[i]-temp))    ans = nums[i]+temp;
                // 移动双指针
                if(temp == target-nums[i])   return target;
                else if(temp > target-nums[i])   right--;
                else left++;
            }
        }
        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(1)