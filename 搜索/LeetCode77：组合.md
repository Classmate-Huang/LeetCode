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

如果掌握46题的思想，那么这题就不难了，只是对于的条件发生了改变而已。具体代码如下：

```c++
class Solution {
public:
    vector<vector<int> > ans;
    vector<int> res;

    void backtracking(int n, int k){
        // calculate start num 找到起始点
        int start = res.size()==0?1:res.back()+1;
        // pruning 进一步剪枝，即使剩下的元素全选也达不到k个
        if(n-start+1+res.size() < k)    return;
        // satisfy request 满足要求
        if(res.size()==k){
            ans.push_back(res);
            return ;
        }
        // DFS 搜索
        for(int i=start; i<=n; i++){
            res.push_back(i);
            backtracking(n, k);
            res.pop_back();
        }
    }

    vector<vector<int>> combine(int n, int k) {
        if(n<k) return {};
        backtracking(n, k);
        return ans;
    }
};
```

时间复杂度：O(C(n,k))，排列公式

空间复杂度：O(n)，临时数组res