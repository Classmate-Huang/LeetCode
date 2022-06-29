# LeetCode912：排序数组

## 题目

给你一个整数数组 `nums`，请你将该数组升序排列。

**示例 1：**

```
输入：nums = [5,2,3,1]
输出：[1,2,3,5]
```

**示例 2：**

```
输入：nums = [5,1,1,2,0,0]
输出：[0,0,1,1,2,5]
```

**提示：**

1. `1 <= nums.length <= 50000`
2. `-50000 <= nums[i] <= 50000`

## 解法一：归并排序

归并排序是一种经典的“分治”算法，其主要将待排序数组分为左右两部分，将左右两部分分别排序后，再进行归并融合。利用递归可以轻松地实现：

```c++
class Solution {
public:
    void merge(vector<int>&nums, int left, int mid, int right){ // 合并两个有序数组
        vector<int> L(nums.begin()+left, nums.begin()+mid+1);
        vector<int> R(nums.begin()+mid+1, nums.begin()+right+1);
        int i = 0, j = 0; 
        for(int t=left; t<=right; t++){
            if(i<L.size() && (j==R.size() || L[i]<=R[j])){
                nums[t] = L[i++];
            }
            else{
                nums[t] = R[j++];
            }
        }
    }
    void divide(vector<int>& nums, int left, int right){ // 分治递归
        if(left==right){
            return;
        }
        int mid = left+(right-left)/2;
        divide(nums, left, mid);
        divide(nums, mid+1, right);
        
        if(nums[mid]>=nums[mid+1]){
            merge(nums, left, mid, right);
        }
    }
    vector<int> sortArray(vector<int>& nums) {
        int n = nums.size();
        divide(nums, 0, n-1);
        return nums;
    }
};
```

时间复杂度：O(nlogn)

空间复杂度：O(n)

> 更多排序方法：https://blog.csdn.net/qq_36560894/article/details/114993113