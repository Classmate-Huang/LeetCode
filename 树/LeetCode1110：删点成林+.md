# LeetCode1110：删点成林

## 题目

给出二叉树的根节点 `root`，树上每个节点都有一个不同的值。

如果节点值在 `to_delete` 中出现，我们就把该节点从树上删去，最后得到一个森林（一些不相交的树构成的集合）。

返回森林中的每棵树。你可以按任意顺序组织答案。

 

**示例 1：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/07/05/screen-shot-2019-07-01-at-53836-pm.png)**

```
输入：root = [1,2,3,4,5,6,7], to_delete = [3,5]
输出：[[1,2,null,4],[6],[7]]
```

**示例 2：**

```
输入：root = [1,2,4,null,3], to_delete = [3]
输出：[[1,2,4]]
```

 

**提示：**

- 树中的节点数最大为 `1000`。
- 每个节点都有一个介于 `1` 到 `1000` 之间的值，且各不相同。
- `to_delete.length <= 1000`
- `to_delete` 包含一些从 `1` 到 `1000`、各不相同的值。

## 解法一：递归

对于某个节点，如果它在删除列表中，那么需要把它的有效孩子节点(非空)放入结果中。但它的孩子节点也可能在删除列表中，所以需要进行**DFS**：先对其孩子节点进行处理，然后再处理当前节点（后序遍历）。

代码如下：

```c++
class Solution {
public:
    vector<TreeNode*> ans;

    TreeNode* dfs(TreeNode* root, unordered_set<int>& record){
        if(!root)   return nullptr;
        // 往下递归
        root->left = dfs(root->left, record);
        root->right = dfs(root->right, record);
        // 判断当前节点是否在删除列表中
        if(record.find(root->val)!=record.end()){
            // 把孩子节点放入ans中【前提是孩子节点有效：非空 / 不被删除】
            if(root->left)  ans.push_back(root->left);
            if(root->right) ans.push_back(root->right);
            return nullptr;
        }
        return root;
    }

    vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete) {
        // 放入set中，方便查询
        unordered_set<int> record(to_delete.begin(), to_delete.end());
        
        root = dfs(root, record);
        if(root)    ans.push_back(root);
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：层次遍历

第一种解法通常没有太多普适性，而且最后存储的结果都是乱序的。假设我们要求需要按照层次遍历的结果来输出最终答案，那么我们就需要用到层次遍历，并同时处理结果。

在层次遍历的同时，首先需要在树上正常地进行层次遍历操作（将左右孩子入队列），然后要根据删除清楚进行判断：

* 首先将根节点压入队列中；【正常层次遍历】
* 判断根节点是否要被删除，如果不需要，则压入结果数组中；
* 层次遍历，将左右孩子压入队列；【正常层次遍历】
* 如果当前节点要被删除，且左(右)孩子不被删除，那么将左(右)孩子压入结果数组；
* 无论当前节点是否要被删除，如果左（右）孩子要被删除，则将左（右）孩子置为`nullptr`。

代码如下：

```c++
class Solution {
public:
    vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete) {
        unordered_set<int> deleteSet;
        for (int i : to_delete) deleteSet.insert(i);
        vector<TreeNode*> ans;
        // 层次遍历
        queue<TreeNode*> que;
        que.push(root);
        if (deleteSet.find(root->val) == deleteSet.end())    ans.push_back(root);
        while (!que.empty()) {
            // 正常的层次遍历
            TreeNode* node = que.front();
            que.pop();
            if (node->left) que.push(node->left);
            if (node->right) que.push(node->right);
            // 当前节点被删除需要被删除时
            if (deleteSet.find(node->val) != deleteSet.end()) {
                if (node->left && deleteSet.find(node->left->val) == deleteSet.end()) { // // 左孩子不删除
                    ans.push_back(node->left);
                }
                if (node->right && deleteSet.find(node->right->val) == deleteSet.end()) { // // 右孩子不删除
                    ans.push_back(node->right);
                }
            }
            // 左右孩子需要被删除时
            if (node->left && deleteSet.find(node->left->val) != deleteSet.end()) {
                node->left = nullptr;
            }
            if (node->right && deleteSet.find(node->right->val) != deleteSet.end()) {
                node->right = nullptr;
            }
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法三：前序遍历

同样，我们可以在前序遍历的同时进行节点删除，代码如下：

```c++
class Solution {
public:
    vector<TreeNode*> ans;
    unordered_set<int> deleteSet;

    void preOrder(TreeNode* root) {
        if (root == nullptr)    return;
        preOrder(root->left);
        preOrder(root->right);
        // 当前节点需要被删除
        if (deleteSet.find(root->val) != deleteSet.end()) {
            if (root->left && deleteSet.find(root->left->val) == deleteSet.end()) {
                ans.push_back(root->left);
            }
            if (root->right && deleteSet.find(root->right->val) == deleteSet.end()) {
                ans.push_back(root->right);
            }
        }
        // 左孩子要被删除
        if (root->left && deleteSet.find(root->left->val) != deleteSet.end()) {
            root->left = nullptr;
        }
        // 右孩子要被删除
        if (root->right && deleteSet.find(root->right->val) != deleteSet.end()) {
            root->right = nullptr;
        }
    }

    vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete) {
        for (int i : to_delete) deleteSet.insert(i);
        
        if (deleteSet.find(root->val) == deleteSet.end()) {
            ans.push_back(root);
        }
        preOrder(root);
        return ans;
    }
};
```

