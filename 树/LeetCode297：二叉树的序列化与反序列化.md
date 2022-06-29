# LeetCode297：二叉树的序列化与反序列化

## 题目

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

提示: 输入输出格式与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

 ![img](https://assets.leetcode.com/uploads/2020/09/15/serdeser.jpg)

示例 1：

```
输入：root = [1,2,3,null,null,4,5]
输出：[1,2,3,null,null,4,5]
```


示例 2：

```
输入：root = []
输出：[]
```

示例 3：

```
输入：root = [1]
输出：[1]
```

示例 4：

```
输入：root = [1,2]
输出：[1,2]
```


提示：

```
树中结点数在范围 [0, 104] 内
-1000 <= Node.val <= 1000
```

## 解法一：利用先序遍历序列化与反序列化

首先使用先序遍历记录将所有节点的值记录下来，对于`nullptr`使用特殊符号进行记录，每个元素之间使用`,`分割开来，这样就可以将一颗树转换为一个字符串。

给定一个字符串，首先按照`,`进行分割，得到每个元素。再利用先序遍历将树进行还原即可，代码如下：

```c++
class Codec {
public:
    // 前序遍历序列化
    void preOrder(TreeNode* root, string& s) {
        if (root == nullptr) {
            s += "#,";
            return ;
        }
        s += to_string(root->val) + ",";
        preOrder(root->left, s);
        preOrder(root->right, s);
    }
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        string s = "";
        preOrder(root, s);
        return s;
    }
    // 分割字符串
    vector<string> splitStr(string s, string pattern) {
        if (s.empty())  return {};
        vector<string> ans;
        auto pos = s.find(pattern);
        while (pos != s.npos) {
            string temp = s.substr(0, pos);
            ans.push_back(temp);
            s = s.substr(pos+1, s.size());
            pos = s.find(pattern);
        }
        return ans;
    }

    TreeNode* bulidTree(vector<string>& sArr, int& i) {
        if (i >= sArr.size() || sArr[i] == "#")   return nullptr;
        int val = stoi(sArr[i]);
        TreeNode* root = new TreeNode(val);
        root->left = bulidTree(sArr, ++i);
        root->right = bulidTree(sArr, ++i);
        return root;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        vector<string> sArr = splitStr(data, ",");
        int i = 0;
        TreeNode* root = bulidTree(sArr, i);
        return root;
    }
};
```

