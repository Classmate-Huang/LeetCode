# LeetCode473：火柴拼正方形

## 题目

你将得到一个整数数组 `matchsticks` ，其中 `matchsticks[i]` 是第 `i` 个火柴棒的长度。你要用 **所有的火柴棍** 拼成一个正方形。你 **不能折断** 任何一根火柴棒，但你可以把它们连在一起，而且每根火柴棒必须 **使用一次** 。

如果你能使这个正方形，则返回 `true` ，否则返回 `false` 。

 

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/04/09/matchsticks1-grid.jpg)

```
输入: matchsticks = [1,1,2,2,2]
输出: true
解释: 能拼成一个边长为2的正方形，每边两根火柴。
```

**示例 2:**

```
输入: matchsticks = [3,3,3,3,4]
输出: false
解释: 不能用所有火柴拼成一个正方形。
```

 

**提示:**

- `1 <= matchsticks.length <= 15`
- `1 <= matchsticks[i] <= 108`

## 解法一：回溯

将问题抽象成分成四个集合，每个集合的数目相等。利用回溯的思想，考虑第t根火柴放在第i个盒子中的情况，找到一种可行解。需要一些优化策略：① 实现判断总和是否能被4整除；② 从大到小排列火柴，优化考虑火柴大的情况（减去很多支路，非常重要！）；③ 去掉一些重复情况，如果两个盒子相等，前一个盒子如果放入火柴t不满足条件，那么放到后一个盒子中也仍不会满足条件。

代码如下：

```c++
class Solution {
public:

    bool dfs(vector<int>& matchsticks, vector<int>& res, int target, int t){
        // 遍历完一个过程
        if(t==matchsticks.size())   
            return res[0] == res[1] && res[1] == res[2] && res[2] == res[3];
        
        bool flag = false;
        for(int i=0; i<4; i++){
            // 如果当前状态和上一状态一致，直接跳过
            if( i > 1 && res[i] == res[i-1])    continue;
            // 修改当前状态
            res[i] += matchsticks[t];
            // 如果大于target，直接跳过
            if(res[i] <= target)   flag = dfs(matchsticks, res, target, t+1);
            // 找到一种满足情况
            if(flag)    return true;
            // 回溯
            res[i] -= matchsticks[t];
        }

        return flag;
    }

    static bool compare(const int& a, const int& b){
        return a > b;
    }

    bool makesquare(vector<int>& matchsticks) {
        vector<int> res(4, 0);  // 四个集合
        
        int sum = 0;    // 求和
        for(auto& stick:matchsticks){
            sum += stick;
        }
        if(sum%4 != 0)  return false;   // 无法被整除
        int target = sum / 4;     // 每个集合所需的目标值

        sort(matchsticks.begin(), matchsticks.end(), compare);  // 先放大的
        return dfs(matchsticks, res, target, 0);

    }
};
```

时间复杂度：O($4^n$)

空间复杂度：O(n)

## 解法二：状态压缩dp

将数组中每个元素是否被选择由二进制来表示，1表示被选择，0表示没有选择。遍历所有状态即可：

> 具体解法见题698，这是简化版

```c++
class Solution {
public:
    bool makesquare(vector<int>& matchsticks) {
        int n = matchsticks.size();
        
        int sum = 0;
        for(auto& i:matchsticks)    sum += i;
        if(sum % 4 != 0)    return false;
        int target = sum / 4;
        
        vector<int> f(1<<n, -1);
        f[0] = 0;
        for(int mask=0; mask<(1<<n); mask++){
            if(f[mask]==-1)    continue;   // 无法到达的状态
            for(int j=0; j<n; j++){
                if((mask&(1<<j))==0 && f[mask] + matchsticks[j] <= target)
                    f[mask | (1<<j)] = (f[mask] + matchsticks[j]) % target;
            }
        }

        return f[(1<<n)-1] == 0;
    }
};
```

时间复杂度：O($n*2^n$)

空间复杂度：O($2^n$)