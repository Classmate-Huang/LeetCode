# LeetCode222：完全二叉树的节点个数

## 题目

给你一棵 **完全二叉树** 的根节点 `root` ，求出该树的节点个数。

[完全二叉树](https://baike.baidu.com/item/完全二叉树/7773232?fr=aladdin) 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 `h` 层，则该层包含 `1~ 2h` 个节点。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/01/14/complete.jpg)

```
输入：root = [1,2,3,4,5,6]
输出：6
```

**示例 2：**

```
输入：root = []
输出：0
```

**示例 3：**

```
输入：root = [1]
输出：1
```

 

**提示：**

- 树中节点的数目范围是`[0, 5 * 104]`
- `0 <= Node.val <= 5 * 104`
- 题目数据保证输入的树是 **完全二叉树**

 

**进阶：**遍历树来统计节点是一种时间复杂度为 `O(n)` 的简单解决方案。你可以设计一个更快的算法吗？

## 解法一：二分遍历子树

对于这个问题，我们主要是要找到对应的分界点【二叉树的最后一行的最后一个节点】。

对于某棵子树的根节点，我们优先去找到其左右子树所对应的最大深度【找到其**最左节点对应的最大深度**】，如果左子树的深度大于右子树深度，说明分界点在左子树，否则在右子树。并且我们可以知道：如果分界点在左子树，那么当前右子树肯定是一颗满二叉树；反之亦然。这样，我们就可以在遍历的过程中同时统计每颗子树的节点个数，代码如下：

```c++
class Solution {
public:
    // 找子树最左节点对应的深度
    int findHeight(TreeNode* root){
        if(!root)   return 0;
        return findHeight(root->left) + 1;
    }

    void dfs(TreeNode* root, int& ans, int heightL){
        if(root == nullptr)     return;	// 根节点为0
		// 左子树的深度为之前父节点对应子树的深度-1
        if(heightL == -1)   heightL = findHeight(root->left);	
        int heightR = findHeight(root->right);
        // 左子树高于右子树
        if(heightL > heightR){
            ans += 1<<heightR;	// 右子树为高度为heightR的满二叉树[2^{h}-1+1(根节点)]
            dfs(root->left, ans, heightL-1);	// 递归到左子树中
        }
        else{
            ans += 1<<heightL;	// 左子树为满二叉树
            if(heightR != 0)  dfs(root->right, ans, heightR-1);	// 递归到右子树
        }
    }

    int countNodes(TreeNode* root) {
        int ans = 0;
        dfs(root, ans, -1);
        return ans;
    }
};
```

时间复杂度：O($log(n)*log(n)$)

空间复杂度：O($log(n)$)



## 解法二：二分遍历分界点

从另一个思路来分析，从根节点开始编号（`1,2,3....分界点x`），只要我们定位到分界点x的值也就得到了结果。

首先遍历树的最大深度为`h`，那么也就确定了分界点的取值范围，即[$2^{h-1}，2^h-1$]。我们用二分的思想去确定具体值，这里需要用到**位运算**的技巧，对于某个值，我们将其化为二进制数，取最低的`h-1`位，根据每位上的取值来确定树遍历时的走向，见下例：

![fig1](https://assets.leetcode-cn.com/solution-static/222/1.png)

代码如下：

```c++
class Solution {
public:
    int maxDepth(TreeNode* root){	// 求最大深度
        if(!root)   return 0;
        return 1 + maxDepth(root->left);
    }

    bool exitsNode(TreeNode* root, int t, int h){	// 判断t节点是否存在
        int flag = 1 << (h-2);
        while(flag){
            if((t&flag) != 0)  root = root->right;
            else root = root->left;
            flag = flag >> 1;
        }
        return root!=nullptr;
    }

    int countNodes(TreeNode* root) {	// 利用二分法计算节点
        if(!root)   return 0;

        int h = maxDepth(root);
        int left = 1<<(h-1), right = (1<<h) - 1;

        while(left < right){
            int mid = (right-left+1) / 2 + left;
            if(exitsNode(root, mid, h))  left = mid;
            else    right = mid-1;
        }

        return left;
    }
};
```

时间复杂度：O($log(n)*log(n)$)

空间复杂度：O($log(n)$)