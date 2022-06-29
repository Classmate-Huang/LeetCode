# LeetCode662：二叉树最大宽度

## 题目

给定一个二叉树，编写一个函数来获取这个树的最大宽度。树的宽度是所有层中的最大宽度。这个二叉树与满二叉树（full binary tree）结构相同，但一些节点为空。

每一层的宽度被定义为两个端点（该层最左和最右的非空节点，两端点间的null节点也计入长度）之间的长度。

示例 1:

输入: 

           1
         /   \
        3     2
       / \     \  
      5   3     9 

输出: 4
解释: 最大值出现在树的第 3 层，宽度为 4 (5,3,null,9)。
示例 2:

输入: 

          1
         /  
        3    
       / \       
      5   3     

输出: 2
解释: 最大值出现在树的第 3 层，宽度为 2 (5,3)。
示例 3:

输入: 

          1
         / \
        3   2 
       /        
      5      

输出: 2
解释: 最大值出现在树的第 2 层，宽度为 2 (3,2)。
示例 4:

输入: 

          1
         / \
        3   2
       /     \  
      5       9 
     /         \
    6           7
输出: 8
解释: 最大值出现在树的第 4 层，宽度为 8 (6,null,null,null,null,null,null,7)。
注意: 答案在32位有符号整数的表示范围内。

## 解法一：层次遍历

如果我们知道每个节点的索引号（按照满二叉树的位置进行编号：对于一个索引为x的节点，其左孩子为`2*x+1`，右孩子为`2*x+2`）以及其所在的深度就可以很方便地处理这个问题。

首先构造一个额外的结构体来存储各节点信息，然后进行层次遍历时，记录每一层的左边界。当遇到一个新节点时，就可以根据这些信息，计算一次宽度，从而更新答案。代码如下：

```c++
struct newNode{
    TreeNode* p;    // 树节点
    int depth;  // 深度
    unsigned long long idx;    // 索引号
    newNode(TreeNode* p, int d, unsigned long long i): p(p), depth(d), idx(i) {}
};

class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if (root == nullptr)    return 0;
        unordered_map<int, unsigned long long> depthBound; // 记录每层深度的左边界
        queue<newNode> que;
        newNode x = newNode(root, 0, 0);
        que.push(x);

        int ans = 1;
        // 层次遍历
        while (!que.empty()) {
            TreeNode* node = que.front().p;
            int depth = que.front().depth; 
            unsigned long long idx = que.front().idx;
            que.pop();
            // 未曾遍历的层，作为左边界
            if (depthBound.find(depth) == depthBound.end()) depthBound[depth] = idx;
            // 已经遍历过，可以计算一次宽度 (idx-左边界+1)
            else    ans = max(ans, int(idx-depthBound[depth]+1));
            // 左右孩子入队列
            if (node->left) que.push(newNode(node->left, depth+1, 2*idx+1));
            if (node->right)    que.push(newNode(node->right, depth+1, 2*idx+2));
        }

        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)