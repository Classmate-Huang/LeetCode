# LeetCode310：最小高度树

## 题目

树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。

给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。

可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。

请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。

树的 高度 是指根节点和叶子节点之间最长向下路径上边的数量。

示例 1：

![img](https://assets.leetcode.com/uploads/2020/09/01/e1.jpg)

```
输入：n = 4, edges = [[1,0],[1,2],[1,3]]
输出：[1]
解释：如图所示，当根是标签为 1 的节点时，树的高度是 1 ，这是唯一的最小高度树。
```

示例 2：

![img](https://assets.leetcode.com/uploads/2020/09/01/e2.jpg)

```
输入：n = 6, edges = [[3,0],[3,1],[3,2],[3,4],[5,4]]
输出：[3,4]
```


提示：

```
1 <= n <= 2 * 104
edges.length == n - 1
0 <= ai, bi < n
ai != bi
所有 (ai, bi) 互不相同
给定的输入 保证 是一棵树，并且 不会有重复的边
```

## 解法一：DFS

这个题目其实就是求树的中心，其中一种解法就是先用两次`dfs`求得树的直径，然后提取直径上的中点即为树的中心。

第一次`dfs`选择任意一个点为起点，找到最远端点`x`；第二次`dfs`以`x`为起点，找到最远端点`y`，其中`x`和`y`的最远端点即为树的直径。

为了求得树的中心（直径的中点），我们在遍历的过程中需要记录每个节点的前驱节点，通过前驱节点恢复得到完整的直径。然后根据直径的长度返回结果：如果长度为偶数，中心点有两个；反之只有一个。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> adjList;

    vector<int> dist;   // 距离
    vector<int> pre;    // 前驱节点
    vector<bool> visited;   // 是否遍历

    // 找到以node为出发点的最长路径
    int findLongest(int node, int n) {
        // 初始化
        dist = vector<int> (n, -1);
        pre = vector<int> (n, -1);
        visited = vector<bool> (n, false);
        // 起点        
        pre[node] = -1;
        dist[node] = 0;
        // dfs
        dfs(node);
        // 找到最长距离点
        int maxlen = -1, target;
        for (int i = 0; i < n; i++) {
            if (dist[i] > maxlen) {
                target = i;
                maxlen = dist[i];
            }
        }
        return target;
    }

    void dfs(int node) {
        visited[node] = true;
        for (int next : adjList[node]) {
            if (!visited[next]) {
                pre[next] = node;
                dist[next] = dist[node] + 1;
                dfs(next);
            }
        }
    }

    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        adjList = vector<vector<int>> (n);
        for (auto edge : edges) {
            int x = edge[0], y = edge[1];
            adjList[x].push_back(y);
            adjList[y].push_back(x);
        }
        // 第一次遍历，找到x
        int x = findLongest(0, n);
        // 第二次遍历，得到y
        int y = findLongest(x, n);
        // 根据前驱节点得到完整直径
        vector<int> path;
        while (pre[y] != -1) {
            path.push_back(y);
            y = pre[y];
        }
        path.push_back(y);
        // 判断直径长度为奇数 or 偶数
        int lenth = path.size();
        if (lenth % 2 == 0)    return {path[lenth/2-1], path[lenth/2]};
        else return {path[lenth/2]};
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：拓扑排序 + 层次遍历

我们要求的是树的中心（其离所有的叶子节点距离最短），那么可以反向思考。我们从所有的叶子结点出发，利用拓扑排序+层次遍历，一层一层向上搜索，直到只剩下两个节点(一个节点)时就退出循环，此时队列中的元素即为要找的中心，代码如下：

> 需要注意，这里要用一个变量来记录当前还有多少个节点没有被遍历到，并且使用层次遍历（方便判断）

```c++
class Solution {
public:
    
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        // 特殊情况处理
        if (n == 1) return {0};
        // 构建邻接链表，记录各结点入度
        vector<vector<int>> adjList(n);
        vector<int> inDegree(n, 0);
        for (auto edge : edges) {
            int x = edge[0], y = edge[1];
            adjList[x].push_back(y);
            adjList[y].push_back(x);
            inDegree[x]++;
            inDegree[y]++;
        }
        // 压入度为1的节点
        queue<int> que;
        for (int i = 0; i < n; i++) {
            if (inDegree[i] == 1)   que.push(i);
        }
        // 拓扑排序 + 层次遍历
        int remainN = n;
        vector<bool> visited(n, false);
        while (remainN > 2) {   // 剩下的节点≤两点时，break
            int k = que.size();
            remainN -= k;
            while (k--) {
                int node = que.front();
                visited[node] = true;
                que.pop();
                for (auto next : adjList[node]) {
                    inDegree[next]--;
                    if (!visited[next] && inDegree[next] == 1) {
                        que.push(next);
                    }
                }
            }
        }
        // 处理结果
        if (remainN == 2) {
            int x = que.front();
            que.pop();
            int y = que.front();
            return {x, y};
        }
        else {
            int x = que.front();
            return {x};
        }
    }
};
```

