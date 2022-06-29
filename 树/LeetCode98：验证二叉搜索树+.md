# LeetCode98：验证二叉搜索树

## 题目

给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

* 节点的左子树只包含 小于 当前节点的数。
* 节点的右子树只包含 大于 当前节点的数。
* 所有左子树和右子树自身必须也是二叉搜索树。


示例 1：

```
输入：root = [2,1,3]
输出：true
```


示例 2：

```
输入：root = [5,1,4,null,null,3,6]
输出：false
解释：根节点的值是 5 ，但是右子节点的值是 4 。
```


提示：

```
树中节点数目范围在[1, 104] 内
-231 <= Node.val <= 231 - 1
```

## 解法一：中序遍历

利用二叉搜索树的特点，如果中序遍历是升序，那么一定是二叉搜索树。因此，我们在设置一个变量，用于记录当前中序遍历过程中的最后一个值`preVal`，新遍历到的节点一定要大于这个值才行。代码如下：

```c++
class Solution {
public:
    bool checkBST(TreeNode* root, long long& preVal) {
        if (root == nullptr)    return true;
        // 判断左子树是否满足条件
        if (!checkBST(root->left, preVal)) return false;
        // 判断当前节点是否满足条件 - 处理当前节点
        if (preVal >= root->val)    return false;
        // 更新最后一个节点并判断右孩子
        preVal = root->val;
        return checkBST(root->right, preVal);

    }

    bool isValidBST(TreeNode* root) {
        long long preVal = LONG_MIN;
        return checkBST(root, preVal);
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)