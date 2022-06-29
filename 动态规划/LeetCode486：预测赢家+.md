# LeetCode486：预测赢家

## 题目

给你一个整数数组 nums 。玩家 1 和玩家 2 基于这个数组设计了一个游戏。

玩家 1 和玩家 2 轮流进行自己的回合，玩家 1 先手。开始时，两个玩家的初始分值都是 0 。每一回合，玩家从数组的任意一端取一个数字（即，nums[0] 或 nums[nums.length - 1]），取到的数字将会从数组中移除（数组长度减 1 ）。玩家选中的数字将会加到他的得分上。当数组中没有剩余数字可取时，游戏结束。

如果玩家 1 能成为赢家，返回 true 。如果两个玩家得分相等，同样认为玩家 1 是游戏的赢家，也返回 true 。你可以假设每个玩家的玩法都会使他的分数最大化。

示例 1：

```
输入：nums = [1,5,2]
输出：false
解释：一开始，玩家 1 可以从 1 和 2 中进行选择。
如果他选择 2（或者 1 ），那么玩家 2 可以从 1（或者 2 ）和 5 中进行选择。如果玩家 2 选择了 5 ，那么玩家 1 则只剩下 1（或者 2 ）可选。 
所以，玩家 1 的最终分数为 1 + 2 = 3，而玩家 2 为 5 。
因此，玩家 1 永远不会成为赢家，返回 false 。
```

示例 2：

```
输入：nums = [1,5,233,7]
输出：true
解释：玩家 1 一开始选择 1 。然后玩家 2 必须从 5 和 7 中进行选择。无论玩家 2 选择了哪个，玩家 1 都可以选择 233 。
最终，玩家 1（234 分）比玩家 2（12 分）获得更多的分数，所以返回 true，表示玩家 1 可以成为赢家。
```


提示：

```
1 <= nums.length <= 20
0 <= nums[i] <= 107
```

## 解法一：暴力递归

当前是先手选择还是后手选择，先手选最大，后手就是被迫选最小。代码如下：

```c++
class Solution {
public:

    int dfs(vector<int>& nums, int left, int right, bool firstA){
        // A是先手
        if(firstA){
            if(left == right)   return nums[left];  // 只能选这个数
            int pLeft = nums[left] + dfs(nums, left+1, right, false); // 选左边的牌
            int pRight = nums[right] + dfs(nums, left, right-1, false); // 选右边的牌
            return max(pLeft, pRight);  // A肯定选最大
        }
        // A是后手，B是先手
        else{
            if(left == right)   return 0;   // A什么都选不到
            int pLeft = dfs(nums, left+1, right, true);   // B选了左边，A能得到的最大值
            int pRight = dfs(nums, left, right-1, true);    // B选了右边，A能得到的最大值
            return min(pLeft, pRight);  // B只会给你最小
        }
    }

    bool PredictTheWinner(vector<int>& nums) {
        int sum = 0;
        for(auto& i:nums)   sum += i;

        int sumA = dfs(nums, 0, nums.size()-1, true);

        return 2*sumA >= sum;
    }
};
```

时间复杂度：O(2^n)

空间复杂度：O(n)

## 解法二：记忆化搜索

可以看到在解法一的递归过程中会遇到很多重复情况的处理，所以我们可以用记忆化搜索，存储每种情况下的值。如果之前已经解决过就直接调用即可，代码如下：

```c++
class Solution {
public:

    int dfs(vector<int>& nums, int left, int right, bool firstA, vector<vector<vector<int>>>& f){
        // A是先手
        if(firstA){
            if(left == right)   return nums[left];  // 只能选这个数
            
            if(f[0][left][right] != -1) return f[0][left][right];

            int pLeft = nums[left] + dfs(nums, left+1, right, false, f); // 选左边的牌
            int pRight = nums[right] + dfs(nums, left, right-1, false, f); // 选右边的牌
            
            f[0][left][right] = max(pLeft, pRight);

            return f[0][left][right];  // A肯定选最大
        }
        // A是后手，B是先手
        else{
            if(left == right)   return 0;   // A什么都选不到

            if(f[1][left][right] != -1) return f[1][left][right];

            int pLeft = dfs(nums, left+1, right, true, f);   // B选了左边，A能得到的最大值
            int pRight = dfs(nums, left, right-1, true, f);    // B选了右边，A能得到的最大值
            
            f[1][left][right] = min(pLeft, pRight);
            
            return f[1][left][right];  // B只会给A最小
        }
    }

    bool PredictTheWinner(vector<int>& nums) {
        int sum = 0, n = nums.size();
        for(auto& i:nums)   sum += i;

        // f[0][i][j]表示A先手从[i,j]取的最大值; f[1][i][j]表示A后手从[i, j]取得的最大值
        vector<vector<vector<int>>> f(2, vector<vector<int>>(n, vector<int>(n, -1))); 
        int sumA = dfs(nums, 0, n-1, true, f);

        return 2*sumA >= sum;
    }
};
```

## 解法三：动态规划

确定了记忆化搜索的版本，不难确定动态规划的版本。

① 定义状态：`f[0][i][j]`表示A作为先手在[i，j]范围内能取到的最大值；`f[1][i][j]`表示A作为后手在[i，j]范围内能取到的最大值；② 状态转移方程：`f[0][i][j] = max(nums[i]+f[1][i+1][j], nums[j]+f[1][i][j-1])`，`f[1][i][j]=min(f[0][i+1][j], f[0][i][j-1])`；③ 初始状态：`f[0][i][i]=nums[i],f[1][i][i]=0`。

代码如下：

```c++
class Solution {
public:

    bool PredictTheWinner(vector<int>& nums) {
        int sum = 0, n = nums.size();
        for(auto& i:nums)   sum += i;
        
        /* DP */
        // f[0][i][j]表示A先手从[i,j]取的最大值; f[1][i][j]表示A后手从[i, j]取得的最大值
        vector<vector<vector<int>>> f(2, vector<vector<int>>(n, vector<int>(n, 0))); 
        // 初始化-边界条件   f[0][i][i] = nums[i]   只有一个数的时候，A先手取得其所有值
        for(int i=0; i<n; i++){
            f[0][i][i] = nums[i];
        }
        // 先手依赖关系 f[0][i][j] = max(nums[i] + f[1][i+1][j], nums[j] + f[1][i][j-1])
        // 后手依赖关系 f[1][i][j] = min(f[0][i+1][j], f[0][i][j-1])
        for(int i=1; i<n; i++){ // 可以画出状态转移矩阵，更加清晰明了
            for(int l=0, r=i; l<n && r<n; l++, r++){    // [l, r]   表示[l, r]区间
                f[0][l][r] = max(nums[l]+f[1][l+1][r], nums[r]+f[1][l][r-1]);
                f[1][l][r] = min(f[0][l+1][r], f[0][l][r-1]);
            }
        }

        return 2*f[0][0][n-1] >= sum;
    }
};
```
