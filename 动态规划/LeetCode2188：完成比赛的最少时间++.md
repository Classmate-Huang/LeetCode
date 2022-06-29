# LeetCode2188：完成比赛的最少时间

## 题目

给你一个下标从 0 开始的二维整数数组 tires ，其中 tires[i] = [fi, ri] 表示第 i 种轮胎如果连续使用，第 x 圈需要耗时 fi * ri(x-1) 秒。

比方说，如果 fi = 3 且 ri = 2 ，且一直使用这种类型的同一条轮胎，那么该轮胎完成第 1 圈赛道耗时 3 秒，完成第 2 圈耗时 3 * 2 = 6 秒，完成第 3 圈耗时 3 * 22 = 12 秒，依次类推。
同时给你一个整数 changeTime 和一个整数 numLaps 。

比赛总共包含 numLaps 圈，你可以选择 任意 一种轮胎开始比赛。每一种轮胎都有 无数条 。每一圈后，你可以选择耗费 changeTime 秒 换成 任意一种轮胎（也可以换成当前种类的新轮胎）。

请你返回完成比赛需要耗费的 最少 时间。

 

示例 1：

```
输入：tires = [[2,3],[3,4]], changeTime = 5, numLaps = 4
输出：21
解释：
第 1 圈：使用轮胎 0 ，耗时 2 秒。
第 2 圈：继续使用轮胎 0 ，耗时 2 * 3 = 6 秒。
第 3 圈：耗费 5 秒换一条新的轮胎 0 ，然后耗时 2 秒完成这一圈。
第 4 圈：继续使用轮胎 0 ，耗时 2 * 3 = 6 秒。
总耗时 = 2 + 6 + 5 + 2 + 6 = 21 秒。
完成比赛的最少时间为 21 秒。
```

示例 2：

```
输入：tires = [[1,10],[2,2],[3,4]], changeTime = 6, numLaps = 5
输出：25
解释：
第 1 圈：使用轮胎 1 ，耗时 2 秒。
第 2 圈：继续使用轮胎 1 ，耗时 2 * 2 = 4 秒。
第 3 圈：耗时 6 秒换一条新的轮胎 1 ，然后耗时 2 秒完成这一圈。
第 4 圈：继续使用轮胎 1 ，耗时 2 * 2 = 4 秒。
第 5 圈：耗时 6 秒换成轮胎 0 ，然后耗时 1 秒完成这一圈。
总耗时 = 2 + 4 + 6 + 2 + 4 + 6 + 1 = 25 秒。
完成比赛的最少时间为 25 秒。
```


提示：

```
1 <= tires.length <= 105
tires[i].length == 2
1 <= fi, changeTime <= 105
2 <= ri <= 105
1 <= numLaps <= 1000
```

## 解法一：抽象问题 + 完全背包

解题思路：

① 由于每跑完一圈，下一圈就需要跑$f_i*r_i^{x-1}$秒，可以看到每一圈时间的增长是呈指数形式的，因此不可能存在"一条轮胎跑很多圈的情况"，因为可以换胎；

② 根据提示①，我们可以推导出一条轮胎最多跑19圈，其实就是解决这个不等式$f_i*r_i^{x-1}≤f_i+changeTime$，估算可得为19【有更小的下界，但是19已经足够求解了】；

③ 可以求出每条轮胎连续跑$x$圈需要的时间，因而进一步求出"**同时考虑所有轮胎，但只选一条轮胎连续跑$x$圈需要的最短时间times[x]**"；

④ 现在我们知道了选一条轮胎跑$x$圈的最短时间，那么对于这个问题就相当于把$numLaps$圈拆分成多个$x$的组合，使其总耗时间最短【经典的完全背包问题】。

> 细节：每次换胎需要额外changeTime，因此对于times[0]需要额外考虑changeTime【但第一次出发时不需要考虑changeTime，所以最后的结果减去一个changeTime即可】

代码如下：

```c++
class Solution {
public:
    int minimumFinishTime(vector<vector<int>>& tires, int changeTime, int numLaps) {
        // 预处理数组 [用一条轮胎跑x圈需要花费的最短时间]
        vector<int> times(20, 1e9+5);
        times[0] = 0;
        for(auto& tire:tires){
            long long f = tire[0], r = tire[1], t1 = f + changeTime, sum = t1;
            for(int i=1; f<=t1; i++){
                times[i] = min((long long)times[i], sum);
                f = f * r;
                sum += f;
            }
        }
        // 问题转换为完全背包问题
        vector<int> f(numLaps+1, 1e9);
        f[0] = 0;
        for(int i=1; i<times.size(); i++){
            int tw = i, tv = times[i];
            for(int j=tw; j<=numLaps; j++){
                f[j] = min(f[j], f[j-tw]+tv);
            }
        }
        
        return f[numLaps]-changeTime;
    }
};
```

时间复杂度:O(max(m,n))，其中m为tires的长度，n为numLaps，其最大值都为10^5

空间复杂度:O(1)