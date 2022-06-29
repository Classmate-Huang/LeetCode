# LeetCode543：二叉树的直径

## 题目

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

 

**示例 :**
给定二叉树

```
          1
         / \
        2   3
       / \     
      4   5    
```

返回 **3**, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。



**注意：**两结点之间的路径长度是以它们之间边的数目表示。

## 解法一：自底向上

首先明确：**以某个结点为根节点（路径经过该根节点向其左右子树伸展）**的最长路径=左子树的最大深度 + 右子树的最大深度。比如示例中，以2为根节点的最长路径为【4,2,5】。

有了上面的转换，那么剩下的过程就与110题【平衡二叉树】一致，自底向上计算每个节点的最长路径，进行比较，最后保留下整体的最优值。

```c++
class Solution {
public:
    int ans = 0;    // 存储最大路径
    int depth(TreeNode* root){  // 获取深度
        if(!root)    return 0;
        int left = depth(root->left);
        int right = depth(root->right);
        // 计算当前节点的最大路径 = 左子树深度 + 右子树深度
        int cur_len = left+right;
        if(cur_len>ans) ans = cur_len;
        return max(left, right)+1;
    }
    int diameterOfBinaryTree(TreeNode* root) {
        depth(root);
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)