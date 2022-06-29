# LeetCode974：和可被K整除的子数组

## 题目

给定一个整数数组 nums 和一个整数 k ，返回其中元素之和可被 k 整除的（连续、非空） 子数组 的数目。

子数组 是数组的 连续 部分。

 

示例 1：

```
输入：nums = [4,5,0,-2,-3,1], k = 5
输出：7
解释：
有 7 个子数组满足其元素之和可被 k = 5 整除：
[4, 5, 0, -2, -3, 1], [5], [5, 0], [5, 0, -2, -3], [0], [0, -2, -3], [-2, -3]
```

示例 2:

```
输入: nums = [5], k = 9
输出: 0
```


提示:

```
1 <= nums.length <= 3 * 104
-104 <= nums[i] <= 104
2 <= k <= 104
```

## 解法一：hash +  同余定理

此题做法和"和为K的子数组"一致，只不过统计的不在是前缀和，而是前缀和 % k：

* 维护前缀和`preSum`与hash表`map`（记录在之前位置中，前缀和%k==t出现的次数，初始化`map[0] = 1`）；

* 在`j`位置上记录前缀和`preSum`，我们要判断以当前位置结尾有多少个整除的组合，先计算`t = preSum % k`，查询`map`中的记录即可。

```c++
class Solution {
public:
    int subarraysDivByK(vector<int>& nums, int k) {
        int n = nums.size(), preSum = 0;
        unordered_map<int, int> myMap;	// [余数，次数]
        myMap[0] = 1;
        
        int ans = 0;
        for (int i = 0; i < n; i++) {
            preSum += nums[i];
            int t = (preSum % k + k) % k;	// 取余操作
            if (myMap.find(t) != myMap.end())   ans += myMap[t];
            myMap[t]++;
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)