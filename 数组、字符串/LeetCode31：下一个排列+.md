# LeetCode31：下一个排列

整数数组的一个 排列  就是将其所有成员以序列或线性顺序排列。

例如，arr = [1,2,3] ，以下这些都可以视作 arr 的排列：[1,2,3]、[1,3,2]、[3,1,2]、[2,3,1] 。
整数数组的 下一个排列 是指其整数的下一个字典序更大的排列。更正式地，如果数组的所有排列根据其字典顺序从小到大排列在一个容器中，那么数组的 下一个排列 就是在这个有序容器中排在它后面的那个排列。如果不存在下一个更大的排列，那么这个数组必须重排为字典序最小的排列（即，其元素按升序排列）。

例如，arr = [1,2,3] 的下一个排列是 [1,3,2] 。
类似地，arr = [2,3,1] 的下一个排列是 [3,1,2] 。
而 arr = [3,2,1] 的下一个排列是 [1,2,3] ，因为 [3,2,1] 不存在一个字典序更大的排列。
给你一个整数数组 nums ，找出 nums 的下一个排列。

必须 原地 修改，只允许使用额外常数空间。

 

示例 1：
```
输入：nums = [1,2,3]
输出：[1,3,2]
```
示例 2：
```
输入：nums = [3,2,1]
输出：[1,2,3]
```
示例 3：
```
输入：nums = [1,1,5]
输出：[1,5,1]
```

提示：
```
1 <= nums.length <= 100
0 <= nums[i] <= 100
```

## 解法一：找规律

对于某个排列，我们需要找到其下一个排列。首先，我们要找到一组数对(i, j)，满足`nums[i] < nums[j]`，并且i要足够靠右，nums[j]要足够小。找到这个数对后，我们进行交换`swap(nums[i], nums[j])`，然后让i+1后的所有数据变得有序。

例如:[6,4,3,5,4,1]，找到的数对就是(2, 4)，交换nums[2]和nums[4]，得到[6,4,4,5,3,1]。然后将需要保持nums[2+1:]有序，得到[6,4,4,1,3,5]，即为我们需要的结果。

具体地，为了得到数对(i,j)，我们设置两个指针从后往前遍历，找到满足条件的第一个数对即为结果。然后对于有序的实现，可以发现，在我们问题的定义中，**只需要对i+1位置后的元素进行一次翻转即可**（因为nums[i+1:n]之间一定是递减的，省去证明，可以自己思考一下）。

代码如下：

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = nums.size();
        for (int i = n-1; i >= 0; i--) {
            for (int j = n-1; j > i; j--) {
                // 找到第一个满足条件的组合
                if (nums[i] < nums[j]) {
                    swap(nums[i], nums[j]);
                    reverse(nums.begin()+i+1, nums.end());
                    return;
                }
            }
        }
        reverse(nums.begin(), nums.end());
    }
};
```
时间复杂度：O(n)
空间复杂度：O(1)

> 如果题目要求是找上一个排列，那么只需要将 `<` 变成 `>` 即可
