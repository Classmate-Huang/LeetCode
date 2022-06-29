# LeetCode128：最长连续序列

## 题目

给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 `O(n)` 的算法解决此问题。

 

**示例 1：**

```
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

**示例 2：**

```
输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
```

 

**提示：**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`



## 解法一：集合

首先把所有数据放到集合中，然后再重新遍历数组元素。对于每个元素`t`，递归检查`t++`和`t--`是否在集合中，如果在集合中说明存在相应的连续序列，同时在集合中删除已遍历过的元素【避免重复遍历】。代码如下：

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> m;
        for(int& i:nums){
            m.insert(i);
        }
        int ans = 0;
        for(int i=0; i<nums.size(); i++){
            int t = nums[i], len = 0;
            // 往右遍历 t++
            while(m.find(t)!=m.end()){
                m.erase(t);
                len++;
                t++;
            }
            // 往左遍历 t--
            t = nums[i]-1;
            while(m.find(t)!=m.end()){
                m.erase(t);
                len++;
                t--;
            }
            // 更新结果
            ans = len>ans ? len : ans;
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)