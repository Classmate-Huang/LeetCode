# LeetCode2141：[同时运行 N 台电脑的最长时间](https://leetcode-cn.com/problems/maximum-running-time-of-n-computers/)

## 题目

你有 n 台电脑。给你整数 n 和一个下标从 0 开始的整数数组 batteries ，其中第 i 个电池可以让一台电脑 运行 batteries[i] 分钟。你想使用这些电池让 全部 n 台电脑 同时 运行。

一开始，你可以给每台电脑连接 至多一个电池 。然后在任意整数时刻，你都可以将一台电脑与它的电池断开连接，并连接另一个电池，你可以进行这个操作 任意次 。新连接的电池可以是一个全新的电池，也可以是别的电脑用过的电池。断开连接和连接新的电池不会花费任何时间。

注意，你不能给电池充电。

请你返回你可以让 n 台电脑同时运行的 最长 分钟数。

 

示例 1：

![img](https://assets.leetcode.com/uploads/2022/01/06/example1-fit.png)

```
输入：n = 2, batteries = [3,3,3]
输出：4
解释：
一开始，将第一台电脑与电池 0 连接，第二台电脑与电池 1 连接。
2 分钟后，将第二台电脑与电池 1 断开连接，并连接电池 2 。注意，电池 0 还可以供电 1 分钟。
在第 3 分钟结尾，你需要将第一台电脑与电池 0 断开连接，然后连接电池 1 。
在第 4 分钟结尾，电池 1 也被耗尽，第一台电脑无法继续运行。
我们最多能同时让两台电脑同时运行 4 分钟，所以我们返回 4 。
```

示例 2：

![img](https://assets.leetcode.com/uploads/2022/01/06/example2.png)

```
输入：n = 2, batteries = [1,1,1,1]
输出：2
解释：
一开始，将第一台电脑与电池 0 连接，第二台电脑与电池 2 连接。
一分钟后，电池 0 和电池 2 同时耗尽，所以你需要将它们断开连接，并将电池 1 和第一台电脑连接，电池 3 和第二台电脑连接。
1 分钟后，电池 1 和电池 3 也耗尽了，所以两台电脑都无法继续运行。
我们最多能让两台电脑同时运行 2 分钟，所以我们返回 2 。
```


提示：

```
1 <= n <= batteries.length <= 105
1 <= batteries[i] <= 109
```

## 解法一：二分 + 分配问题

如果存在一个函数`isFine()`，其能判断给定batteries能否给n台电脑同时充t分钟的电。如果能充t分钟的电，那说明小于t分钟都能满足条件，可以向上继续搜索，问题存在单调性，所以可以用二分法。根据题目给定的数据范围，确定时间t的上界[batteris的最大值×电池个数÷n]和下界[0]，利用二分搜索即可。

但如何确定`isFine()`中的逻辑关系呢？给定n台电脑，batteries电池容量，目标是判断是否能同时充t分钟。确定如下分配方案：① 对于容量大于t分钟的电池，直接让它去充1台电脑；经过步骤①，现在还剩下n'台电脑，对于这n'台电脑执行步骤②： 对于剩下的电池(小于t分钟)，只要它们的和 ≥  n‘ * t，就能满足要求。

对于步骤②的判断，可以做出以下证明：只要它们的和大于目标值，就一定存在某种分配方式能同时给n’台电脑充t分钟的电。

![image.png](https://pic.leetcode-cn.com/1642562033-toeuSh-image.png)

```c++
class Solution {
public:
    // 电池是否能够同时给n台电脑充t分钟的电
    bool isFine(int n, vector<int>& batteries, long long& t){
        long long sum = (long long)n*t;
        for(auto& battery:batteries){
            sum -= min((long long)battery, t);
            if(sum <= 0)    return true;
        }
        return false;
    }

    long long maxRunTime(int n, vector<int>& batteries) {
        long long left = 0, right = 1e14/n;
        // 二分
        while(left < right){
            long long mid = (right-left+1)/2 + left;
            if(isFine(n, batteries, mid))   left = mid;
            else right = mid-1;
        }
        return left;
    }
};
```

时间复杂度：O(nlogC)，C最多为1e14

空间复杂度：O(1)