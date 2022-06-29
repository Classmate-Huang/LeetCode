# LeetCode698：划分为k个相等的子集

## 题目

给定一个整数数组  nums 和一个正整数 k，找出是否有可能把这个数组分成 k 个非空子集，其总和都相等。

 

示例 1：

```
输入： nums = [4, 3, 2, 3, 5, 2, 1], k = 4
输出： True
说明： 有可能将其分成 4 个子集（5），（1,4），（2,3），（2,3）等于总和。
```

示例 2:

```
输入: nums = [1,2,3,4], k = 3
输出: false
```


提示：

* 1 <= k <= len(nums) <= 16

* 0 < nums[i] < 10000

* 每个元素的频率在 [1,4] 范围内

## 解法一：回溯

太暴力了，太暴力了，必须考一些trick剪枝才能过：① 将元素从大到小排序，优先放大的；② 排除重复情况的遍历[如果两个相邻集合的值相等，A元素放在前一个集合中不满足要求，那么一定不能放到后一个集合中]；

> 这种做法与473题思想一致

```c++
class Solution {
public:
    bool dfs(vector<int>& nums, vector<int>& res, int target, int t){
        if(t == nums.size()){	// 已遍历完一种情况
            for(auto& temp:res){
                if(temp != target)  return false;
            }
            return true;
        }
		// DFS回溯
        bool flag = false;
        for(int i=0; i<res.size(); i++){
            if(i>1 && res[i]==res[i-1]) continue;	// 剪枝
            res[i] += nums[t];
            if(res[i] <= target) flag = dfs(nums, res, target, t+1);
            if(flag)    return true;
            res[i] -= nums[t];	// 回到前一种状态
        }

        return flag;
    }

    static bool compare(const int& a, const int& b){
        return a > b;
    }

    bool canPartitionKSubsets(vector<int>& nums, int k) {
        if(k == 1)  return true;        
        vector<int> res(k, 0);
        
        int sum = 0;
        for(int& i:nums)    sum += i;
        if(sum%k != 0)  return false;	// 剪枝
        int target = sum / k;
        
        sort(nums.begin(), nums.end(), compare);	// 排序
        return dfs(nums, res, target, 0);
    }
};
```

时间复杂度：O($k·n^k$)，所以不剪枝是绝对过不了的

空间复杂度：O(k)

## 解法二：状态压缩+DP

利用二进制表示每个元素状态：0表示不选择，1表示选择。数组长度为n，那么有$2^n$种状态，$dp[i]$表示在状态`i`的情况下，数组能否分成若干个和为`target`（sum/k）的子数组，所以最终的$dp[2^n-1]$即为结果（把所有数都选上）。

这里使用了一个技巧，也就是`dp[i]`表示的是：**当前状态求和之后%target的余数**，除了`dp[0]`以外，其余状态%target等于的0话，就表明**一定存在某种划分**满足条件。

状态转移方程：$dp[mask|(1<<i)]=(dp[mask]+nums[i])\%target$，但首先要确保$dp[mask]+nums[i]$是小于target的(`nums[i]不能拆分成两部分`)，并且元素i在mask状态下应该没有选择的。

```c++
class Solution {
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        int n = nums.size();

        int sum = 0;
        for(auto& i:nums)   sum += i;
        if(sum%k != 0)  return false;   // 剪枝
        int target = sum / k;

        vector<int> f(1<<n, -1);    // f[i]表示当前i状态能否划分成若干个值为target的子集
        f[0] = 0;   // 边界条件
        // 状态压缩dp
        for(int mask=0; mask<(1<<n); mask++){
            if(f[mask]==-1) continue;
            for(int j=0; j<n; j++){
                if((mask&(1<<j))==0 && f[mask]+nums[j]<=target){ // 元素j之前并没有选 and 和为target
                    f[mask | (1<<j)] = (f[mask] + nums[j]) % target;
                }
            }
        }
        return f[(1<<n) - 1] == 0;
    }
};
```

时间复杂度：O($n*2^n$)，无需特殊的剪枝即可通过

空间复杂度：O($2^n$)

> 题解参考：https://leetcode-cn.com/problems/partition-to-k-equal-sum-subsets/solution/wai-zhan-ban-yun-zi-ji-li-jie-by-amazing-u1wh/