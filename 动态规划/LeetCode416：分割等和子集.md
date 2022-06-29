# LeetCode416：分割等和子集

## 题目

给你一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

 

**示例 1：**

```
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

**示例 2：**

```
输入：nums = [1,2,3,5]
输出：false
解释：数组不能分割成两个元素和相等的子集。
```

**提示：**

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`

## 解法一：0-1背包问题

将问题转换为0-1背包问题，首先确定数组和为偶数，这样才能会分为两部分。可以视作一个容量为`sum/2`的背包，是否能够存在能装满它的策略（也可以视作价值与重量相等，装载价值为`sum/2`的策略）。代码如下：

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size();
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if(sum%2==1){
            return false;
        }
        
        int w = sum/2;
        vector<vector<int>> f(n+1, vector<int>(w+1, 0));
        for(int i=1; i<=n; i++){
            int tw = nums[i-1], tv = nums[i-1];
            for(int j=1; j<=w; j++){
                if(j>=tw){
                    f[i][j] = max(f[i-1][j], f[i-1][j-tw]+tv);
                }
                else{
                    f[i][j] = f[i-1][j];
                }
            }
        }
        if(f[n][w]==w)  return true;
        return false;
    }
};
```

时间复杂度：O(MN)，M为nums长度，N为`sum/2`，此问题为NP问题。

空间复杂度：O(MN)



## 解法二：0-1背包-空间压缩

0-1背包问题的空间压缩版本，注意内层循环需要逆向遍历。

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size();
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if(sum%2==1){
            return false;
        }
        
        int w = sum/2;
        vector<int> f(w+1, 0);
        for(int i=1; i<=n; i++){
            int tw = nums[i-1], tv = nums[i-1];
            for(int j=w; j>=tw; j--){
                f[j] = max(f[j], f[j-tw]+tv);
            }
        }
        if(f[w]==w)  return true;
        return false;
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(N)

