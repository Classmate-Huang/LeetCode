# LeetCode236：二叉树的最近公共祖先

## 题目

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/binarytree.png)

示例 1:

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```

示例 2:

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

说明:

```
所有节点的值都是唯一的。
p、q 为不同节点且均存在于给定的二叉树中。
```

## 解法一：哈希表+集合

首先构造一个哈希表，记录每个节点的父节点，这样对于任意一个点就可以往上遍历找到所有的祖先节点。

然后对节点p向上遍历，记录它的所有祖先节点在集合中。对于点q向上遍历，直到遇到与p相同的祖先节点就返回。

代码如下：

```c++
class Solution {
public:
    // 记录每个节点的父节点
    unordered_map<TreeNode*, TreeNode*> fatherMap;

    void recordFather(TreeNode * root) {
        if (root == nullptr)    return;

        if (root->left) fatherMap[root->left] = root;
        if (root->right) fatherMap[root->right] = root;

        recordFather(root->left);
        recordFather(root->right);
        
    }

    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 1. 记录每个节点的父节点
        recordFather(root);
        fatherMap[root] = root; // 根节点的父节点等于自身
        // 2. 记录p的祖先节点序列
        unordered_set<TreeNode*> pSet;
        while (p != fatherMap[p]) {
            pSet.insert(p);
            p = fatherMap[p];
        }
        pSet.insert(root);
        // 3. 遍历q的祖先节点，直到遇到公共祖先
        while (pSet.find(q) == pSet.end()) {
            q = fatherMap[q];
        }
        return q;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：不用额外存储空间

先将问题分解为以下两种情况：

第1种情况：`q`是`p`的最近公共祖先，或者`p`是`q`的最近公共祖先；

第2种情况：`q,p`不互为对方最近公共祖先，两节点都要向上遍历找公共祖先。

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 如果结点为空，或者为 p / q
        if (root == nullptr || root == p || root == q)  return root;
        // 向左右子树问答案
        TreeNode * left = lowestCommonAncestor(root->left, p, q);
        TreeNode * right = lowestCommonAncestor(root->right, p, q);
        // p, q分布在左右子树上，即返回当前节点（最近公共祖先）
        if (left != nullptr && right != nullptr)    return root;
        // 左右子树都为空时返回null，否则返回不为null的节点
        return left != nullptr ? left : right;
    }
};
```

根据上述代码分析，如果`p,q`互为祖先节点（对于第一种情况）会返回对应的`p/q`；如果不互为祖先节点，那么q所在的子树会向上返回q，p所在的子树会向上返回p，它们在最近公共祖先处相遇，激发if中的条件，返回对应结果。