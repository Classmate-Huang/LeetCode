# 剑指offer 51：数组中的逆序对

## 题目

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。



示例 1:

```
输入: [7,5,6,4]
输出: 5
```


限制：

```
0 <= 数组长度 <= 50000
```



## 解法一：归并排序

在归并排序中的`merge`操作中，每次遇到左边元素大于右边元素时就可以累积一次逆序对数量。代码如下：

```c++
class Solution {
public:
    // 利用归并排序思想求逆序对
    int process(vector<int>& nums, int L, int R){
        if(L >= R)  return 0;
        int mid = (R - L) / 2 + L;
        return process(nums, L, mid) + process(nums, mid+1, R) + merge(nums, L, R, mid);
    }

    // 归并排序，顺便求逆序对（左边有多少个数比当前元素大）
    int merge(vector<int>& nums, int L, int R, int M){
        vector<int> help(R-L+1);
        int i = 0, p1 = L, p2 = M+1, ans = 0;
        while(p1<=M && p2<=R){
            if(nums[p1] > nums[p2])     ans += M-p1+1;
            help[i++] = nums[p1] <= nums[p2] ? nums[p1++] : nums[p2++];
        }
        while(p1<=M){
            help[i++] = nums[p1++];
        }
        while(p2<=R){
            help[i++] = nums[p2++];
        }
        for(i=0; i<R-L+1; i++){
            nums[L+i] = help[i];
        }
        return ans;
    }

    int reversePairs(vector<int>& nums) {
        return process(nums, 0, nums.size()-1);
    }
};
```

时间复杂度：O(nlogn)

空间复杂度：O(n)