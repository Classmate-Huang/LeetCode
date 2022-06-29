# LeetCode周赛284

## 题目一： [找出数组中的所有 K 近邻下标](https://leetcode-cn.com/problems/find-all-k-distant-indices-in-an-array/)

在比赛的时候，我是直接先找到每个key值所在的下标索引，然后一个个遍历其k近邻范围（当时为了防止重复，还额外用了一个vector来去重）。但后来看题解，发现一种巧妙的方案，就是使用双指针。指针fast先遍历，如果遍历到了key就用slow来存入结果。需要注意细节： i-k和i+k可能会存在越界，所以需要min和max来约束。代码如下：

```c++
class Solution {
public:
    vector<int> findKDistantIndices(vector<int>& nums, int key, int k) {
        vector<int> ans;
        int slow = 0, fast = 0, n = nums.size();
        for(int i=0; i<nums.size(); i++){
            if(nums[i] == key){
                for(slow=max(slow, i-k); slow<=min(i+k, n-1); slow++){
                    ans.push_back(slow);
                }
            }
        }
        return ans;
    }
};
```

## 题目二：[统计可以提取的工件](https://leetcode-cn.com/problems/count-artifacts-that-can-be-extracted/)

其实这个题目，直接暴力搜索就能过：记录可挖掘的区域，然后遍历工件，判断当前是否被挖掘即可。但我在比赛的时候第一时间想到的是利用积分图（因为每个工件都是一个矩形），思想其实与暴力是一致的，`f[i][j]`表示`[0:i, 0:j]`矩形区域中为1的和（记录可挖掘区域），对于每个工件，直接判断每个工件对应的矩形区域内子矩阵的和是否与面积相等即可。代码如下：

```c++
class Solution {
public:
    int digArtifacts(int n, vector<vector<int>>& artifacts, vector<vector<int>>& dig) {
        // 地图上为1表示可挖掘grid
        vector<vector<int>> digMap(n, vector<int>(n, 0));
        for(auto& d:dig){
            int r = d[0], c = d[1];
            digMap[r][c] = 1;
        }
        // 计算积分图
        vector<vector<int>> digF(n+1, vector<int>(n+1, 0));     // digF[i][j]表示[0,0]~[i-1, j-1]区域1的个数
        for(int i=1; i<=n; i++){
            for(int j=1; j<=n; j++){
                digF[i][j] = digF[i-1][j] + digF[i][j-1] - digF[i-1][j-1] + digMap[i-1][j-1];
            }
        }
        // 遍历工件区域
        int ans = 0;
        for(auto& artifact:artifacts){
            int r1 = artifact[0], c1 = artifact[1], r2 = artifact[2], c2 = artifact[3];
            int area = (r2-r1+1) * (c2-c1+1); // 计算工件所占面积
            int digArea = digF[r2+1][c2+1] - digF[r1][c2+1] - digF[r2+1][c1] + digF[r1][c1];
            if(digArea == area) ans++;	// 相等即加1
        }
        return ans;
    }
};
```

## 题目三：[K 次操作后最大化顶端元素](https://leetcode-cn.com/problems/maximize-the-topmost-element-after-k-moves/)

这个题目是真的坑，在比赛的时候和后来定榜的时候题目内容都不一样，真的服了。题目本身并不难，分情况讨论即可，代码如下：

```c++
class Solution {
public:
    int maximumTop(vector<int>& nums, int k) {
        int n = nums.size();
        if(k==0)    return nums[0];     // 操作次数为0， 因此后面操作数k一定大于1
        // 只有一个元素时
        if(n==1 && k%2==0)      return nums[0];
        if(n==1 && k%2==1)      return -1;
        // 当k>n时，直接能找到最大值
        if(k>n) return *max_element(nums.begin(), nums.end());
        // k<=n时 k一定可以选取到前k-1个和第k+1个最大的数
        int ans = 0;
        for(int i=0; i<k-1; i++)    ans = max(ans, nums[i]);
        if(k<n)   ans = max(ans, nums[k]);
        return ans;
    }
};
```

## 题目四：[得到要求路径的最小带权子图](https://leetcode-cn.com/problems/minimum-weighted-subgraph-with-the-required-paths/)

这个题目非常有意思，给出两个源点一个目标点，在有向图中去找一条路径最小的值。首先，对整幅图进行反向，针对目标点用一次`Dijkstra`，这样就把所有点到目标点的最短路径求出来了；然后针对正向图，对两个点用一次`Dijkstra`，求得两个点到图中每个点的最短路径。枚举中间点，计算路径($dist_{src_1-t}$+$dist_{src_2-t}$+$dist_{t-target}$)（任何一个点都有可能，值最小的路径即为答案）。

```c++
struct cmp{
    bool operator()(pair<int, long long> a, pair<int, long long> b){
        return a.second > b.second;
    }
};

class Solution {
public:
    // Dijkstra - 返回src点到其他每个点的最短距离
    vector<long long> Dijkstra( int n, vector<vector<pair<int, int>>>& adjList, int src){
        vector<long long> dist(n, 1e12);
        vector<int> pre(n, -1);
        vector<bool> visited(n, false);
        priority_queue<pair<int, long long>, vector<pair<int, long long>>, cmp> pri_que;
        // 从源点出发
        dist[src] = 0;
        pri_que.push(make_pair(src, dist[src]));
        while(!pri_que.empty()){
            int node = pri_que.top().first;
            pri_que.pop();
            if(visited[node])   continue;
            visited[node] = true;
            for(auto& [nextNode, weight]:adjList[node]){
                if(visited[nextNode])   continue;
                if(dist[node]+weight < dist[nextNode]){
                    dist[nextNode] = dist[node]+weight;
                    pre[nextNode] = node;
                    pri_que.push(make_pair(nextNode, dist[nextNode]));
                }
            }
        }
        return dist;
    }

    long long minimumWeight(int n, vector<vector<int>>& edges, int src1, int src2, int dest) {
        vector<vector<pair<int, int>>> adjList(n); // 正向图
        vector<vector<pair<int, int>>> ReverseAdjList(n);  // 反向图
        for(auto& edge:edges){
            int from = edge[0], to = edge[1], weight = edge[2];
            adjList[from].push_back(make_pair(to, weight));
            ReverseAdjList[to].push_back(make_pair(from, weight));
        }
        // 得到dest到所有点的最短距离
        vector<long long> destDist = Dijkstra(n, ReverseAdjList, dest);
        // 得到src1和src2分别到所有点的最短距离
        vector<long long> src1Dist = Dijkstra(n, adjList, src1);
        vector<long long> src2Dist = Dijkstra(n, adjList, src2);
        // 枚举中间点
        long long ans = 1e12;
        for(int i=0; i<n; i++){
            ans = min(ans, src1Dist[i]+src2Dist[i]+destDist[i]);
        }
        return ans == 1e12 ? -1 : ans;
    }
};
```

