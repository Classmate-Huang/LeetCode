# LeetCode496：下一个更大元素

## 题目

nums1 中数字 x 的 下一个更大元素 是指 x 在 nums2 中对应位置 右侧 的 第一个 比 x 大的元素。

给你两个 没有重复元素 的数组 nums1 和 nums2 ，下标从 0 开始计数，其中nums1 是 nums2 的子集。

对于每个 0 <= i < nums1.length ，找出满足 nums1[i] == nums2[j] 的下标 j ，并且在 nums2 确定 nums2[j] 的 下一个更大元素 。如果不存在下一个更大元素，那么本次查询的答案是 -1 。

返回一个长度为 nums1.length 的数组 ans 作为答案，满足 ans[i] 是如上所述的 下一个更大元素 。

 

示例 1：

```
输入：nums1 = [4,1,2], nums2 = [1,3,4,2].
输出：[-1,3,-1]
解释：nums1 中每个值的下一个更大元素如下所述：

- 4 ，用加粗斜体标识，nums2 = [1,3,4,2]。不存在下一个更大元素，所以答案是 -1 。
- 1 ，用加粗斜体标识，nums2 = [1,3,4,2]。下一个更大元素是 3 。
- 2 ，用加粗斜体标识，nums2 = [1,3,4,2]。不存在下一个更大元素，所以答案是 -1 。
```



示例 2：

```
输入：nums1 = [2,4], nums2 = [1,2,3,4].
输出：[3,-1]
解释：nums1 中每个值的下一个更大元素如下所述：

- 2 ，用加粗斜体标识，nums2 = [1,2,3,4]。下一个更大元素是 3 。
- 4 ，用加粗斜体标识，nums2 = [1,2,3,4]。不存在下一个更大元素，所以答案是 -1 。
```




提示：

```
1 <= nums1.length <= nums2.length <= 1000
0 <= nums1[i], nums2[i] <= 104
nums1和nums2中所有整数 互不相同
nums1 中的所有整数同样出现在 nums2 中
```


进阶：你可以设计一个时间复杂度为 O(nums1.length + nums2.length) 的解决方案吗？

## 解法一：单调栈

暴力的方法很容易过，对于nums1中每个元素都去找到nums中对应的元素，然后往后遍历得到最大的值即可。

这里介绍O(m+n)的方法，维护一个单调栈（递减），遍历`nums2`，如果当前元素比当前栈顶元素更大，则说明当前栈顶元素对应的结果找到了，将结果存在一个hash表中。然后再进行一次遍历，输出`nums1`每个元素对应的结果即可。代码如下：

```c++
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        vector<int> relation(1e4+5, -1);
        stack<int> stk;
        for(auto& num:nums2){
            while(!stk.empty() && num>stk.top()){
                relation[stk.top()] = num;
                stk.pop();
            }
            stk.push(num);
        }
        vector<int> ans;
        for(auto& i:nums1)  ans.push_back(relation[i]);
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)