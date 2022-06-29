# LeetCode188：买卖股票的最佳时机 IV

## 题目

给定一个整数数组 `prices` ，它的第 `i` 个元素 `prices[i]` 是一支给定的股票在第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 **k** 笔交易。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

**示例 1：**

```
输入：k = 2, prices = [2,4,1]
输出：2
解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。
```

**示例 2：**

```
输入：k = 2, prices = [3,2,6,5,0,3]
输出：7
解释：在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。
     随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。
```

 

**提示：**

- `0 <= k <= 100`
- `0 <= prices.length <= 1000`
- `0 <= prices[i] <= 1000`



## 解法一：DP

构造`buy`和`sell`两个数组，分别视为“买入股票”和“卖出股票”操作，大小均为`[n][k]`。表示n天和k次交易，`buy[i][k]`表示在第i天最多交易k次的情况下，手中**所持股票数为1**的最大收益（也就是说`buy[i][k]`是维持着**持有股票**的状态），相反`sell[i][k]`表示在第i天最多交易k次的情况下，手中**所持股票数为0**的最大收益（维持**无持有股票**状况）。

转换方程为：① `buy[i][k]=max(buy[i-1][k], sell[i-1][k-1]-price[i])`，要么维持之前的状态不变，要么就是在之前`k-1`交易已结束的基础上再购入当天的股票(只有`buy`操作会影响交易次数)；② `sell[i][k]=max(sell[i-1][k], buy[i-1][k]+price[i])`，要么维持之前的状态，要么就是在当天结束之前的交易。

```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> buy(n+1, vector(k+1, 0));
        vector<vector<int>> sell(n+1, vector(k+1, 0));
        // boundary
        for(int i=0; i<=n; i++){
            buy[i][0] = INT_MIN;  // 0次交易无法持有股票
        }
        for(int j=0; j<=k; j++){
            buy[0][j] = INT_MIN;  // 在第1天开始前，无法持有股票
        }
        // DP
        for(int i=1; i<=n; i++){
            for(int j=1; j<=k; j++){
                sell[i][j] = max(sell[i-1][j], buy[i-1][j]+prices[i-1]);
                buy[i][j] = max(buy[i-1][j], sell[i-1][j-1]-prices[i-1]);
            }
        }

        return sell[n][k];  // 易得，sell[n][k] > buy[n][k]
    }
};
```

时间复杂度：O(nk)

空间复杂度：O(nk)

## 解法二：空间压缩

可以看出来，每一天都之和前一天的状态相关，因此可以采用状态压缩：

```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        vector<int> buy(k+1, INT_MIN);
        vector<int> sell(k+1, 0);

        // DP
        for(int i=1; i<=n; i++){
            for(int j=1; j<=k; j++){
                buy[j] = max(buy[j], sell[j-1]-prices[i-1]);
                sell[j] = max(sell[j], buy[j]+prices[i-1]);
            }
        }

        return sell[k];
    }
};
```

时间复杂度：O(nk)

空间复杂度：O(k)

