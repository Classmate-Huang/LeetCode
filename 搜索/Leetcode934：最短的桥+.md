# Leetcode934：最短的桥

## 题目

在给定的二维二进制数组 `A` 中，存在两座岛。（岛是由四面相连的 `1` 形成的一个最大组。）

现在，我们可以将 `0` 变为 `1`，以使两座岛连接起来，变成一座岛。

返回必须翻转的 `0` 的最小数目。（可以保证答案至少是 `1` 。）



**示例 1：**

```
输入：A = [[0,1],[1,0]]
输出：1
```

**示例 2：**

```
输入：A = [[0,1,0],[0,0,0],[0,0,1]]
输出：2
```

**示例 3：**

```
输入：A = [[1,1,1,1,1],[1,0,0,0,1],[1,0,1,0,1],[1,0,0,0,1],[1,1,1,1,1]]
输出：1
```

 

**提示：**

- `2 <= A.length == A[0].length <= 100`
- `A[i][j] == 0` 或 `A[i][j] == 1`



## 解法一：DFS + BFS

问题转换为求两岛之间的最短距离，这可以利用广度优先搜索，从A岛的边界一层一层向外扩展，直到达到另B岛的边界。因此，首先需要将这两个岛区分开来（目前全是1），这里利用DFS遍历某个岛，将其元素转为2（同时将该岛元素全部入队列）。接下来再从其中一个岛出发，利用BFS遍历得到距离。代码如下：

```c++
class Solution {
public:
    vector<pair<int, int>> sites = {{0,1},{0,-1},{1,0},{-1,0}};
	// DFS遍历
    void dfs(vector<vector<int>>& grid, queue<pair<int, int>>& q, int x, int y){
        grid[x][y] = 2;	// 元素置为2
        q.push(make_pair(x, y));	// 入队
        for(auto t:sites){
            int tx = x+t.first, ty = y+t.second;
            if(tx>=0 && tx<grid.size() && ty>=0 && ty<grid[0].size()){
                if(grid[tx][ty]==1){
                    dfs(grid, q, tx, ty);
                }
            }
        }
        
    }

    int shortestBridge(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        queue<pair<int, int>> q;
        // divide into 2 island by DFS (all elemet is 1 or 2) 区分两个岛
        bool flag = false;
        for(int i=0; i<m; i++){	// 遍历到某个岛的元素即可退出循环
            if(flag)    break;
            for(int j=0; j<n; j++){
                if(grid[i][j]==1){ 
                    dfs(grid, q, i, j);
                    flag = true;
                    break;
                }
            }
        }
        // get distance by BFS 利用BFS求距离
        int dist = 0;
        while(!q.empty()){
            int k = q.size();
            while(k--){	// 层次遍历
                int x = q.front().first, y = q.front().second;
                q.pop();
                for(auto t:sites){
                    int tx = x+t.first, ty = y+t.second;
                    if(tx>=0 && tx<grid.size() && ty>=0 && ty<grid[0].size()){
                        if(grid[tx][ty]==0){ // 遍历到海洋，入队
                            q.push(make_pair(tx, ty));
                            grid[tx][ty]=2;
                        }
                        else if(grid[tx][ty]==1){ // 遍历到B岛，返回结果
                            return dist;
                        }
                    }
                }
            }
            dist++;
        }
        return dist;
    }
};
```

时间复杂度：O(MN)，M，N分别为行列数

空间复杂度：O(MN)