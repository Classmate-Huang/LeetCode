# LeetCode560：和为K的子数组

## 题目：

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回该数组中和为 `k` 的连续子数组的个数。

 

**示例 1：**

```
输入：nums = [1,1,1], k = 2
输出：2
```

**示例 2：**

```
输入：nums = [1,2,3], k = 3
输出：2
```

 

**提示：**

- `1 <= nums.length <= 2 * 104`
- `-1000 <= nums[i] <= 1000`
- `-107 <= k <= 107`



## 解法一：前缀和 + 哈希表

题目要求找到连续和为k的连续数组个数，这个搜索空间其实非常大，直接暴搜复杂度将达到O($n^3$)。对于`nums[i:j]`的求和操作可以用前缀和`pre_sum`达到O(1)的复杂度，但遍历起始点和结束点仍需要O($n^2$)复杂度，因此仍会超时。

> 前缀和见303题

回看这个问题的本质，实际上是要求对于某对(i,j)`0<=i<j=n`，`pre_sum[j]-pre_sum[i]==k`是否成立。实际上也是判断`pre_sum[j]-k==pre_sum[i]`是否成立，且`i<j`。由于顺序遍历，因此可以把所有`pre_sum[i]`值以及出现的次数存到hash表中，然后判断`pre_sum[j]-k`是否出现在hash表中，如果存在则更新ans，最后在更新hash表的值。详见代码：

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n = nums.size(), ans = 0;
        unordered_map<int, int> m = {{0, 1}};  // 记录[前缀和, 次数]
        vector<int> pre_sum(n+1, 0); // 前缀和
        for(int j=1; j<=n; j++){
            pre_sum[j] = pre_sum[j-1] + nums[j-1]; // 计算前缀和
            int t = pre_sum[j] - k;
            if(m.find(t) != m.end()){	// 说明存在满足条件的pre_sum[i], 更新次数
                ans += m[t];
            }
            m[pre_sum[j]]++;  // 更新hash
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)， 哈希表和前缀和

**小改进**：其实在后续遍历的过程中并不需要用到`pre_sum[i]`的结果，因此可以进行空间压缩，如下：

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n = nums.size(), ans = 0;
        unordered_map<int, int> m = {{0, 1}};
        int pre_sum = 0;

        for(int j=1; j<=n; j++){
            pre_sum += nums[j-1];
            int t = pre_sum - k;
            if(m.find(t) != m.end()){
                ans += m[t];
            }
            m[pre_sum]++;
        }
        
        return ans;
    }
};
```

