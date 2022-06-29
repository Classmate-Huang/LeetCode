# LeetCode双周赛76

## 题目一：[找到最接近 0 的数字](https://leetcode.cn/problems/find-closest-number-to-zero/)

简单模拟，打卡题

```c++
class Solution {
public:
    int findClosestNumber(vector<int>& nums) {
        int ans = nums[0];
        for (auto i : nums) {
            if (abs(i) < abs(ans))  ans = i;
            else if (abs(i) == abs(ans))   ans = max(i, ans);
        }
        return ans;
    }
};
```

## 题目二：[买钢笔和铅笔的方案数](https://leetcode.cn/problems/number-of-ways-to-buy-pens-and-pencils/)

枚举购买钢笔的数量，计算出购买铅笔的方案数，累积起来即可

```c++
class Solution {
public:
    long long waysToBuyPensPencils(int total, int cost1, int cost2) {
        long long ans = 0;
        
        for (int i = 0; i <= total/cost1; i++) {
            ans +=  ((total - i*cost1) / cost2) + 1;
        }
        
        return ans;
    }
};
```

## 题目三：[设计一个 ATM 机器](https://leetcode.cn/problems/design-an-atm-machine/)

大模拟题，遇到"需要更改数组元素，但可能更改失败"的情况，最好使用一个额外的临时数组来操作，成功再替换。

```c++
class ATM {
public:
    vector<int> money = {20, 50, 100, 200, 500};
    vector<long long> moneyCnt = vector<long long> (5, 0);;
    
    ATM(){
    }
    
    void deposit(vector<int> banknotesCount) {
        for(int i=0; i<5; i++)  moneyCnt[i] += banknotesCount[i];
    }
    
    vector<int> withdraw(int amount) {
        vector<int> ops(5, 0);
        // 优先取大额钞票
        for(int i=4; amount>0 && i>=0; i--){
            // 待取数量小于当前的面值，跳过
            if(amount < money[i])    continue;
            // 否则，尽可能多取
            long long tempMax = amount / money[i];
            amount -= min(tempMax, moneyCnt[i]) * money[i];
            ops[i] = int(min(tempMax, moneyCnt[i]));
        }
        // 无法满足条件
        if(amount>0) return {-1};
        // 满足条件，更新
        for(int i=0; i<5; i++)  moneyCnt[i] -= ops[i];
        return ops;
    }
};
```

## 题目四：[节点序列的最大得分](https://leetcode.cn/problems/maximum-score-of-a-node-sequence/)

答案样式为`a-x-y-b`，枚举中间的边(`x-y`)得到两个端点。只要找到`x`相邻最大的非`y`的端点`a`，和`y`相邻最大的非`x`端点`b`，其中`a!=b`。需要注意的是，我们并不需要枚举`x`和`y`的所有相邻点，而是只要遍历权值排在前3的邻接点即可。代码如下：

```c++
bool cmp(pair<int, int>& a, pair<int, int>& b) {
    return a.first > b.first;
}

class Solution {
public:
    int maximumScore(vector<int>& scores, vector<vector<int>>& edges) {
        int n = scores.size();
        vector<vector<pair<int, int>>> adjList(n);
        
        for (auto& edge:edges) {
            int x = edge[0], y = edge[1];
            adjList[x].push_back(make_pair(scores[y], y));
            adjList[y].push_back(make_pair(scores[x], x));
        }
        
        for (int i = 0; i < n; i++) {
            sort(adjList[i].begin(), adjList[i].end(), cmp);    // 升序遍历
        }
        
        int ans = -1;
        // 枚举边-即确定中间两个节点
        for (auto edge : edges) {
            int x = edge[0], y = edge[1];
            for (int tx = 0; tx < min(3, int(adjList[x].size())); tx++) {
                for (int ty = 0; ty < min(3, int(adjList[y].size())); ty++) {
                    if (adjList[x][tx].second != y && adjList[y][ty].second != x && adjList[x][tx].second != adjList[y][ty].second) {
                        ans = max(ans, scores[x] + scores[y] + adjList[x][tx].first + adjList[y][ty].first);
                    }
                }
            }
        }
        
        return ans;
    }
};
```

