# 图

#### 0. 构建图结构的模板

遇到图问题，先根据题目的需要，构造成自己熟悉的数据结构模板，然后直接套算法即可。

```c++
vector<vector<int>> adjList(n);	// 构造邻接链表 - vector<vector<pair<int, int>>> adjList 带权值的边
vector<int> inDegree(n);	// 入度
vector<int> outDegree(n);	// 出度
vector<int> pre(n);		// 路径中每个点的前驱节点
```

#### 1. 图的遍历

图包括`DFS`和`BFS`，但需要注意避免**重复遍历**（使用一个`unordered_set`或者`vector`记录已遍历过的点即可），题547(省份数量)。

#### 2. 拓扑排序

拓扑排序（topological sort）是一种常见的、对**有向无环图排序**的算法。给定有向无环图中的 *N* 个节点，我们把它们排序成一个线性序列；**若原图中节点 i 指向节点 j，则排序结果中 i 一定在 j 之前**。拓扑排序的结果不是唯一的，只要满足以上条件即可（题210）。

使用`BFS`：首先把所有入度为0的点压入队列，然后处理队列中的点（加入结果队列 + 消除当前节点对邻接点的影响），并将新的入度为0的点放入队列。

```c++
while (!que.empty()) {
	int node = que.front();
	que.pop();
	... // 处理当前节点
	for (auto next : adjList[node]) {
		inDegree[next]--;
		if (inDegree[next] == 0)    que.push(next);
	}
}
```

#### 3. 最小生成树

**最小生成树**的问题，简单得理解就是给定一个带有权值的连通图（连通网），从众多的生成树中筛选出权值总和最小的生成树（连通无环），即为**该图的最小生成树**（题1584）。

* **Kruskal算法**：以**边**为主，按权值排序，如果边对应的两个节点当前并没有连通则将次边加入生成树中。在这个过程中使用**并查集**进行节点合并判断。 

```c++
sort(edges.begin(), edges.end(), cmp);	// 按权值排序
DJS djs = DJS(n);	// 并查集
for (auto edge : edges) {
    int x = edge[0], y = edge[1], w = edge[2];
    if (djs.find(x) != djs.find(y)) {	// 判断当前边对应的节点是否已连接
	    .... // 处理
        djs.merge(x, y);	// 合并
    }
}
```

* **Prim算法**：以**点**为主，**使用集合`unordered_set`**记录已选节点，并将已选节点的边加入**优先队列**中，每次选取权值最小的边【此边对应的另一个顶点并不在已选节点中】，再将对应节点的边送入优先队列。

```c++
unordered_set<int> visited; // 记录已遍历的点集
priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> pri_que;    // 优先队列
// 随机从一个点出发
visited.insert(0);
for (auto temp : adjList[0]) {
    pri_que.push(make_pair(temp.first, temp.second));	// 邻接点 + 权值
}
while (!pri_que.empty()) {
    int next = pri_que.top().first, weight = pri_que.top().second;
    pri_que.pop();
    if (visited.find(next) != visited.end())    continue;   // 如果已加入点集，跳过
    visited.insert(next);   // 否则加入点集，并加入新的边集
    ... // 处理
    for (auto temp : adjList[next]) {	// 将新的边集加入队列
        pri_que.push(make_pair(temp.first, temp.second));
    }
}
```

#### 4. 单源最短路问题

> 涉及算法：**Dijsktra 算法**、**Floyd 算法**、**Bellman-Ford 算法**、SPFA 算法
>
> [最短路径算法总结和LeetCode题目实践 / Drrany](https://drrany.github.io/ShortestPathAlgorithm/) 
>
> [【最短路/必背模板】涵盖所有的「存图方式」与「最短路算法（详尽注释）」](https://mp.weixin.qq.com/s?__biz=MzU4NDE3MTEyMA==&mid=2247488007&idx=1&sn=9d0dcfdf475168d26a5a4bd6fcd3505d&chksm=fd9cb918caeb300e1c8844583db5c5318a89e60d8d552747ff8c2256910d32acd9013c93058f&token=754098973&lang=zh_CN#rd)

##### **4.1 Dijkstra算法**（题743,1514）

**核心**：设置一个顶点集合 S，存放已找到最短路径的顶点，每次从 V-S 集合中**找距离源点距离最小的点**，假设为u，将其**加入集合 S**。再次遍历 V-S 集合中的点，设为v，看是否能对边(u,v)进行**松弛操作**(`dist[u]+G[u][v]<dist[v]`)更新距离。

> Dijsktra本质是一种贪心算法，每次去找到使距离最短的路径，因此不能解决边权有负值（有负值的环）的情况

**核心代码**：① 构图 - 带权值的邻接链表；② 维护构造优先队列（小顶堆） - 每次都能比较快速地找到最近的未遍历点；③ 松弛 - 每处理到新节点时，遍历其对应的边，进行松弛（`dist[next] = min(dist[next], dist[node]+weight)`；④ 更新优先队列 - 将节点与新权值加入优先队列。

```c++
// 设置的数据结构：
bool visited[n]; // visited[i]=true表示顶点i已经加入集合S
int dist[n];	 // dist[i]表示顶点i距离源点的最短距离
int pre[n];	     // 到达pre[i]的最短路径上，i的前一个顶点
priority_queue<pair<int, int>, vector<pair<int,int>>, cmp> pri_que;	// 优先队列

// 加入源节点信息
pre[source] = source;
dist[source] = 0；
pri_que.push(make_pair(source, 0));
    
// 借助优先队列进行Dijkstra
while(!pri_que.empty()){
    int node = pri_que.top().first; pri_que.pop();
    if(visited[node])	continue;
    for(auto& [nextNode, weight]:adjList[node]){	// 遍历邻接链表
		// 松弛条件
        if(dist[node]+weight < nextNode){
            dist[nextNode] = dist[node]+weight;
            pri_que.push(make_pair(nextNode, dist[nextNode]));
            pre[nextNode] = node;
        }
    }
}
```

> 在找距离最小的点时，可以使用**枚举**或者**构建最小堆**，当边数较少时，堆更快 O($m+nlogn$)；当节点数较少时枚举更快O($n^2$)。



#### 其他图相关知识点

① **树的直径**：树的定义其实非常广泛，一个连通无环图就可以称为树。而树的直径就是图中任意两个节点间的最远距离，要求解树的直径(**端点+长度**)，可以使用两次`dfs`解决：首先选择任意一个节点A，从A出发找到最远节点`x`，从`x`出发找到最远节点`y`，那么`x`和`y`就是直径的两个端点，它们之间的距离就是直径。

② **树的中心**：树中的一个节点，其到其他每个点的距离最大值最小（题310 - 最小高度树），这个点称为树的中心。树的中心有两种解法：a. 先求出树的直径，直径上的中点即为树的中心；b. 使用拓扑排序，从叶子节点一层一层往上搜，最后剩下的节点即为树的中心。

> 需要注意，树的中心不止一个，但最多两个。当直径为偶数时，中心就有两个。
