# LeetCode210：课程表II

## 题目

现在你总共有 numCourses 门课需要选，记为 0 到 numCourses - 1。给你一个数组 prerequisites ，其中 prerequisites[i] = [ai, bi] ，表示在选修课程 ai 前 必须 先选修 bi 。

例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示：[0,1] 。
返回你为了学完所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回 任意一种 就可以了。如果不可能完成所有课程，返回 一个空数组 。

 

示例 1：

```
输入：numCourses = 2, prerequisites = [[1,0]]
输出：[0,1]
解释：总共有 2 门课程。要学习课程 1，你需要先完成课程 0。因此，正确的课程顺序为 [0,1] 。
```

示例 2：

```
输入：numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
输出：[0,2,1,3]
解释：总共有 4 门课程。要学习课程 3，你应该先完成课程 1 和课程 2。并且课程 1 和课程 2 都应该排在课程 0 之后。
因此，一个正确的课程顺序是 [0,1,2,3] 。另一个正确的排序是 [0,2,1,3] 。
```

示例 3：

```
输入：numCourses = 1, prerequisites = []
输出：[0]
```

提示：

```
1 <= numCourses <= 2000
0 <= prerequisites.length <= numCourses * (numCourses - 1)
prerequisites[i].length == 2
0 <= ai, bi < numCourses
ai != bi
所有[ai, bi] 互不相同
```

## 解法一：拓扑排序

典型的拓扑排序题型，拓扑排序：首先构建邻接矩阵，同时统计每个节点的入度值。将所有入度为0的节点压入队列，执行`BFS`，每处理到一个节点（加入`ans`），就要消除它后续节点的影响(相邻节点入度-1)，同时把新的入度为0的节点压入队列。代码如下：

```c++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> adjList(numCourses);
        vector<int> inDegree(numCourses, 0);
        // 构建邻接矩阵与入度
        for (auto temp : prerequisites) {
            adjList[temp[1]].push_back(temp[0]);
            inDegree[temp[0]]++;
        }
        // 拓扑排序
        queue<int> que;
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0)   que.push(i);
        }
        vector<int> ans;
        while (!que.empty()) {
            int node = que.front();
            que.pop();
            ans.push_back(node);
            for (auto next : adjList[node]) {
                inDegree[next]--;
                if (inDegree[next] == 0)    que.push(next);
            }
        }

        if (ans.size() != numCourses)   return {};
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)