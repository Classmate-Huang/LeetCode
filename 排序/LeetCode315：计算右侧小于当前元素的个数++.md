# LeetCode315：计算右侧小于当前元素的个数

## 题目

给你一个整数数组 nums ，按要求返回一个新数组 counts 。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。

 

示例 1：

```
输入：nums = [5,2,6,1]
输出：[2,1,1,0] 
解释：
5 的右侧有 2 个更小的元素 (2 和 1)
2 的右侧仅有 1 个更小的元素 (1)
6 的右侧有 1 个更小的元素 (1)
1 的右侧有 0 个更小的元素
```

示例 2：

```
输入：nums = [-1]
输出：[0]
```

示例 3：

```
输入：nums = [-1,-1]
输出：[0,0]
```


提示：

```
1 <= nums.length <= 105
-104 <= nums[i] <= 104
```

## 解法一：归并排序 - 逆序对

这个题目一个经典的**逆序对**问题，逆序对问题可以使用归并排序解决：在归并排序的"合并过程"中，得到两个有序数组，可以很容易判断**左子数组中有多少个数大于右子数组的某个元素** or **右子数组中有多少个数大于左子数组的某个元素**这类问题。

因为题目可能存在多个相同元素，为了方便处理，我们将每个元素和它对应的索引封装成一个结构体`node`，这样每次记录右边有多少个元素大于它的时候就可以很方便直接记录在对应的索引位置上了。然后构造一个`vector<int> ans`来记录每个初始索引对应的元素右边有多少个小于它的数字。

这里由于我们需要求得每个元素右边有多少个小于它的数字，我们这里采用递减排序。并在合并的时候，优先移动左子数组的指针，当出现`nums[p1].val > nums[p2].val`的时候，对于`nums[p1]`这个元素来说，其右边就有`r-p2+1`个小于它的数字，累加在`ans[nums[p1].idx]`中。

代码如下：

```c++
struct node{
    int idx;
    int val;
    node(int idx, int val) : idx(idx), val(val) {};
};

class Solution {
public:
    vector<int> ans;
    
    // 降序排序 nums[l : mid]  nums[mid+1 : r]  递减序列
    void merge(vector<node>& newnums, int l, int mid, int r) {
        vector<node> help(r-l+1, node(0, 0));
        int p1 = l, p2 = mid+1, i = 0;
        while (p1 <= mid && p2 <= r) {
            if (newnums[p1].val > newnums[p2].val)  ans[newnums[p1].idx] += r - p2 + 1;
            help[i++] = newnums[p1].val > newnums[p2].val ? newnums[p1++] : newnums[p2++];
        }
        while (p1 <= mid)   help[i++] = newnums[p1++];
        while (p2 <= r)   help[i++] = newnums[p2++];
        for (int i = 0; i < r-l+1; i++) {
            newnums[i+l] = help[i];
        }
    }
    
    void mergeSort(vector<node>& newnums, int l, int r) {
        if (l >= r) return ;
        int mid = (l + r) / 2;
        mergeSort(newnums, l, mid);
        mergeSort(newnums, mid+1, r);
        merge(newnums, l, mid, r);
    }
    
    vector<int> countSmaller(vector<int>& nums) {
        int n = nums.size();
        ans = vector<int> (n, 0);
        // 转换成新节点 - 记录索引与值
        vector<node> newnums;
        for (int i = 0; i < n; i++) {
            node t = node(i, nums[i]);
            newnums.push_back(t);
        }
        // 求逆序对
        mergeSort(newnums, 0, n-1);
        return ans;
    }
};
```

时间复杂度：O(nlogn)

空间复杂度：O(n)