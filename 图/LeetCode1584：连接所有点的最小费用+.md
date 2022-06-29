# LeetCode1584：连接所有点的最小费用

## 题目

给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [xi, yi] 。

连接点 [xi, yi] 和点 [xj, yj] 的费用为它们之间的 曼哈顿距离 ：|xi - xj| + |yi - yj| ，其中 |val| 表示 val 的绝对值。

请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。

 

示例 1：

![img](https://assets.leetcode.com/uploads/2020/08/26/d.png)

```
输入：points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
输出：20
解释：

我们可以按照上图所示连接所有点得到最小总费用，总费用为 20 。
注意到任意两个点之间只有唯一一条路径互相到达。
```

示例 2：

```
输入：points = [[3,12],[-2,5],[-4,1]]
输出：18
```

示例 3：

```
输入：points = [[0,0],[1,1],[1,0],[-1,1]]
输出：4
```

示例 4：

```
输入：points = [[-1000000,-1000000],[1000000,1000000]]
输出：4000000
```

示例 5：

```
输入：points = [[0,0]]
输出：0
```


提示：

```
1 <= points.length <= 1000
-106 <= xi, yi <= 106
所有点 (xi, yi) 两两不同。
```

## 解法一：Kruskal算法

本题是经典的最小生成树算法，最小生成树有两种经典解，首先介绍**以边为核心**的算法：Kruskal算法。

构造并查集，记录各节点的连通情况。然后对图中所有的边按照权值进行排序，从小到大遍历边，对于每条边判断其对应的两个端点是否已经连通，如果未连通，说明此边属于最小生成树中的边，加入队列，并更新并查集信息。最后遍历所有的边即可得到结果，代码如下：

```c++
class DJS {
public:
    vector<int> father;
    DJS(int n) {
        father = vector<int> (n);
        for (int i = 0; i < n; i++) father[i] = i;
    }
    int find(int x) {
        father[x] = father[x] == x ? x : find(father[x]);
        return father[x];
    }
    void merge(int x, int y) {
        int rootx = find(x), rooty = find(y);
        if (rootx != rooty) father[rootx] = rooty;
    }
};


bool cmp(vector<int>& a, vector<int>& b) {
    return a[2] < b[2];
}

class Solution {
public:
    int minCostConnectPoints(vector<vector<int>>& points) {
        int n = points.size();
        vector<vector<int>> edges;
        for (int i = 0; i < n; i++) {
            for (int j = i+1; j < n; j++) {
                int dist = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1]);
                edges.push_back({i, j, dist});
            }
        }
        
        // Kruskal算法
        int ans = 0;
        sort(edges.begin(), edges.end(), cmp);
        DJS djs = DJS(n);
        for (auto edge : edges) {
            int x = edge[0], y = edge[1], w = edge[2];
            if (djs.find(x) != djs.find(y)) {
                ans += w;
                djs.merge(x, y);
            }
        }
        return ans;
    }
};
```

时间复杂度：O($n^2logn$)，Kruskal算法本来是`mlogm`级别的算法，m为边数，在本题中m=n^2，所以很容易超时。

空间复杂度：O(n)

## 解法二：Prim算法

第二种算法叫做Prim算法，其是以点为核心的算法：构造一个`unordered_set`记录遍历过的点集，随机选择一个点出发，将对应的边全部加入**优先队列**中，每次从优先队列中选择第一个没有遍历过的点加入集合，并将新的边加入优先队列，直到所有点都被遍历到。代码如下：

```c++

// 小顶堆
struct cmp {
    bool operator() (pair<int, int>& a, pair<int, int>& b) {
        return a.second > b.second;
    }
};

class Solution {
public:
    int minCostConnectPoints(vector<vector<int>>& points) {
        int n = points.size();
        // 构造带权值的邻接链表
        vector<vector<pair<int, int>>> adjList(n);
        for (int i = 0; i < n; i++) {
            for (int j = i+1; j < n; j++) {
                int dist = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1]);
                adjList[i].push_back(make_pair(j, dist));
                adjList[j].push_back(make_pair(i, dist));
            }
        }
        
        // Prim算法
        int ans = 0;
        unordered_set<int> visited; // 记录已遍历的点集
        priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> pri_que;    // 优先队列
        // 随机从一个点出发
        visited.insert(0);
        for (auto temp : adjList[0]) {
            pri_que.push(make_pair(temp.first, temp.second));
        }
        while (!pri_que.empty()) {
            int next = pri_que.top().first, weight = pri_que.top().second;
            pri_que.pop();
            if (visited.find(next) != visited.end())    continue;   // 如果已加入点集，跳过
            visited.insert(next);   // 否则加入点集，并加入新的边集
            ans += weight;
            for (auto temp : adjList[next]) {
                if (visited.find(temp.first) != visited.end()) continue;   // 剪枝
                pri_que.push(make_pair(temp.first, temp.second));
            }
        }

        return ans;
    }
};
```

