# LeetCode周赛283

## 第一题 [2194. Excel 表中某个范围内的单元格](https://leetcode-cn.com/problems/cells-in-a-range-on-an-excel-sheet/)（模拟）

打卡题，需要注意string的初始化

```c++
class Solution {
public:
    vector<string> cellsInRange(string s) {
        char start_col = s[0], end_col = s[3], start_row = s[1], end_row = s[4];
        
        vector<string> ans;
        for(char i=start_col; i<=end_col; i++){
            // string t = string(1,i);  // 使用string构造函数 (size_type, char c)
            for(char j=start_row; j<=end_row; j++){
                // string res =  t + string(1,j);
                ans.push_back(string({i,j}));   // string构造函数 初试化{}
            }
        }
        
        return ans;
    }
};
```

## 第二题 [2195. 向数组中追加 K 个整数](https://leetcode-cn.com/problems/append-k-integers-with-minimal-sum/)（排序）

这题需要注意不能使用"打表法"，因为数据取值范围非常大($10^9$)，这样显然会超时。因此我们需要把看作一个从区间取值的问题，首先加入一个最小值0，和最大值MAX[1e9+1e5+1]进行排序，我们现在需要做的就是遍历数组，从区间(nums[i], nums[j])中尽可能取一些数，共取k个数。

```c++
class Solution {
public:
    long long minimalKSum(vector<int>& nums, int k) {
        
        // 排序后插入极大值,方便后续判断
        nums.push_back(0);
        nums.push_back(1e9+1e5+5);
        sort(nums.begin(), nums.end());
        
        long long ans = 0;
        for(int i=0; k && i<nums.size(); i++){
            // 无效区间: 相等 or 相邻
            if(nums[i]+1 >= nums[i+1])  continue;
            // 取值进行求和
            long long left = nums[i]+1, right = min(nums[i+1]-1, nums[i]+k);   // [left, right]
            ans += (right+left) * (right-left+1) / 2;
            k -= right-left+1;
        }
        
        return ans;
    }
};
```

时间复杂度：O($nlogn$)

空间复杂度：O(1)

## 第三题 [2196. 根据描述创建二叉树](https://leetcode-cn.com/problems/create-binary-tree-from-descriptions/)（哈希）

比较简单，利用哈希存储每个值对应的节点地址，然后只要模拟构造树的过程即可。

```c++
class Solution {
public:
    TreeNode* createBinaryTree(vector<vector<int>>& descriptions) {
        unordered_map<int, TreeNode*> node_map;
        vector<int> father(100001);
        // 记录每个节点的父节点
        for(int i=1; i<=100000; i++){
            father[i] = i;
        }
        
        // 随便记录一个节点值
        int search_node = -1;
        for(auto& descript:descriptions){
            int parent = descript[0], child = descript[1], isLeft = descript[2];
            // 判断节点是否已被构造
            if(node_map.find(child)==node_map.end())    node_map[child] = new TreeNode(child);
            if(node_map.find(parent)==node_map.end())    node_map[parent] = new TreeNode(parent);
            // 插入
            if(isLeft)  node_map[parent]->left = node_map[child];
            else    node_map[parent]->right = node_map[child];
            father[child] = parent;
            if(search_node == -1)   search_node = parent;
        }
        
        // 递归找到根节点
        while(father[search_node]!=search_node){
            search_node = father[search_node];
        }
        
        return node_map[search_node];
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 第四题 [2197. 替换数组中的非互质数](https://leetcode-cn.com/problems/replace-non-coprime-numbers-in-array/)（栈模拟）

首先`nums[i]`的取值范围在$10^5$以内，而`gcd`算法的时间复杂度为O($\log{m}$)，因此这个题目可以通过暴力模拟解决。为了编码方便使用栈进行存储，从左到右遍历数组：

如果**栈为空**或者**与栈顶元素互质**，则入栈；否则，与栈顶元素求最小公倍数后，更新该元素，再进行一次判断。由于最后还需要返回所有元素，因此直接使用vector模拟栈操作。代码如下：

```c++
class Solution {
public:
    vector<int> replaceNonCoprimes(vector<int>& nums) {
        
        vector<int> ans;    // 利用vector模拟栈

        for(int t:nums){
            if(ans.empty()) ans.push_back(t);
            else{
                int k = __gcd(ans.back(), t);
                while(k > 1){
                    t = ans.back() / k * t;  // 细节，先除再乘
                    ans.pop_back();
                    if(ans.empty()) break;
                    k = __gcd(ans.back(), t);
                }
                ans.push_back(t);
            }
        }
        
        return ans;
    }
};
```

时间复杂度：O($n\log{m}$)

空间复杂度：O($n$)