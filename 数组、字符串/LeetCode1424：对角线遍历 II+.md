# LeetCode1424：对角线遍历 II

## 题目

给你一个列表 nums ，里面每一个元素都是一个整数列表。请你依照下面各图的规则，按顺序返回 nums 中对角线上的整数。


示例 1：

<img src='https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/04/23/sample_1_1784.png'>


```
输入：nums = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,4,2,7,5,3,8,6,9]
```
示例 2：

<img src='https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/04/23/sample_2_1784.png'>

```
输入：nums = [[1,2,3,4,5],[6,7],[8],[9,10,11],[12,13,14,15,16]]
输出：[1,6,2,8,7,3,9,4,12,10,5,13,11,14,15,16]
```
示例 3：
```
输入：nums = [[1,2,3],[4],[5,6,7],[8],[9,10,11]]
输出：[1,4,2,5,3,8,6,9,7,10,11]
```
示例 4：
```
输入：nums = [[1,2,3,4,5,6]]
输出：[1,2,3,4,5,6]
```

提示：
```
1 <= nums.length <= 10^5
1 <= nums[i].length <= 10^5
1 <= nums[i][j] <= 10^9
nums 中最多有 10^5 个数字。
```

## 解法一：根据坐标确定位置

可以很容易发现，在同一次对角线遍历中，所有的元素坐标下标(x, y)相加之后的值都是相等的，并且所有相等的下标和所处的位置上一定会出现在同一次遍历过程中。

因此，我们可以从上到下，从左到右依次遍历每个元素，按照其下标和放到对应的桶中，最后再依次从桶中取出对应的元素放入结果数组即可。需要注意的是，需要倒序放入结果中（因为题目是从↗的遍历方式）。代码如下：

```cpp
class Solution {
public:
    vector<int> findDiagonalOrder(vector<vector<int>>& nums) {
        vector<vector<int>> cnt(2e5+1); // 记录每个坐标下标和为(x+y)的元素
        for (int i = 0; i < nums.size(); i++) {
            for (int j = 0;  j < nums[i].size(); j++) {
                cnt[i+j].push_back(nums[i][j]);
            }
        }
        
        vector<int> ans;
        for (auto temp : cnt) {
            if (!temp.empty()) {
                for (int t = temp.size()-1; t >= 0; t--) {  //逆序
                    ans.push_back(temp[t]);
                }
            }
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(2e5)