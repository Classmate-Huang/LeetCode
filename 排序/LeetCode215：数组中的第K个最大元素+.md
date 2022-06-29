# LeetCode215：数组中的第K个最大元素

## 题目

在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

**示例 1:**

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

**示例 2:**

```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```

**说明:**

你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

## 解法一：快排

最简单的解法，现将数组变为有序数组，然后检索其第k大的元素即可：

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        return nums[n-k];
    }
};
```

时间复杂度：O(nlogn)

空间复杂度：O(logn)

## 解法二：快速选择

由于我们只需要检索第K大的元素，对所有元素进行排序显然浪费了时间。关于找第K大/小元素，有一个基于快速排序改进的算法：快速选择，其时间复杂度为O(n)。

简单介绍其思路，在进行快速排序时我们首先会找到一个基准点p，将小于p的值放置在左边，大于p的值放置在右边。那么快速选择的思路就是，我每次判断p点是不是我要找的第k大元素，如果是直接返回，如果不是则选择左右两个区间中的一个进行递归即可。代码如下：

```c++
class Solution {
public:
    int quickSelect(vector<int>& nums, int left, int right, int k){
        swap(nums[left], nums[rand()%(right-left+1)+left]);
        int target = nums[left], lBound = left, rBound = right, i = left;
        // 荷兰国旗
        while(i <= rBound){
            if(nums[i]==target) i++;
            else if(nums[i]>target) swap(nums[i++], nums[lBound++]);
            else    swap(nums[i], nums[rBound--]);
        }
        // 二分
        if(k-1 < lBound)    return quickSelect(nums, left, lBound-1, k);
        else if(k-1 > rBound)   return quickSelect(nums, rBound+1, right, k);
        else return nums[k-1];
    }

    int findKthLargest(vector<int>& nums, int k) {
        return quickSelect(nums, 0, nums.size()-1, k);
    }
};
```

时间复杂度：O(n)

空间复杂度：O(logn)