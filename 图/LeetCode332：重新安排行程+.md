# LeetCode332：重新安排行程

## 题目

给你一份航线列表 `tickets` ，其中 `tickets[i] = [fromi, toi]` 表示飞机出发和降落的机场地点。请你对该行程进行重新规划排序。

所有这些机票都属于一个从 `JFK`（肯尼迪国际机场）出发的先生，所以该行程必须从 `JFK` 开始。如果存在多种有效的行程，请你按字典排序返回最小的行程组合。

- 例如，行程 `["JFK", "LGA"]` 与 `["JFK", "LGB"]` 相比就更小，排序更靠前。

假定所有机票至少存在一种合理的行程。且所有的机票 必须都用一次 且 只能用一次。

 

**示例 1：**

![img](LeetCode332：重新安排行程+.assets/itinerary1-graph.jpg)

```
输入：tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
输出：["JFK","MUC","LHR","SFO","SJC"]
```

**示例 2：**

![img](LeetCode332：重新安排行程+.assets/itinerary2-graph.jpg)

```
输入：tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
输出：["JFK","ATL","JFK","SFO","ATL","SFO"]
解释：另一种有效的行程是 ["JFK","SFO","ATL","JFK","ATL","SFO"] ，但是它字典排序更大更靠后。
```

 

**提示：**

- `1 <= tickets.length <= 300`
- `tickets[i].length == 2`
- `fromi.length == 3`
- `toi.length == 3`
- `fromi` 和 `toi` 由大写英文字母组成
- `fromi != toi`



## 解法一： Hierholzer算法

关于欧拉图的知识回顾：

> 通过图中所有边恰好一次且行遍所有顶点的通路称为欧拉通路; 通过图中所有边恰好一次且行遍所有顶点的回路称为欧拉回路。具有欧拉回路的无向图称为欧拉图;具有欧拉通路但不具有欧拉回路的无向图称为半欧拉图。
>
> 如何判断是否为欧拉图：对于无向图G，G是欧拉图当且仅当G是连通的且没有奇度顶点。对于无向图G，G是半欧拉图当且仅当G是连通的且G中恰有2个奇度顶点。
>
> 对于有向图G，G是欧拉图当且仅当G的所有顶点属于同一个强连通分量且每个顶点的入度和出度相同。对于有向图G，G是半欧拉图当且仅当G的所有顶点属于同一个强连通分量且恰有一个顶点的出度与入度差为1 + 恰有一个顶点的入度与出度差为1 + 所有其他顶点的入度和出度相同。

本题保证能存在合理的路径，那么也就确保了一定是欧拉图或半欧拉图。如果是欧拉图，直接递归搜索就行，每次贪心去搜索最小的地点。但如果是半欧拉图，可能会进入死胡同而导致不能遍历剩余节点。如下图的情况：

<img src="LeetCode332：重新安排行程+.assets/332_fig2.png" alt="Graph2" style="zoom: 25%;" />

因此采用Hierholzer算法，用于在连通图中找寻欧拉路径：1. 从起点出发，进行深度优先搜索；2. 每次沿着某条边从某个顶点移动到另外一个顶点的时候，都需要删除这条边；3. 如果没有可移动的路径，则将所在节点加入到栈中，并返回。

也就是说，只有遍历了一个结点的所有路径才会将这个节点放入结果队列中，这样的做法会让**死胡同**路径上的节点优先进入队列。最后将结果进行reverse，即可得到最终结果。

> 相似题型：753

应用不同的数据结构下，有两种做法，一是使用`unordered_map + priority_queue`找寻当前最小字母序的目的地，代码如下：

```c++
class Solution {
public:
    
    unordered_map<string, priority_queue<string, vector<string>, greater<string>>> m;
    vector<string> ans;

    void dfs(const string& s){
        while(m.find(s)!=m.end() && !m[s].empty()){
            string next = m[s].top();
            m[s].pop();
            dfs(next);
        }
        ans.push_back(s);
    }

    vector<string> findItinerary(vector<vector<string>>& tickets) {
        // 构建多重映射 - 使用优先队列来找到最小字母序
        for(auto& t:tickets){
            m[t[0]].push(t[1]);
        }
        // DFS
        dfs("JFK");

        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

时间复杂度：O($mlogm$)，m为边数

空间复杂度：O(m)，递归所占用的资源

此外，还可以使用`unordered_map + multiset`达到同样的目的，代码如下：

```c++
class Solution {
public:
    
    unordered_map<string, multiset<string>> m;
    vector<string> ans;

    void dfs(const string& s){
        while(m.find(s)!=m.end() && !m[s].empty()){
            string next = *m[s].begin();
            m[s].erase(m[s].begin());
            dfs(next);
        }
        ans.push_back(s);
    }

    vector<string> findItinerary(vector<vector<string>>& tickets) {
        // 构建多重映射 - 使用优先队列来找到最小字母序
        for(auto& t:tickets){
            m[t[0]].insert(t[1]);
        }
        // DFS
        dfs("JFK");

        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

