# LeetCode1514：概率最大的路径

## 题目

给你一个由 n 个节点（下标从 0 开始）组成的无向加权图，该图由一个描述边的列表组成，其中 edges[i] = [a, b] 表示连接节点 a 和 b 的一条无向边，且该边遍历成功的概率为 succProb[i] 。

指定两个节点分别作为起点 start 和终点 end ，请你找出从起点到终点成功概率最大的路径，并返回其成功概率。

如果不存在从 start 到 end 的路径，请 返回 0 。只要答案与标准答案的误差不超过 1e-5 ，就会被视作正确答案。

 

示例 1：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/07/12/1558_ex1.png)

```
输入：n = 3, edges = [[0,1],[1,2],[0,2]], succProb = [0.5,0.5,0.2], start = 0, end = 2
输出：0.25000
解释：从起点到终点有两条路径，其中一条的成功概率为 0.2 ，而另一条为 0.5 * 0.5 = 0.25
```

示例 2：

```
输入：n = 3, edges = [[0,1],[1,2],[0,2]], succProb = [0.5,0.5,0.3], start = 0, end = 2
输出：0.30000
```

示例 3：

```
输入：n = 3, edges = [[0,1]], succProb = [0.5], start = 0, end = 2
输出：0.00000
解释：节点 0 和 节点 2 之间不存在路径
```


提示：

```
2 <= n <= 10^4
0 <= start, end < n
start != end
0 <= a, b < n
a != b
0 <= succProb.length == edges.length <= 2*10^4
0 <= succProb[i] <= 1
每两个节点之间最多有一条边
```

## 解法一：Dijkstra算法

我们首先证明本题可以使用"Dijkstra算法求最短路"：重新构造一个图G'，其与原图G一致，然后对图G'每条边求一个`log`，然后取负号`-log(p)`，对于原来的路径相乘就变成了相加问题，并且每条路径的p的取值范围都在`[0, 1]`之间，因此这个问题就从`求G概率最大的路径`变成了`求G'的最短路径`的问题，因此可以对原图G使用Dijkstra算法。

然后`Dijkstra`算法的核心在于：① 构图 - 带权值的邻接链表；② 维护构造优先队列（小顶堆） - 每次都能比较快速地找到最近的未遍历点；③ 松弛 - 每处理到新节点时，遍历其对应的边，进行松弛（`dist[next] = min(dist[next], dist[node]+weight)`；④ 更新优先队列 - 将节点与新权值加入优先队列。

```c++
// 大根堆
struct cmp {
    bool operator()(pair<int, float>& a, pair<int, float>& b) {
        return a.second < b.second;
    }
};

class Solution {
public:
    double maxProbability(int n, vector<vector<int>>& edges, vector<double>& succProb, int start, int end) {
        // 构图
        vector<vector<pair<int, float>>> adjLsit(n);
        for (int i = 0; i < edges.size(); i++) {
            int x = edges[i][0], y = edges[i][1];
            float p = succProb[i];
            adjLsit[x].push_back(make_pair(y, p));
            adjLsit[y].push_back(make_pair(x, p));
        }
        // 初始化
        vector<float> probs(n, 0);
        vector<bool> visited(n, false);
        priority_queue<pair<int, float>, vector<pair<int, float>>, cmp> pri_que;
        // 设置源点
        probs[start] = 1;
        pri_que.push(make_pair(start, 1));
        while (!pri_que.empty()) {
            int node = pri_que.top().first;
            pri_que.pop();
            if(visited[node])   continue;
            visited[node] = true;
            for (auto temp : adjLsit[node]) {
                int next = temp.first;
                float p = temp.second;
                if (visited[next])  continue;
                if (probs[node] * p > probs[next]) {
                    probs[next] = probs[node] * p;
                    pri_que.push(make_pair(next, probs[next]));
                }
            }
        }
        return probs[end];
    }
};
```

时间复杂度：O(mlogm)

空间复杂度：O(m)