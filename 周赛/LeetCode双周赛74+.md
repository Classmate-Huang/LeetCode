# LeetCode双周赛74+

## 题目一：[将数组划分成相等数对](https://leetcode-cn.com/problems/divide-array-into-equal-pairs/)

打卡题，利用vector存储每个字符出现次数，然后判断每个字符出现次数是否为偶数即可。

```c++
class Solution {
public:
    bool divideArray(vector<int>& nums) {
        vector<int> cnt(501, 0);
        for(auto& i:nums)   cnt[i]++;
        for(auto& i:cnt){
            if(i%2!=0)  return false;
        }
        return true;
    }
};
```

## 题目二：[字符串中最多数目的子字符串](https://leetcode-cn.com/problems/maximize-number-of-subsequences-in-a-string/)

从左到右依次统计`pattern[0]`和`pattern[1]`出现的次数，每次遇到`pattern[1]`就累加一次结果，最后判断那个数量多，再加一次就好了。这里需要注意处理`pattern[0]==pattern[1]`的特殊情况，代码如下：

```c++
class Solution {
public:
    long long maximumSubsequenceCount(string text, string pattern) {
        // 记录pattern[0]和patter[1]的出现总次数
        long long totalP0 = 0, totalP1 = 0, n = text.size(), ans = 0;;   
        // 特殊情况 pattern[0] == pattern[1]
        if(pattern[0]==pattern[1]){
            for(auto& c:text){
                if(c==pattern[0])   totalP0++;
            }
            ans += (totalP0-1)*totalP0/2 + totalP0;
            return ans;
        }
        // 从左到右逐个统计
        for(auto& c:text){
            if(c==pattern[0])   totalP0++;  // pattern[0]计数
            else if(c==pattern[1]){
                ans += totalP0; // 记录一次
                totalP1++;  // 总数
            }
        }
        // 选择出现次数最多的进行插入
        ans += max(totalP0, totalP1);
        
        return ans;
    }
};
```

## 题目三：[将数组和减半的最少操作次数](https://leetcode-cn.com/problems/minimum-operations-to-halve-array-sum/)

贪心，每次选择当前数组最大的元素进行减半，直到减少总数大于目标值，利用优先队列实现。代码如下：

```c++
struct cmp{ // 大顶堆
  bool operator()(double& a, double& b){
      return a < b;
  }
};


class Solution {
public:
    int halveArray(vector<int>& nums) {
        int ans = 0;
        double cur = 0, target = 0;
        
        priority_queue<double, vector<double>, cmp> pri_que;  //优先队列 - 大顶堆
        for(auto& i:nums){
            target += double(i);
            pri_que.push(double(i));
        }
        target /= 2;
        while(cur < target){
            double node = pri_que.top();    pri_que.pop();
            cur += node/2;
            pri_que.push(node/2);
            ans ++;
        }
        return ans;
    }
};
```

## 题目四：[用地毯覆盖后的最少白色砖块](https://leetcode-cn.com/problems/minimum-white-tiles-after-covering-with-carpets/) *

比赛的时候搞了很久的贪心（优先去铺最长的白色地板区域），但后来才发现有问题(`101111010001`，2块长度为4的地毯，应该返回的是1)。这题正确的解法应该是动态规划，需要考虑关键因素：地板，毛毯数量（一般题目给了什么就用什么），`f[i][j]`表示前i块地板用了j条地毯后剩下的白色地板数（题目要求什么就定义什么）。

考虑状态转移方程：对于第i块地板，覆盖 or 不覆盖：如果不覆盖 `f[i][j]=f[i-1][j]+floor[i]=='1'?1:0`，如果覆盖`f[i][j]=f[i-carpetLen][j-1]`（覆盖当前到前carpetLen长的地板）。最后再注意边界条件即可，代码如下：

```c++
class Solution {
public:
    int minimumWhiteTiles(string floor, int numCarpets, int carpetLen) {
        int n = floor.size();
        vector<vector<int>> f(n+1, vector<int>(numCarpets+1, 1001));   // f[i][j]表示前i块砖用了j条毛毯还剩下多少个白色砖块
        for(int i=0; i<=numCarpets; i++)    f[0][i] = 0;
        for(int i=0; i<n; i++){
            int cnt = floor[i] == '0' ? 0 : 1;
            for(int j=0; j<=numCarpets; j++){
                if(j == 0)  f[i+1][j] = f[i][j] + cnt;
                else f[i+1][j] = min(f[i][j] + cnt, f[max(i+1-carpetLen,0)][j-1]); // 是否铺这块地毯
            }
        }
        return f[n][numCarpets];
    }
};
```

