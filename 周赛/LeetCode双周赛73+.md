# LeetCode双周赛73

## 题目一：[2190. 数组中紧跟 key 之后出现最频繁的数字](https://leetcode-cn.com/problems/most-frequent-number-following-key-in-an-array/)（哈希）

利用哈希表记录每个`target`出现的次数，然后返回最大的那个即可。

```c++
class Solution {
public:
    int mostFrequent(vector<int>& nums, int key) {
        unordered_map<int, int> map;

        for(int i=1; i<nums.size(); i++){
            if(nums[i-1]==key)  map[nums[i]]++;
        }

        int ans = 0;
        for(auto it = map.begin(); it!=map.end(); it++){
            if(ans == 0 || it->second > map[ans]){
                ans = it->first;
            }
        }
        return ans;
    }
};
```

## 题目二：[2191. 将杂乱无章的数字排序](https://leetcode-cn.com/problems/sort-the-jumbled-numbers/)（排序模拟）

这道题，我在比赛的时候直接手写归并排序，虽然最后写出来了，但是调通归并排序花了我太长时间了，导致下一道题做完没时间提交了，害。最后发现c++已经提供好了稳定排序`stable_sort()`，这告诉我三件事，1. 多看看c++的STL书；2. 平时注意对基础算法的编码巩固；3. 多写题，多打比赛。

这个题目要实现起来非常简单，也不需要稳定排序，直接构造一个`pair<int,int>`关键字，进行多关键字排序，分别记录转换后的值和对应的索引。代码如下：

```c++
class Solution {
public:
    
    vector<int> sortJumbled(vector<int>& mapping, vector<int>& nums) {
        int n = nums.size();
        
        vector<pair<int, int>> temp;
        // 化成mapping映射后的数值
        for(int i=0; i<n; i++){
            string s = to_string(nums[i]);
            int res = 0;
            for(auto& c:s){
                res = res*10 + mapping[c-'0'];
            }
            temp.push_back(make_pair(res, i));
        }
        sort(temp.begin(), temp.end());
        
        vector<int> ans;
        for(auto& t:temp)   ans.push_back(nums[t.second]);

        return ans;
    }
};
```

## 题目三：[2192. 有向无环图中一个节点的所有祖先](https://leetcode-cn.com/problems/all-ancestors-of-a-node-in-a-directed-acyclic-graph/)（BFS）

这个题目我在比赛的时候，用了`拓扑排序 + 数组记录`的方法，但其实直接上`BFS`就能解决，对每个元素都进行一次`BFS`，给对应的节点在ans中新增祖先节点即可。代码如下：

```c++
class Solution {
public:

    void bfs(vector<vector<int>>& adjList, vector<vector<int>>&ans, int root){
        queue<int> que;
        vector<bool> visited(adjList.size(), false);
        que.push(root);
        visited[root] = true;
        while(!que.empty()){
            int node = que.front(); que.pop();
            for(auto& nextNode:adjList[node]){
                if(!visited[nextNode]){
                    ans[nextNode].push_back(root);
                    que.push(nextNode);
                    visited[nextNode] = true;
                }
            }
        }
    }


    vector<vector<int>> getAncestors(int n, vector<vector<int>>& edges) {
        
        vector<vector<int>> ans(n);
        vector<vector<int>> adjList(n);    // 构造邻接链表    
        for(auto& edge:edges)   adjList[edge[0]].push_back(edge[1]);
        
        // BFS 
        for(int i=0; i<n; i++)  bfs(adjList, ans, i);
        
        return ans; // 由于是从0遍历到n-1，因此不需要再排序
    }
};
```

## 题目四：[2193. 得到回文串的最少操作次数](https://leetcode-cn.com/problems/minimum-number-of-moves-to-make-palindrome/) *

> 证明过程请参考评论区和题解区，大佬真多啊

这个题目直接使用贪心策略，对于一个字符串，固定某个边界字符不动，以最小的代价完成另一个边界（构成回文）。比如`letelt`，可以固定左边`s[0]`位置上的`l`不同，将`s[4]`移动到右边界即可(交换1次)。现在就将问题缩小到判断最少操作多少次使得`etet`变为回文串。这个问题就很容易编码实现了，代码如下：

```c++
class Solution {
public:
    vector<int> cnt = vector(26, 0);    // 记录每个字符出现次数
    
    int minMove(string s){
        if(s.empty())    return 0;  // 为空直接返回
        int n = s.size();
        if(s[0] == s[n-1]){
            cnt[s[0]-'a'] -= 2; // 减去字符
            return 0 + minMove(s.substr(1, n-2));
        }
        int cnt_temp = 0;   // 交换次数
        if(cnt[s[0]-'a'] == 1){ // 左边界为奇数，则固定右边界
            int idx = 0;
            while(s[idx] != s[n-1])    idx++;   // 从左往右，找到与右边界第一个相等的索引
            while(idx>0){   // 模拟交换
                swap(s[idx], s[idx-1]);
                idx--;
                cnt_temp++;
            }
            cnt[s[0]-'a'] -= 2; // 减去字符
            return cnt_temp + minMove(s.substr(1, n-2));
        }
        else{   // 固定左边界
            int idx = n-1;
            while(s[idx]!=s[0])    idx--;   // 从右往左，找到与左边界第一个相等的索引
            while(idx<n-1){    // 模拟交换
                swap(s[idx], s[idx+1]);
                idx++;
                cnt_temp++;
            }
            cnt[s[0]-'a'] -= 2;
            return cnt_temp + minMove(s.substr(1, n-2));
        }
        
    }

    int minMovesToMakePalindrome(string s) {
        for(char& i:s)  cnt[i-'a']++;
        return minMove(s);
    }
};
```

