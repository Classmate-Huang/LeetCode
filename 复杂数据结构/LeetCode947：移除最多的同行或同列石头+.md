# LeetCode947：移除最多的同行或同列石头

## 题目

`n` 块石头放置在二维平面中的一些整数坐标点上。每个坐标点上最多只能有一块石头。

如果一块石头的 **同行或者同列** 上有其他石头存在，那么就可以移除这块石头。

给你一个长度为 `n` 的数组 `stones` ，其中 `stones[i] = [xi, yi]` 表示第 `i` 块石头的位置，返回 **可以移除的石子** 的最大数量。

 

**示例 1：**

```
输入：stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]
输出：5
解释：一种移除 5 块石头的方法如下所示：
1. 移除石头 [2,2] ，因为它和 [2,1] 同行。
2. 移除石头 [2,1] ，因为它和 [0,1] 同列。
3. 移除石头 [1,2] ，因为它和 [1,0] 同行。
4. 移除石头 [1,0] ，因为它和 [0,0] 同列。
5. 移除石头 [0,1] ，因为它和 [0,0] 同行。
石头 [0,0] 不能移除，因为它没有与另一块石头同行/列。
```

**示例 2：**

```
输入：stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]
输出：3
解释：一种移除 3 块石头的方法如下所示：
1. 移除石头 [2,2] ，因为它和 [2,0] 同行。
2. 移除石头 [2,0] ，因为它和 [0,0] 同列。
3. 移除石头 [0,2] ，因为它和 [0,0] 同行。
石头 [0,0] 和 [1,1] 不能移除，因为它们没有与另一块石头同行/列。
```

**示例 3：**

```
输入：stones = [[0,0]]
输出：0
解释：[0,0] 是平面上唯一一块石头，所以不可以移除它。
```

 

**提示：**

- `1 <= stones.length <= 1000`
- `0 <= xi, yi <= 104`
- 不会有两块石头放在同一个坐标点上



## 解法一：建图+BFS搜索

首先需要将问题进行转换成图结构，可以分析知道，每两个石头在同一行或同一列时就可以只保留一个，那么把在同一行、同一列的石头放在建立连接，问题就变成了判断图连通分支数（每个连通分支只保留一个石头，那么答案就是石头个数-连通分支数）。

解法一首先采用比较暴力的方法，直接两两判断石头之间是否连通，建图之后再利用BFS/DFS查找连通分支数即可。代码如下：

```c++
class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        // bulid Adjacency matrix 构建邻接矩阵
        int n = stones.size();
        vector<vector<int>> M(n);
        // bulid graph 建图
        for(int i=0; i<n; i++){
            for(int j=0; j<n; j++){
                if(stones[i][0]==stones[j][0] || stones[i][1]==stones[j][1])
                    M[i].push_back(j);
            }
        }

        vector<int> unseen(n, 1);
        queue<int> q;
        int branch = 0;
        // BFS 搜索
        for(int i=0; i<n; i++){
            if(unseen[i]){
                q.push(i);
                unseen[i] = 0;
                branch++;
            }
            while(!q.empty()){
                int node = q.front();
                q.pop();
                for(auto adjNode:M[node]){
                    if(unseen[adjNode]){
                        q.push(adjNode);
                        unseen[adjNode] = 0;
                    }
                }
            }
        }
        return n-branch;
    }
};
```

时间复杂度：O($N^2$)，主要浪费在建图上

空间复杂度：O($N^2$)，存储图结构

## 解法二：优化建图+DFS搜索

其实在本题中，我们并不关注具体的两个点是否存在连接，而只想知道哪些点在同一个连通分支中。因此对于一个n个点的图，我们只用n-1边来表示其连通性。首先使用Map存储同一行/列的石头节点，然后建图时只构建n-1条边。可能有点晕，配合代码食用即可(这里练练DFS)：

```c++
class Solution {
public:
    // DFS 记得加&，不然每次拷贝的时间复杂度爆炸
    void DFS(int node, vector<vector<int>> &edge, vector<int>& unseen){
        unseen[node] = 0;
        for(int &nextNode:edge[node]){
            if(unseen[nextNode]){
                DFS(nextNode, edge, unseen);
            }
        }
    }
	
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        vector<vector<int> > edge(n);
        unordered_map<int, vector<int> > m;
        // build graph in a better way 优化建图
        for(int i=0; i<n; i++){
            m[stones[i][0]].push_back(i);	// row 行
            m[stones[i][1]+10001].push_back(i);	// col 列 偏移10^4表示
        }
        for(auto &[_, vec]:m){
            int k = vec.size();
            for(int i=1; i<k; i++){
                // 只保留前后两个节点的边
                edge[vec[i-1]].push_back(vec[i]);
                edge[vec[i]].push_back(vec[i-1]);
            }
        }
        // DFS 搜索
        int branch = 0;
        vector<int> unseen(n, 1);
        for(int i=0; i<n; i++){
            if(unseen[i]){
                DFS(i, edge, unseen);
                branch++;
            }
        }
        return n-branch;
    }
};
```

时间复杂度：O(N)，优化建图时每个每节点最多连4条边；

空间复杂度：O(N)

## 解法三：哈希+并查集

对于判断连通性的另一个方法就是采用并查集，首先使用哈希表存储同一行/列的石头节点，方法同解法二。然后构建并查集，将同一列/行的石头计入一个连通分支，最后判断有多少个连通分支即可。代码如下：

```c++
class DisJointSetUnion{
private:
    vector<int> f;	// father node
public:
    DisJointSetUnion(int n):f(vector<int> (n)){	// 构造函数
        for(int i=0; i<n; i++){	// 初始化父节点
            f[i] = i;
        }
    }

    int find(int x){	// find x's father node
        f[x] = x==f[x]?x:find(f[x]);
        return f[x];
    }

    void merge(int x, int y){	// merge x, y node to a branch
        int rootX = find(x), rootY = find(y);
        f[rootX] = rootY;
    }

    int numberBranch(){	// return connected branch number
        int n = 0;
        for(int i=0; i<f.size(); i++){
            if(i==f[i]) n++;
        }
        return n;
    }
};

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        // 记录同一行/列的石头节点
        unordered_map<int, vector<int> > m;
        for(int i=0; i<n; i++){
            m[stones[i][0]].push_back(i);
            m[stones[i][1]+10000].push_back(i);
        }
		// 查并集
        DisJointSetUnion dsu(n);
        for(auto &[_, vec]:m){
            int k = vec.size();
            for(int i=1; i<k; i++){
                dsu.merge(vec[i-1], vec[i]);
            }
        }

        return n - dsu.numberBranch();

    }
};
```

时间复杂度：涉及到并差查，较复杂

空间复杂度：O(N)
