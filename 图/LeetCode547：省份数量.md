# LeetCode547：省份数量

## 题目

有 `n` 个城市，其中一些彼此相连，另一些没有相连。如果城市 `a` 与城市 `b` 直接相连，且城市 `b` 与城市 `c` 直接相连，那么城市 `a` 与城市 `c` 间接相连。

**省份** 是一组直接或间接相连的城市，组内不含其他没有相连的城市。

给你一个 `n x n` 的矩阵 `isConnected` ，其中 `isConnected[i][j] = 1` 表示第 `i` 个城市和第 `j` 个城市直接相连，而 `isConnected[i][j] = 0` 表示二者不直接相连。

返回矩阵中 **省份** 的数量。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/12/24/graph1.jpg)

```
输入：isConnected = [[1,1,0],[1,1,0],[0,0,1]]
输出：2
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/12/24/graph2.jpg)

```
输入：isConnected = [[1,0,0],[0,1,0],[0,0,1]]
输出：3
```

 

**提示：**

- `1 <= n <= 200`
- `n == isConnected.length`
- `n == isConnected[i].length`
- `isConnected[i][j]` 为 `1` 或 `0`
- `isConnected[i][i] == 1`
- `isConnected[i][j] == isConnected[j][i]`

## 解法一：图的搜索

使用图的搜索，遍历所有节点，每个连通区域就是一个省份。需要注意的是，要构造一个`unordered_set`或者`vector`来判断当前点是否被遍历过。以深度优先遍历为例：

```c++
class Solution {
public:
    vector<bool> visited;
	// 深度优先遍历
    void dfs(int node, vector<vector<int>>& M) {
        visited[node] = true;
        for (int j = 0; j < M.size(); j++) {
            if (M[node][j] && !visited[j]) {
                dfs(j, M);
            }
        }
    }

    int findCircleNum(vector<vector<int>>& M) {
        int n = M.size(), ans = 0;
        visited = vector<bool> (n, false);

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(i, M);
                ans++;
            }
        }

        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O($n$)

## 解法二：并查集

使用并查集，当两个节点之间存在边时，那他们属于同一个集合。最后统计有多少个独立集合即可，代码如下：

```c++
class DJS {
public:
    vector<int> father;
    DJS(int n) {
        father = vector<int> (n);
        for(int i = 0; i < n; i++)  father[i] = i;
    }
    int find(int x) {
        father[x] = father[x] == x ? x : find(father[x]);
        return father[x];
    }
    void merge(int x, int y) {
        int rootx = find(x), rooty = find(y);
        if (rootx != rooty) {
            father[rootx] = rooty;
        }
    }
};


class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        int n = M.size();
        DJS djs = DJS(n);

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (M[i][j] == 1)   djs.merge(i, j);
            }
        }

        int ans = 0;
        unordered_set<int> root;
        for (int i = 0; i < n; i++) {
            int t = djs.find(i);
            if (root.find(t) == root.end()) {
                ans++;
                root.insert(t);
            }
        }

        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O($n$)
