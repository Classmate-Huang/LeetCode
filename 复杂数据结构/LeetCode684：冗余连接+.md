# LeetCode684：[冗余连接](https://leetcode-cn.com/problems/redundant-connection/description/)

## 题目

树可以看成是一个连通且 **无环** 的 **无向** 图。

给定往一棵 `n` 个节点 (节点值 `1～n`) 的树中添加一条边后的图。添加的边的两个顶点包含在 `1` 到 `n` 中间，且这条附加的边不属于树中已存在的边。图的信息记录于长度为 `n` 的二维数组 `edges` ，`edges[i] = [ai, bi]` 表示图中在 `ai` 和 `bi` 之间存在一条边。

请找出一条可以删去的边，删除后可使得剩余部分是一个有着 `n` 个节点的树。如果有多个答案，则返回数组 `edges` 中最后出现的边。

 

**示例 1：**

![img](https://pic.leetcode-cn.com/1626676174-hOEVUL-image.png)

```
输入: edges = [[1,2], [1,3], [2,3]]
输出: [2,3]
```

**示例 2：**

![img](https://pic.leetcode-cn.com/1626676179-kGxcmu-image.png)

```
输入: edges = [[1,2], [2,3], [3,4], [1,4], [1,5]]
输出: [1,4]
```

 

**提示:**

- `n == edges.length`
- `3 <= n <= 1000`
- `edges[i].length == 2`
- `1 <= ai < bi <= edges.length`
- `ai != bi`
- `edges` 中无重复元素
- 给定的图是连通的 

## 解法一：并查集

如题目所提示的那样，树本身是一个连通且无环的无向图，因此**任意两个节点之间只能存在一条路径**。因此可以利用并查集的**合并、查询**操作可以很方便地解决这个问题。代码如下：

```c++
class DisJointSetUnion{
public:
    vector<int> f;
    
    DisJointSetUnion(int n): f(n+1){
        for(int i=1; i<=n; i++){
            f[i] = i;
        }
    }

    int find(int i){
        return f[i] = f[i]==i ? i : find(f[i]);
    }

    void merge(int x, int y){
        int rootx = find(x), rooty = find(y);
        if(rootx != rooty){
            f[rootx] = rooty;
        }
    }
};

class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        DisJointSetUnion JSU(n);
        vector<int> ans(2);
        // 遍历所有边
        for(auto& edge:edges){
            int x = edge[0], y = edge[1];
            // 两个节点之前已存在连通路径【即在同一个集合中】
            if(JSU.find(x) == JSU.find(y)){
                ans[0] = x;
                ans[1] = y;
                break;
            }
            // 合并操作
            JSU.merge(x, y);
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)