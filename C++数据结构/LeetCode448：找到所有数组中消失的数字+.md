# LeetCode448：找到所有数组中消失的数字

## 题目

给你一个含 `n` 个整数的数组 `nums` ，其中 `nums[i]` 在区间 `[1, n]` 内。请你找出所有在 `[1, n]` 范围内但没有出现在 `nums` 中的数字，并以数组的形式返回结果。

 

**示例 1：**

```
输入：nums = [4,3,2,7,8,2,3,1]
输出：[5,6]
```

**示例 2：**

```
输入：nums = [1,1]
输出：[2]
```

 

**提示：**

- `n == nums.length`
- `1 <= n <= 105`
- `1 <= nums[i] <= n`

**进阶：**你能在不使用额外空间且时间复杂度为 `O(n)` 的情况下解决这个问题吗? 你可以假定返回的数组不算在额外空间内。



## 解法一：记录

用一个数组记录每个元素的出现情况即可。

```c++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int n = nums.size();
        vector<int> f(n+1, 0), ans;

        for(int& i:nums){
            f[i] = 1;
        }

        for(int i=1; i<=n; i++){
            if(!f[i])   ans.push_back(i);
        }

        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：数组本身用于记录

为了达到空间复杂度为O(1)，可以使用数组本身用于记录，如果出现了对应的数字，将对应的数字加上`n`。

```c++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans;

        for(int& i:nums){
            int t = (i-1)%n;
            nums[t] += n;
        }

        for(int i=0; i<n; i++){
            if(nums[i]<=n)   ans.push_back(i+1);
        }

        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)