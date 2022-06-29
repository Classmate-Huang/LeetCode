# LeetCode周赛289

## 题目一：[计算字符串的数字和](https://leetcode.cn/problems/calculate-digit-sum-of-a-string/)

模拟题，模拟整个过程即可

```c++
class Solution {
public:
    string strsum(string s) {
        int sum = 0;
        for (char i:s)  sum += i - '0';
        return to_string(sum);
    }
    
    string digitSum(string s, int k) {
        while (s.size() > k) {
            string temp = "";
            for (int i = 0; i < s.size(); i = i+k) {
                temp += strsum(s.substr(i, k));
            }
            s = temp;
        }
        return s;
    }
};
```

## 题目二：[完成所有任务需要的最少轮数](https://leetcode.cn/problems/minimum-rounds-to-complete-all-tasks/)*

在比赛的时候暴力枚举了一些情况，然后复盘的时候又想了动态规划预处理的方法(`f[i]=min(f[i-3], f[i-2])+1`)。

但其实这个题目只需要分类讨论即可：

* 首先`cnt==1`，为-1；如果`cnt==2 || 3`，为1；
* 当`cnt > 3`之后，我们一定能找到一种解决策略，`cnt % 3 == 0`，显然为`cnt/3`；而`cnt % 3 == 2`的情况，为`cnt/3+1`；当`cnt % 3 == 1`的情况，可以拆分为`cnt % 3 == 4`的状况，同样为`cnt/3+1`。

```c++
class Solution {
public:
    int getAns(int& cnt) {
        if (cnt == 1)   return -1;
        else if (cnt % 3 == 0)  return cnt/3;
        else    return cnt/3 + 1;
    }
    
    int minimumRounds(vector<int>& tasks) {
        unordered_map<int, int> myMap;
        for (auto i : tasks)    myMap[i]++;
        
        int ans = 0;
        for (auto it : myMap) {
            int temp = getAns(it.second);
            if (temp == -1) return -1;
            ans +=  temp;
        }
        return ans;
    }
};
```

## 题目三：[转角路径的乘积中最多能有几个尾随零](https://leetcode.cn/problems/maximum-trailing-zeros-in-a-cornered-path/)*

有一个核心解题点：要求一串数的乘积有多少个尾随0，只需要统计这些数分解后因子2的个数`s2`和因子5的个数`s5`，尾随0的数量即为`min(s2, s5)`。

对于本题，我们可以选择枚举转角点，对于每个转角点有4种不同的路径设计`上-左，上-右，下-左，下-右`，实现统计好每个点从左-右 / 右-左 / 上-下 / 下-上 的`s2，s5`，然后计算不同路径下的尾随0个数即可。最后取整体的最大值，代码如下：

```c++
template<class Ty1,class Ty2> 
inline const pair<Ty1,Ty2> operator+(const pair<Ty1, Ty2>&p1, const pair<Ty1, Ty2>&p2)
{
    pair<Ty1, Ty2> ret;
    ret.first = p1.first + p2.first;
    ret.second = p1.second + p2.second;
    return ret;
}

template<class Ty1,class Ty2> 
inline const pair<Ty1,Ty2> operator-(const pair<Ty1, Ty2>&p1, const pair<Ty1, Ty2>&p2)
{
    pair<Ty1, Ty2> ret;
    ret.first = p1.first - p2.first;
    ret.second = p1.second - p2.second;
    return ret;
}

class Solution {
public:
    pair<int, int> findNeed(int num){
        int t5 = 0, t2 = 0;
        while(num%5==0){
            num = num / 5;
            t5++;
        }
        while(num%2==0){
            num = num / 2;
            t2++;
        }
        return make_pair(t5, t2);
    }
    
    int maxTrailingZeros(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        // 先统计每个点上 5的倍速 + 2的倍数
        vector<vector<pair<int, int>>> newGrid(m, vector<pair<int, int>>(n));
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                newGrid[i][j] = findNeed(grid[i][j]);
            }
        }
        // 记录前缀和
        vector<vector<pair<int, int>>> left2right(m, vector<pair<int, int>>(n));
        vector<vector<pair<int, int>>> up2down(m, vector<pair<int, int>>(n));
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(i==0)    up2down[i][j] = newGrid[i][j];
                if(j==0)    left2right[i][j] = newGrid[i][j];
                if(i!=0)    up2down[i][j] = newGrid[i][j] + up2down[i-1][j];
                if(j!=0)    left2right[i][j] = newGrid[i][j] + left2right[i][j-1];
            }
        }
        vector<vector<pair<int, int>>> right2left(m, vector<pair<int, int>>(n));
        vector<vector<pair<int, int>>> down2up(m, vector<pair<int, int>>(n));
        for(int i=m-1; i>=0; i--){
            for(int j=n-1; j>=0; j--){
                if(i==m-1)    down2up[i][j] = newGrid[i][j];
                if(j==n-1)    right2left[i][j] = newGrid[i][j];
                if(i!=m-1)    down2up[i][j] = newGrid[i][j] + down2up[i+1][j];
                if(j!=n-1)    right2left[i][j] = newGrid[i][j] + right2left[i][j+1];
            }
        }
        // 遍历所有点，统计以该点为转折点的最大零个数
        int ans = 0;
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                int temp1 = min((left2right[i][j] + up2down[i][j] - newGrid[i][j]).first, (left2right[i][j] + up2down[i][j] - newGrid[i][j]).second);
                int temp2 = min((left2right[i][j] + down2up[i][j] - newGrid[i][j]).first, (left2right[i][j] + down2up[i][j] - newGrid[i][j]).second);
                int temp3 = min((right2left[i][j] + up2down[i][j] - newGrid[i][j]).first, (right2left[i][j] + up2down[i][j] - newGrid[i][j]).second);
                int temp4 = min((right2left[i][j] + down2up[i][j] - newGrid[i][j]).first, (right2left[i][j] + down2up[i][j] - newGrid[i][j]).second);
                ans = max(ans, temp1);
                ans = max(ans, temp2);
                ans = max(ans, temp3);
                ans = max(ans, temp4);
            }
        }
        return ans;
    }
};
```

## 题目四：[相邻字符不同的最长路径](https://leetcode.cn/problems/longest-path-with-different-adjacent-characters/)

这个题目实际不难，跟求`树的最大直径`差不多，唯一区别就在于这里加了一个判断条件：相邻节点的字符不能一致。

(这个题目在比赛中本来做出来了，但是传参忘记对`string s`加引用了，导致时间复杂度爆了，太可惜了)

```c++
class Solution {
public:
    int ans = 1;
    vector<vector<int>> child;
    
    int maxDepth(vector<int>& parent, string& s, int i) {
        int d = 0;  // 维护满足要求的孩子节点的最大深度
        for (auto c : child[i]) {
            int temp = maxDepth(parent, s, c);
            if (s[c] != s[i]) {
                ans = max(ans, d+temp+1);
                d = max(d, temp);
            }
        }
        return d+1;
    }
    
    int longestPath(vector<int>& parent, string s) {
        int n = parent.size();
        child = vector<vector<int>>(n);
        // 记录孩子节点
        for (int i = 1; i < n; i++) {
            child[parent[i]].push_back(i);
        }
        maxDepth(parent, s, 0);
        return ans;
    }
};
```

