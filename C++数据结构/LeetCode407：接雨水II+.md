# LeetCode407：接雨水II

## 题目

给你一个 m x n 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。

示例 1:

![img](https://assets.leetcode.com/uploads/2021/04/08/trap1-3d.jpg)

```
输入: heightMap = [[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]]
输出: 4
解释: 下雨后，雨水将会被上图蓝色的方块中。总的接雨水量为1+2+1=4。
```

示例 2:

![img](https://assets.leetcode.com/uploads/2021/04/08/trap2-3d.jpg)

```
输入: heightMap = [[3,3,3,3,3],[3,2,2,2,3],[3,2,1,2,3],[3,2,2,2,3],[3,3,3,3,3]]
输出: 10
```


提示:

```
m == heightMap.length
n == heightMap[i].length
1 <= m, n <= 200
0 <= heightMap[i][j] <= 2 * 104
```

## 解法一：优先队列（木桶效应）

在2维的接雨水问题中，根据木桶效应（一个木桶的储水量取决于最短的一块木板），我们使用双指针来移动左、右边界，从而计算每个方块的储水量。本题可以使用同样的思想，只不过初始化边界不再是两个点，而是整个外围的一圈方块(无法储水)。

我们先将边界放入一个优先队列中（最小堆，每次获取得到最矮的边界方块），然后选择最堆顶元素进行传播(向上下左右扩散)。如果遇到一个未遍历过的方块，其高度比当前边界元素矮，那说明可以储水，更新答案，并将该方块压入队列中；如果比当前元素高，虽然不能储水，但边界可以更新了，将该方块压入队列。

代码如下：

```c++
struct cmp{ // 小顶堆
    bool operator() (vector<int> a, vector<int> b) {
        return a[0] > b[0];
    }
};

class Solution {
public:
    vector<vector<int>> offsets = { {0, 1}, {0, -1}, {1, 0}, {-1, 0} };
    
    int trapRainWater(vector<vector<int>>& heightMap) {
        int m = heightMap.size(), n = heightMap[0].size();
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        priority_queue<vector<int>, vector<vector<int>>, cmp> pri_que;
        
        // 先把四周的方块加入小顶堆中
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 || i == m-1 || j == 0 || j == n-1) {
                    pri_que.push({heightMap[i][j], i, j});
                    visited[i][j] = true;       
                }
            }
        }
        
        // 根据木桶效应，每次选择最矮的边界方块进行更新
        int ans = 0;
        while (!pri_que.empty()) {
            int h = pri_que.top()[0], x = pri_que.top()[1], y = pri_que.top()[2];
            pri_que.pop();
            for (auto off : offsets) {
                int xt = x + off[0], yt = y + off[1];
                if (xt < 0 || xt >= m || yt < 0 || yt >= n || visited[xt][yt])  continue;
                if (heightMap[xt][yt] < h)  ans += h - heightMap[xt][yt];
                visited[xt][yt] = true;
                pri_que.push({max(heightMap[xt][yt], h), xt, yt});  // 压入新边界
            }
        }
        
        return ans;
    }
};
```

时间复杂度：O(M·N·log(MN))

空间复杂度：O(M·N)