# LeetCode218：天际线问题

## 题目

城市的天际线是从远处观看该城市中所有建筑物形成的轮廓的外部轮廓。给你所有建筑物的位置和高度，请返回由这些建筑物形成的 **天际线** 。

每个建筑物的几何信息由数组 `buildings` 表示，其中三元组 `buildings[i] = [lefti, righti, heighti]` 表示：

- `lefti` 是第 `i` 座建筑物左边缘的 `x` 坐标。
- `righti` 是第 `i` 座建筑物右边缘的 `x` 坐标。
- `heighti` 是第 `i` 座建筑物的高度。

**天际线** 应该表示为由 “关键点” 组成的列表，格式 `[[x1,y1],[x2,y2],...]` ，并按 **x 坐标** 进行 **排序** 。**关键点是水平线段的左端点**。列表中最后一个点是最右侧建筑物的终点，`y` 坐标始终为 `0` ，仅用于标记天际线的终点。此外，任何两个相邻建筑物之间的地面都应被视为天际线轮廓的一部分。

**注意：**输出天际线中不得有连续的相同高度的水平线。例如 `[...[2 3], [4 5], [7 5], [11 5], [12 7]...]` 是不正确的答案；三条高度为 5 的线应该在最终输出中合并为一个：`[...[2 3], [4 5], [12 7], ...]`

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/12/01/merged.jpg)

```
输入：buildings = [[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
输出：[[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
解释：
图 A 显示输入的所有建筑物的位置和高度，
图 B 显示由这些建筑物形成的天际线。图 B 中的红点表示输出列表中的关键点。
```

**示例 2：**

```
输入：buildings = [[0,2,3],[2,5,3]]
输出：[[0,3],[5,0]]
```

 

**提示：**

- `1 <= buildings.length <= 104`
- `0 <= lefti < righti <= 231 - 1`
- `1 <= heighti <= 231 - 1`
- `buildings` 按 `lefti` 非递减排序

## 解法一：暴力（超时）

<img src="https://pic.leetcode-cn.com/1626143638-AbMTRj-image.png" alt="image.png" style="zoom:33%;" />

对于输入的建筑物，其实只有左右边界上端点才有意义。较容易想到两阶段的暴力搜索：① 记录所有建筑物的左右端点作为搜索点。② 依次找到每个端点`i`上的最大高度$Hi$，如果$H_i!=H_j$（不记录水平线）则记录端点上的结果。代码如下：

```c++
class Solution {
public:
    vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
        vector<vector<int>> ans;
        vector<int> sites;
        // 记录建筑物的左右坐标位置
        for(auto& building:buildings){
            sites.push_back(building[0]);
            sites.push_back(building[1]);
        }
        sort(sites.begin(), sites.end());
        // 遍历坐标找天际线
        int last_height = -1;
        for(int& site:sites){
            // 遍历每个建筑物
            int max_height = 0;
            for(auto& building:buildings){
                // 需要注意建筑物右端点不贡献高度
                if(building[0]<=site && building[1]>site){
                    if(building[2]>max_height){
                        max_height = building[2];
                    }
                }
            }
            // 不记录水平线
            if(max_height != last_height){
                ans.push_back({site, max_height});
                last_height = max_height;
            }
        }

        return ans;
    }
};

```

时间复杂度：O($n^2$)，n为建筑个数

空间复杂度：O(n)，额外记录n个端点

## 解法二：利用优先队列

利用优先队列维护最大高度，因此可以加速找坐标点对应最大高度。

和解法一相同，首先记录坐标点。`buildings`数组本身按左端点升序排序，因此在遍历坐标点时可以维护一个最大堆。每次存入有效建筑物【包含该坐标的建筑物】到优先队列中，记录右端点和高度。

在找最大高度前，需要删除无效建筑物【不包含当前坐标的建筑物，注意右端点无贡献】。这里采用**延时删除**的方法，只有影响了判断才进行删除【即无效建筑物占用了最大高度】。最终代码如下：

```c++
class Solution {
public:
    vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
        vector<vector<int>> ans;
        vector<int> sites;
        // 记录建筑物的左右坐标位置
        for(auto& building:buildings){
            sites.push_back(building[0]);
            sites.push_back(building[1]);
        }
        sort(sites.begin(), sites.end());
        // 优先队列 - 大顶堆
        priority_queue<pair<int,int>, vector<pair<int,int>>, less<pair<int,int>>> q;
        
        int idx = 0, n = buildings.size(), last_height = -1, max_height = 0;
        for(int& site:sites){
            while(idx<n && buildings[idx][0]<=site){
                // 插入 [高度, 右端点] → 因为pair的比较是按顺序的
                q.push(make_pair(buildings[idx][2], buildings[idx][1]));
                idx++;
            }
            while(!q.empty() && q.top().second<=site){
                q.pop();
            }
            max_height = q.empty() ? 0 : q.top().first;
            if(max_height != last_height){
                ans.push_back({site, max_height});
                last_height = max_height;
            }
        }

        return ans;
    }
};
```

时间复杂度：O($nlog(n)$)

空间复杂度：O(n)