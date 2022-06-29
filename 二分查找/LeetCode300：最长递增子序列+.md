# LeetCode300：最长递增子序列

## 题目

给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

 

**示例 1：**

```
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

**示例 2：**

```
输入：nums = [0,1,0,3,2,3]
输出：4
```

**示例 3：**

```
输入：nums = [7,7,7,7,7,7,7]
输出：1
```

 

**提示：**

- `1 <= nums.length <= 2500`
- `-104 <= nums[i] <= 104`

 

**进阶：**

- 你可以设计时间复杂度为 `O(n2)` 的解决方案吗？
- 你能将算法的时间复杂度降低到 `O(n log(n))` 吗?

## 解法一：DP

子问题`f[i]`为以`nums[i]`结尾的最大递增子序列，当出现`nums[i]>num[j](j<i时)`，状态转移方程为`f[i]=max(f[i], f[j]+1)`。代码如下：

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size(), ans = 1;
        vector<int> f(n, 1);
        for(int i=1; i<n; i++){
            for(int j=i-1; j>=0; j--){
                if(nums[i]>nums[j]){
                    f[i] = max(f[i], f[j]+1);
                }
            }
            ans = max(ans, f[i]);
        }
        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n)

## 解法二：贪心+二分

进阶的方法很巧妙，因为本题只关心最长上升子序列的长度，而不关心具体内容，所以可以使用一个数组存储每个出现位置上的最小数。

核心思想是采用**贪心**的思想去维护一个数组`f`，其维护一个尽可能的长上升子序列，并且增幅尽可能得小。每次遍历到`nums[i]`，判断其是否大于`f.back()`，如果满足直接添加到末尾。如果不满足，找到当前数组中第一个不小于`nums[i]`的数字进行替换（`这个过程使用二分法`）。

> 建议直接参照官方题解：https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/zui-chang-shang-sheng-zi-xu-lie-by-leetcode-soluti/

```c++
class Solution {
public:
    // find the first number that not smaller than target
    int halfSelect(vector<int>& f, int target){
        int left = 0, right = f.size()-1;
        while(left<right){
            int mid = (left + right)/2;
            if(f[mid]<target){
                left = mid+1;
            }
            else{
                right = mid;
            }
        }
        return left;
    }

    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size(), ans = 1;
        vector<int> f = {nums[0]};

        for(int i=1; i<n; i++){
            if(nums[i]>f.back()){
                f.push_back(nums[i]);
            }
            else{
                int t = halfSelect(f, nums[i]);
                f[t] = nums[i];
            }
        }

        return f.size();
    }
};
```

时间复杂度：O($nlog(n)$)

空间复杂度：O(n)