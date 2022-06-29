# LeetCode77：组合

## 题目

给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合。

**示例:**

```
输入: n = 4, k = 2
输出:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

## 解法一：回溯

跟排列数不同，对于组合来说，不需要考虑他们之间的位置关系。因此，对于每个数字我们只需要考虑“选” or “不选”，具体代码如下：

```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> res;

    void dfs(int i, int n, int k){
        if(res.size() == k){
            ans.push_back(res);
            return;
        }
        if(i > n)  return ;
        // 两种选择
        res.push_back(i);
        dfs(i+1, n, k); // 选
        res.pop_back();
        dfs(i+1, n, k); // 不选
    }

    vector<vector<int>> combine(int n, int k) {
        dfs(1, n, k);
        return ans;
    }
};
```

时间复杂度：O(C(n,k))，排列公式

空间复杂度：O(n)，临时数组res