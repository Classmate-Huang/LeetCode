# LeetCode637：二叉树的层平均值

## 题目

给定一个非空二叉树的根节点 `root` , 以数组的形式返回每一层节点的平均值。与实际答案相差 `10-5` 以内的答案可以被接受。

 

**示例 1：**

![img](LeetCode637：二叉树的层平均值.assets/avg1-tree.jpg)

```
输入：root = [3,9,20,null,null,15,7]
输出：[3.00000,14.50000,11.00000]
解释：第 0 层的平均值为 3,第 1 层的平均值为 14.5,第 2 层的平均值为 11 。
因此返回 [3, 14.5, 11] 。
```

**示例 2:**

![img](LeetCode637：二叉树的层平均值.assets/avg2-tree.jpg)

```
输入：root = [3,9,20,15,7]
输出：[3.00000,14.50000,11.00000]
```

 

**提示：**



- 树中节点数量在 `[1, 104]` 范围内
- `-231 <= Node.val <= 231 - 1`

## 解法一：BFS

简单的层次遍历，采用BFS+额外的变量存储每层节点个数即可。代码如下：

```c++
class Solution {
public:
    vector<double> averageOfLevels(TreeNode* root) {
        vector<double> ans;
        queue<TreeNode*> q;
        if(root) q.push(root);

        while(!q.empty()){
            int k = q.size();
            double cur_res = 0., cont = 0.;
            while(k--){
                TreeNode* temp = q.front();   
                q.pop();
                cur_res += temp->val;
                cont += 1;
                if(temp->left)    q.push(temp->left);
                if(temp->right)  q.push(temp->right);
            }
            ans.push_back(cur_res/cont);
        }

        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)