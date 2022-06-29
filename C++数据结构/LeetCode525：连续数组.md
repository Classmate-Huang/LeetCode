# LeetCode525：连续数组

## 题目

给定一个二进制数组 nums , 找到含有相同数量的 0 和 1 的最长连续子数组，并返回该子数组的长度。

 

示例 1:

```
输入: nums = [0,1]
输出: 2
说明: [0, 1] 是具有相同数量 0 和 1 的最长连续子数组。
```

示例 2:

```
输入: nums = [0,1,0]
输出: 2
说明: [0, 1] (或 [1, 0]) 是具有相同数量0和1的最长连续子数组。
```


提示：

```
1 <= nums.length <= 105
nums[i] 不是 0 就是 1
```

## 解法一：前缀和+哈希表

此题与"和为k的子数组"解法类似：

* 一次遍历，用hash表维护**0,1差值为t**时第一次出现的索引位置（初始化`map[0] = -1;`；
* 统计当前位置`j`的0和1的前缀和`cnt0, cnt1`，计算差值`t = cnt0 - cnt1`。要找到当前位置`j`结尾的最长满足条件的子数组，就要找到在之前数组中，第一次出现`t`的索引位置`i`，那么`j-i`即为子数组长度。

还有一种思路就是将0是为-1，那么这个问题就转换成了和为0的最长子数组长度。代码如下：

```c++
class Solution {
public:
    int findMaxLength(vector<int>& nums) {
        int n = nums.size(), cnt0 = 0, cnt1 = 0;
        unordered_map<int, int> myMap; // key:0-1差值, value:索引
        myMap[0] = -1;
        
        int ans = 0;
        for (int i = 0; i < n; i++) {
            // 统计nums[0:i]中0和1的个数
            if (nums[i] == 0)   cnt0++;
            else    cnt1++;
            // 计算它们之前的差值，在hash表中找到第一个出现的索引
            if (myMap.count(cnt0-cnt1)) {
                ans = max(ans, i-myMap[cnt0-cnt1]);
            }
            else    myMap[cnt0-cnt1] = i;
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)