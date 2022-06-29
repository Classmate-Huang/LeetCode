# LeetCode958：二叉树的完全性检验

## 题目

给定一个二叉树的 root ，确定它是否是一个 完全二叉树 。

在一个 完全二叉树 中，除了最后一个关卡外，所有关卡都是完全被填满的，并且最后一个关卡中的所有节点都是尽可能靠左的。它可以包含 1 到 2h 节点之间的最后一级 h 。

 

示例 1：

```
输入：root = [1,2,3,4,5,6]
输出：true
解释：最后一层前的每一层都是满的（即，结点值为 {1} 和 {2,3} 的两层），且最后一层中的所有结点（{4,5,6}）都尽可能地向左。
```

示例 2：

```
输入：root = [1,2,3,4,5,null,7]
输出：false
解释：值为 7 的结点没有尽可能靠向左侧。
```


提示：

```
树的结点数在范围  [1, 100] 内。
1 <= Node.val <= 1000

```

## 解法一：层次遍历

要判断一颗二叉树是否为完全二叉树，采用层次遍历，并在遍历过程中遵循两个准则：① 不能出现"有右孩子，无左孩子"的节点；② 当遍历到处"缺失孩子"的节点时，后续的所有节点都必须是叶子节点。

满足这两条规则的树就是完全二叉树，否则就不是，代码如下：

```c++
class Solution {
public:
    bool isCompleteTree(TreeNode* root) {
        if (root == nullptr)    return true;
        
        bool leaf = false;  // 是否该遍历叶节点了?
        queue<TreeNode*> que;
        que.push(root);
        while (!que.empty()) {
            TreeNode * node = que.front();
            que.pop();
            // 1. 不能存在"有右无左"的情况
            if (node->right && node->left == nullptr)   return false;
            // 2. 如果开启了leaf标志位，那么后续的所有点不能有孩子
            if (leaf && (node->left || node->right)) return false;
            // 层次遍历
            if (node->left) que.push(node->left);
            if (node->right) que.push(node->right);
            // 如果出现无孩子的节点，那么开启leaf标志
            if (node->left == nullptr || node->right == nullptr)    leaf = true;
        }

        return true;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)