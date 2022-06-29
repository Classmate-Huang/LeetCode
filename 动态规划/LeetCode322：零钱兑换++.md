# LeetCode322：零钱兑换

## 题目

给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。

计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。

你可以认为每种硬币的数量是无限的。

 

**示例 1：**

```
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```

**示例 2：**

```
输入：coins = [2], amount = 3
输出：-1
```

**示例 3：**

```
输入：coins = [1], amount = 0
输出：0
```

**示例 4：**

```
输入：coins = [1], amount = 1
输出：1
```

**示例 5：**

```
输入：coins = [1], amount = 2
输出：2
```

 

**提示：**

- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 231 - 1`
- `0 <= amount <= 104`

## 解法一：完全背包问题

每种纸币就代表一类物体，每种物体可以拿`0~无限`次，重量为其本身的币值，价值为1，背包容量为`amonut`。在满足正好装满背包的情况下，价值最小的情况。

与标准的完全背包问题不一样的地方在于：① 需要求价值最小值的装载策略；② 背包容量必须装满。考虑未空间压缩的代码，首先背包内价值初始化均为`10e5`，这是一个一定大于amount的数，这样方便比较最小值。其次，在背包容量为0时价值为0，一方面是为了保证后续出现正好装满背包的情况下，能够正常计算价值（否则`f[i][j]=min(f[i-1][j], f[i][j-tw]+1)`永远都为`10e5`）；另一方面也满足真实情况，0不需要找零钱。

代码如下：

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<vector<int>> f(n+1, vector<int>(amount+1, 10e5));
        for(int i=0; i<=n; i++){
            f[i][0] = 0;
        }

        for(int i=1; i<=n; i++){
            int tw = coins[i-1];
            for(int j=1; j<=amount; j++){
                if(j>=tw){
                    f[i][j] = min(f[i-1][j], f[i][j-tw]+1);
                }
                else{
                    f[i][j] = f[i-1][j];
                }
            }
        }
        if(f[n][amount]==10e5)  return -1;
        return f[n][amount];

    }
};
```

时间复杂度：O(NW)，N为物体数量，W为amount值

空间复杂度：O(NW)

## 解法二：完全背包问题-空间压缩

使用空间压缩，注意正向遍历。

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<int> f(amount+1, 10e5);
        f[0] = 0;

        for(int i=0; i<n; i++){
            int tw = coins[i];
            for(int j=tw; j<=amount; j++){
                f[j] = min(f[j], f[j-tw]+1);
            }
        }
        
        if(f[amount]==10e5)  return -1;
        return f[amount];

    }
};
```

时间复杂度：O(NW)，N为物体数量，W为amount值

空间复杂度：O(W)