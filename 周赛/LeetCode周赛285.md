# LeetCode周赛285

## 题目一：[统计数组中峰和谷的数量](https://leetcode.cn/problems/count-hills-and-valleys-in-an-array/)

用一个变量标识当前是要找"峰"还是"谷"：如果当前状态要找"峰"，那么出现下降时，就表明出现峰值了，状态变化为找"谷"；如果当前状态要找"谷"，那么出现上升时，就表明出现谷底了，状态再变化为找"峰"。

```c++
class Solution {
public:
    int countHillValley(vector<int>& nums) {
        int ans = 0, flag = 0;   // 0为初始状态，1表示找"峰",2表示找"谷"
        for (int i = 1; i < nums.size(); i++) {
            if (nums[i] > nums[i-1]) {  // 上升
                if (flag == 2)  ans++;
                flag = 1;
            }
            if (nums[i] < nums[i-1]) {  // 下降
                if (flag == 1)  ans++;
                flag = 2;
            }
        }
        return ans;
    }
};
```

## 题目二：[统计道路上的碰撞次数](https://leetcode.cn/problems/count-collisions-on-a-road/)

这个题目可以从左到右判断，也可以从右向左遍历判断，这里以从左到右为例：

1. 如果此前没有出现过`R`和`S`，`L`都可以顺利的开走，因此利用一个变量`flag`来指示当前能否向左开；

2. 如果出现了正在向右开的车`R`，就需要统计其出现的次数，因为只要一旦出现`L`和`S`，就会产生"连环车祸"。

以上面两点为出发点，不难写出以下代码：

```c++
class Solution {
public:
    int countCollisions(string directions) {
        bool flag = true;   // 是否能向左行驶
        int  ans = 0, cntR = 0;   // 目前向右开的车辆数量
        for (auto c : directions) {
            if (c == 'R')   cntR++;
            if (c == 'S') {
                ans += cntR;
                cntR = 0;	
                flag = false;
            }
            if (c == 'L') {
                if (cntR > 0 || !flag) {
                    ans += cntR + 1;
                    cntR = 0;
                    flag = false;
                }
            }
        }
        return ans;
    }
};
```

## 题目三：[射箭比赛中的最大得分](https://leetcode.cn/problems/maximum-points-in-an-archery-competition/) *

**解法1（动态规划）**：当成动态规划问题来考虑，`f[i][k]`表示前i个区域用了K根箭能达到的最大分数，对于每个区域，有两种选择：① 要得这个分，那么用的箭就要大于`aliceArrows[i]`，状态转移`f[i][k] = f[i-1][k-aliceArrows[i]-1] + i`； ② 不得这个分，那么`f[i][k] = f[i-1][k]`。

最后还需要进行恢复，和背包问题一样，**通过判断`f[i][k] > f[i-1][k]`来判断bob是否拿了当前区域的分**。代码如下：

```c++
class Solution {
public:
    vector<int> maximumBobPoints(int numArrows, vector<int>& aliceArrows) {
        // f[i][k] 表示前i个区域用了k支箭得到的最大分数
        vector<vector<int>> f(12, vector<int>(numArrows+1, 0));
        
        for (int i = 1; i <= 11; i++) {
            int value = i, cost = aliceArrows[i];
            for (int k = 0; k <= numArrows; k++) {
                f[i][k] = f[i-1][k];
                if (k > cost) { // 可以得这个分
                    f[i][k] = max(f[i][k], f[i-1][k-cost-1] + value);
                }
            }
        }
        
        vector<int> ans(12, 0);
        int k = numArrows;
        for (int i = 11; i > 0; i--) {
            // 当前区域应该得分
            if (f[i][k] > f[i-1][k]) {
                ans[i] = aliceArrows[i] + 1;
                k -= aliceArrows[i] + 1;
            }
        }
        ans[0] = k;
        return ans;
    }
};
```

但这种解法的时间复杂度是O(n*k)，不如直接枚举状态来得快。

**解法2（枚举状态）**：一共只有12个区域，所以状态一共也就$2^{12}$种，直接枚举所有的状态，找到最大的得分状态即可。

```c++
class Solution {
public:
    vector<int> maximumBobPoints(int numArrows, vector<int>& aliceArrows) {
        int max_state, max_score = 0;
        
        // state表明bob的状态 (1表示要得分，0表示不得分)
        for (int state = 0; state < (1<<12); state++) {
            int score = 0, k = 0;
            for (int i = 0; i < 12; i++) {
                if ((1<<i) & state) {
                    score += i;
                    k += aliceArrows[i] + 1;
                }
            }
            if (k <= numArrows && score > max_score) {
                max_state = state;
                max_score = score;
            }
        }
        
        vector<int> ans(12, 0);
        int k = 0;
        for (int i = 1; i < 12; i++) {
            if ((1<<i) & max_state) {
                ans[i] = aliceArrows[i] + 1;
                k += ans[i];
            }
        }
        ans[0] = numArrows - k;
        return ans;
    }
};
```

## 题目四：[由单个字符重复的最长子字符串](https://leetcode.cn/problems/longest-substring-of-one-repeating-character/)

> 要用线段树解决，暂时不涉及