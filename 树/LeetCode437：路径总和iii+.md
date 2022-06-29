# LeetCode437：路径总和iii

## 题目

给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。

**路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg)

```
输入：root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
输出：3
解释：和等于 8 的路径有 3 条，如图所示。
```

**示例 2：**

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
输出：3
```

 

**提示:**

- 二叉树的节点个数的范围是 `[0,1000]`
- `-109 <= Node.val <= 109` 
- `-1000 <= targetSum <= 1000` 

## 解法一：暴力搜索

由于题目给出了节点个数范围在1000以内，因此可以采用暴力搜索：即以每个节点为起点，向下搜索所有可能存在的情况。代码如下：

```c++
class Solution {
public:
    int ans = 0;  //存储结果
    // 利用dfs进行搜索，curSum存储当前路径中的节点和
    void dfs(TreeNode* root, int& curSum, int targetSum){
        if(!root)   return ;
        curSum += root->val;
        if(curSum==targetSum)   ans++;
        dfs(root->left, curSum, targetSum);
        dfs(root->right, curSum, targetSum);
        curSum -= root->val;
    }

    int pathSum(TreeNode* root, int targetSum) {
        // 利用BFS进行节点遍历
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            TreeNode * temp = q.front(); 
            q.pop();
            int curSum = 0;
            if(temp){
                // 搜索以temp为起点的情况
                dfs(temp, curSum, targetSum);
                q.push(temp->left);
                q.push(temp->right);
            }
        }
        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n) 

## 解法二：前缀和

以DFS的视角来看，二叉树可以分成许多条路径【比如示例中的[10,5,3,3]和[10,5,3,-2]就是两条不同的路径】，对于每条路径可以视为一个数组，那么这个问题就可以转换为经典的<前缀和>问题（善于求解区间和之类的问题）。

```c++
class Solution {
public:
    int ans = 0;
    unordered_map<long long, int> m = {{0, 1}}; //初始化M[0]=1
    // 利用DFS遍历所有情况
    void dfs(TreeNode* root, int& perSum, int targetSum){
        if(!root)   return ;
        // 查询以当前节点为结尾的情况
        perSum += root->val;
        if(m.find(perSum-targetSum)!=m.end())
            ans += m[perSum-targetSum];
        // 往下遍历
        m[perSum]++;  // 记录前缀和
        dfs(root->left, perSum, targetSum);
        dfs(root->right, perSum, targetSum);
        m[perSum]--;  // 删除该节点的记录
        
        perSum -= root->val; // 回溯
    }

    int pathSum(TreeNode* root, int targetSum) {
        int perSum = 0;
        dfs(root, perSum, targetSum);
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)