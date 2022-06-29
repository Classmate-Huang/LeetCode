# LeetCode105：从前序与中序遍历序列构造二叉树

## 题目

给定一棵树的前序遍历 `preorder` 与中序遍历 `inorder`。请构造二叉树并返回其根节点。

 

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

**示例 2:**

```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

 

**提示:**

- `1 <= preorder.length <= 3000`
- `inorder.length == preorder.length`
- `-3000 <= preorder[i], inorder[i] <= 3000`
- `preorder` 和 `inorder` 均无重复元素
- `inorder` 均出现在 `preorder`
- `preorder` 保证为二叉树的前序遍历序列
- `inorder` 保证为二叉树的中序遍历序列



## 解法一：递归

前序遍历的特点【根，左子树，右子树】，中序遍历的特点【左子树，根，右子树】，利用这个特性可以递归地解决这个问题。找到根节点后，分别确定前序遍历和后序遍历数组中左、右子树的范围，这部分具体看代码注释。此外，为了确定根节点在中序遍历中的位置，可以利用hash表来记录位置对应关系。代码如下：

```c++
class Solution {
public:
    unordered_map<int, int> m;
    // 递归构造函数
    TreeNode* build(vector<int>& preorder, vector<int>& inorder, int preLeft, int preRight, int inLeft, int inRight){
        if(preLeft>preRight || inLeft>inRight)    return nullptr;
		// 当前子树的根节点
        int val = preorder[preLeft];
        TreeNode* root = new TreeNode(val);
        // 中序遍历数组中的位置
        int inP = m[val];
        // 递归到左右子树中
        root->left = build(preorder, inorder, preLeft+1, inP-inLeft+preLeft, inLeft, inP-1);
        root->right = build(preorder, inorder, inP-inLeft+preLeft+1, preRight, inP+1, inRight);
        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        // 利用hash存储中序遍历中的<值, 下标索引>
        for(int i=0; i<n; i++){
            m[inorder[i]] = i;
        }
        TreeNode* root = build(preorder, inorder, 0, n-1, 0, n-1);
        return root;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)