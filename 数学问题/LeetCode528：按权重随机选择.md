# LeetCode528：按权重随机选择

## 题目

给定一个正整数数组 `w` ，其中 `w[i]` 代表下标 `i` 的权重（下标从 `0` 开始），请写一个函数 `pickIndex` ，它可以随机地获取下标 `i`，选取下标 `i` 的概率与 `w[i]` 成正比。



例如，对于 `w = [1, 3]`，挑选下标 `0` 的概率为 `1 / (1 + 3) = 0.25` （即，25%），而选取下标 `1` 的概率为 `3 / (1 + 3) = 0.75`（即，75%）。

也就是说，选取下标 `i` 的概率为 `w[i] / sum(w)` 。

 

**示例 1：**

```
输入：
["Solution","pickIndex"]
[[[1]],[]]
输出：
[null,0]
解释：
Solution solution = new Solution([1]);
solution.pickIndex(); // 返回 0，因为数组中只有一个元素，所以唯一的选择是返回下标 0。
```

**示例 2：**

```
输入：
["Solution","pickIndex","pickIndex","pickIndex","pickIndex","pickIndex"]
[[[1,3]],[],[],[],[],[]]
输出：
[null,1,1,1,1,0]
解释：
Solution solution = new Solution([1, 3]);
solution.pickIndex(); // 返回 1，返回下标 1，返回该下标概率为 3/4 。
solution.pickIndex(); // 返回 1
solution.pickIndex(); // 返回 1
solution.pickIndex(); // 返回 1
solution.pickIndex(); // 返回 0，返回下标 0，返回该下标概率为 1/4 。

由于这是一个随机问题，允许多个答案，因此下列输出都可以被认为是正确的:
[null,1,1,1,1,0]
[null,1,1,1,1,1]
[null,1,1,1,0,0]
[null,1,1,1,0,1]
[null,1,0,1,0,0]
......
诸若此类。
```

 

**提示：**

- `1 <= w.length <= 10000`

- `1 <= w[i] <= 10^5`

- `pickIndex` 将被调用不超过 `10000` 次

  

## 解法一：前缀和+二分

维护一个前缀和数组，每个数组的内的值为当前的前缀和。权重和为`sum`，在`0~sum`之间随机采样某个数x，只要找到x在前缀和数组中对应的区域返回索引即可。在查找索引采用二分法，加速算法。

比如[1,2,3]得到前缀和数组为[1,3,6]，在`0~5`之间随机采样一个数，如果为5，那么在[3,6)的区间内，对应下标为`2`。

```c++
class Solution {
public:
    vector<int> idx;
    int sum = 0;

    Solution(vector<int>& w) {
        for(auto& i:w){ // 前缀和数组
            sum += i;
            idx.push_back(sum);
        }
    }
    
    int pickIndex() {
        int p = rand()%sum;
        int left = 0, right = idx.size()-1;
        
        while(left<=right){  // 二分
            int mid = (left+right)/2;
            if(idx[mid]<p)
                left = mid+1;
            else if(idx[mid]==p)
                return mid+1;
            else if(idx[mid]<p && (mid==0 || p>=idx[mid-1]))
                return mid;
            else
                right = mid-1; 
        }

        return left;
    }
};
```

时间复杂度：O(logn)

空间复杂度：O(n)