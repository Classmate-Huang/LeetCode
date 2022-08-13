# 剑指Offer36：二叉搜索树与双向链表

## 题目

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

为了让您更好地理解问题，以下面的二叉搜索树为例：
![](https://assets.leetcode.com/uploads/2018/10/12/bstdlloriginalbst.png)

我们希望将这个二叉搜索树转化为双向循环链表。链表中的每个节点都有一个前驱和后继指针。对于双向循环链表，第一个节点的前驱是最后一个节点，最后一个节点的后继是第一个节点。

下图展示了上面的二叉搜索树转化成的链表。“head” 表示指向链表中有最小元素的节点。
![](https://assets.leetcode.com/uploads/2018/10/12/bstdllreturndll.png) 

特别地，我们希望可以就地完成转换操作。当转化完成以后，树中节点的左指针需要指向前驱，树中节点的右指针需要指向后继。还需要返回链表中的第一个节点的指针。

## 解法一：中序遍历

首先明确是二叉搜索树，所以中序遍历就一定能得到正确的有序数列。因此，我们可以在中序遍历的过程中构造双向链表。

具体地说，用一个全局变量`pre`来记录有序数列中的前一个节点，处理时将当前节点`cur`的左指针指向前一个节点，然后`pre`的右指针指向当前节点即可。需要注意头结点的特殊处理(`pre == nullptr`时表示遍历到的是第一个节点)。代码如下：

```python
class Solution {
public:
    Node *head, *pre = nullptr;
    
    void dfs(Node* cur) {
        if (cur->left)  dfs(cur->left);
        
        if (pre == nullptr)  head = cur; // 记录头结点
        else    pre->right = cur;
        cur->left = pre;
        pre = cur;
        
        if (cur->right) dfs(cur->right);
    }
    
    Node* treeToDoublyList(Node* root) {
        if (root == nullptr)    return nullptr;
        dfs(root);
        head->left = pre;
        pre->right = head;
        return head;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)