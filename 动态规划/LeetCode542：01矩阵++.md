# LeetCode542：01矩阵

## 题目

给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。

 

**示例 1：**

```
输入：
[[0,0,0],
 [0,1,0],
 [0,0,0]]

输出：
[[0,0,0],
 [0,1,0],
 [0,0,0]]
```

**示例 2：**

```
输入：
[[0,0,0],
 [0,1,0],
 [1,1,1]]

输出：
[[0,0,0],
 [0,1,0],
 [1,2,1]]
```

 

**提示：**

- 给定矩阵的元素个数不超过 10000。
- 给定矩阵中至少有一个元素是 0。
- 矩阵中的元素只在四个方向上相邻: 上、下、左、右。

## 解法一：BFS

先找到所有的`0`节点位置压入队列（将所有的`0`结点当做一个超级源节点），然后进行层次遍历，思路简单。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> sites = {{1,0}, {-1,0}, {0,1}, {0,-1}};
	
    void BFS(vector<vector<int>>& mat, vector<vector<int>>& ans, queue<pair<int, int>>& q){
        int depth = 1, x, y, xt, yt;
        while(!q.empty()){
            int k = q.size();
            while(k--){
                auto node = q.front();
                q.pop();
                x = node.first, y = node.second;
                for(auto& offset:sites){
                    xt = x+offset[0], yt = y+offset[1];
                    if(xt>=0 && xt<mat.size() && yt>=0 && yt<mat[0].size()){
                        if(mat[xt][yt]==0){
                            continue;
                        }
                        else if(ans[xt][yt]==0 || ans[xt][yt]>depth){
                            ans[xt][yt] = depth;
                            q.push(make_pair(xt, yt));
                        }
                    }
                }
            }
            depth++;
        }
    }

    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int m = mat.size(), n = mat[0].size();
        vector<vector<int>> ans(m, vector<int>(n, 0));
        queue<pair<int,int>> q;

        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(mat[i][j]==0){
                    q.push(make_pair(i,j));
                }
            }
        }
        BFS(mat, ans, q);

        return ans;
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(MN)

## 解法二：DP

回想一下`64题`最小路径和，它的路径规则只有左向路径/上向路径，现在看看这一题。1到最近的0，有四种路径方式：

① 向左，向上； ② 向右，向上； ③ 向左，向下； ④ 向右，向下。

只需要遍历完这四种情况，即可得到最终的解。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int m = mat.size(), n = mat[0].size(), MAX = 1e6;

        vector<vector<int>> f(m, vector<int>(n, MAX));
        queue<pair<int,int>> q;
        
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(mat[i][j]==0){
                    f[i][j] = 0;
                }
            }
        }
        // 1. left + up
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                int up = i>0?f[i-1][j]:MAX;
                int left = j>0?f[i][j-1]:MAX;
                f[i][j] = min(f[i][j], min(up, left)+1);
            }
        }
        // 2. right + up
        for(int i=0; i<m; i++){
            for(int j=n-1; j>=0; j--){
                int up = i>0?f[i-1][j]:MAX;
                int right = j<n-1?f[i][j+1]:MAX;
                f[i][j] = min(f[i][j], min(up, right)+1);
            }
        }
        // 3. left + down
        for(int i=m-1; i>=0; i--){
            for(int j=0; j<n; j++){
                int down = i<m-1?f[i+1][j]:MAX;
                int left = j>0?f[i][j-1]:MAX;
                f[i][j] = min(f[i][j], min(down, left)+1);
            }
        }
        // 4. right + down
        for(int i=m-1; i>=0; i--){
            for(int j=n-1; j>=0; j--){
                int down = i<m-1?f[i+1][j]:MAX;
                int right = j<n-1?f[i][j+1]:MAX;
                f[i][j] = min(f[i][j], min(down, right)+1);
            }
        }

        return f;
    }
};
```

时间复杂度：O(MN)

空间复杂度：O(1)

## 解法三：解法二的常数优化

解法二中4条路径，对每个方向重复地遍历了2次，其实可以优化到只使用2条路径：① left + up； ② right + down。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int m = mat.size(), n = mat[0].size(), MAX = 1e6;

        vector<vector<int>> f(m, vector<int>(n, MAX));
        queue<pair<int,int>> q;

        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(mat[i][j]==0){
                    f[i][j] = 0;
                }
            }
        }
        // 1. left + up
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                int up = i>0?f[i-1][j]:MAX;
                int left = j>0?f[i][j-1]:MAX;
                f[i][j] = min(f[i][j], min(up, left)+1);
            }
        }
        // 2. right + down
        for(int i=m-1; i>=0; i--){
            for(int j=n-1; j>=0; j--){
                int down = i<m-1?f[i+1][j]:MAX;
                int right = j<n-1?f[i][j+1]:MAX;
                f[i][j] = min(f[i][j], min(down, right)+1);
            }
        }

        return f;
    }
};
```

