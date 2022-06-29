# LeetCode110：平衡二叉树

## 题目

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1 。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/06/balance_1.jpg)

```
输入：root = [3,9,20,null,null,15,7]
输出：true
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/10/06/balance_2.jpg)

```
输入：root = [1,2,2,3,3,null,null,4,4]
输出：false
```

**示例 3：**

```
输入：root = []
输出：true
```

 

**提示：**

- 树中的节点数在范围 `[0, 5000]` 内
- `-104 <= Node.val <= 104`

## 解法一：递归

判断一颗二叉树是否为平衡二叉树，根据递归套路：① 思考需要哪些条件：左右子树是否都是平衡二叉树？左子树深度与右子树深度差是否>1? ② 思考需要获取的信息：左右子树是否平衡的判断 + 左右子树深度。这样就很容易写出递归代码。为了方便，这里构造了一个新的数据结果，包含两个信息(是否平衡 + 树的深度)，代码如下：

```c++
struct ReturnType {
    bool flag;  // 是否为平衡二叉树
    int depth;  // 最大深度
    ReturnType(bool flag, int depth): flag(flag), depth(depth) {}
};

class Solution {
public:
    ReturnType fun(TreeNode* root) {
        if (root == nullptr) return ReturnType(true, 0);
        // 先获取左，右子树信息
        ReturnType left = fun(root->left);
        ReturnType right = fun(root->right);
        // 根据信息，得到当前子树深度 + 是否满足完全二叉树
        int depth = max(left.depth, right.depth) + 1;
        bool flag = left.flag & right.flag & (abs(left.depth-right.depth) <= 1);
        // 返回
        return ReturnType(flag, depth);
    }

    bool isBalanced(TreeNode* root) {
        return fun(root).flag;
    }
};
```

时间复杂度：O(n)，每个节点处理一次

空间复杂度：O(n)
