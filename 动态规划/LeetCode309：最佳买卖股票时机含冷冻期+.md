# LeetCode309：最佳买卖股票时机含冷冻期

## 题目

给定一个整数数组，其中第 *i* 个元素代表了第 *i* 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**示例:**

```
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

## 解法一：DP

股票问题的一种特殊情况，在188的基础上，考虑卖出后的冷冻期。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int k = n / 2;
        vector<vector<int>> buy(n+1, vector<int>(k+1, INT_MIN));
        vector<vector<int>> sell(n+1, vector<int>(k+1, 0));

        for(int i=1; i<=n; i++){
            for(int j=1; j<=k; j++){
                if(i>=2){  // 在2天开始时，可存在卖出操作（考虑冷冻期）
                    buy[i][j] = max(buy[i-1][k], sell[i-2][j-1]-prices[i-1]);
                }
                else{  // 第1天，只能买，不能卖
                    buy[i][j] = max(buy[i-1][k], 0-prices[i-1]);
                }
                sell[i][j] = max(sell[i-1][j], buy[i-1][j]+prices[i-1]);
            }
        }
        
        return sell[n][k];
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O($n^2$)

## 解法二：更简单的DP

虽然由通解构成的解法一比较简单，但时间、空间复杂度还是比较高。我们可以考虑一种更简单的动态规划的思路，因为有无穷多次交易的机会，所以我们可以不考虑`k`交易次数的影响。`buy[n][0]`表示持有股票状态，`sell_coll[n]`表示不持有股票状态且处于冷冻期，`sell[n]`表示不持有股票状态且处于非冷冻期。



```c++

```

