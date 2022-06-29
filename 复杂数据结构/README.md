

# 复杂数据结构

#### 1. 查并集

> 参考优质资源：https://leetcode-cn.com/problems/most-stones-removed-with-same-row-or-column/solution/tu-jie-bing-cha-ji-by-yexiso-nbcz/

> 详细的来龙去脉见上述资源链接，写得很不错，这里只简单提及最终的形式。

查并集其实就是一种模拟集合的操作，常用于不相交集合的合并以及查询问题。其以**树形结构**来表示不同种类数据的集合，一般当我们**需要用到数据的连通性时**会用到它（比如Kruskal最小生成树）。

查并集的核心思想是为每个数据节点维护一个**父节点**，如果两个数据节点的**父节点**一致，那么说明在同一个连通分支中，如果不一致说明分别存在两个不同的连通分支中。对于合并操作，只需要将其中某个分支的**父节点**指向另一个分支的父节点即可。

**最终模板**

```c++
class DisJointSetUnion{
public:
    vector<int> f;
    DisJointSetUnion(int n):f(vector<int> (n)){
        for(int i=0; i<n; i++){
            f[i] = i;
        }
    }
    
    int find(int x){
        // 路径压缩
        f[x] = x == f[x] ? x : find(f[x]);
        return f[x];
    }
    
    void merge(int x, int y){
		int rootX = f[x], rootY = f[y];
		f[rootX] = rootY;
    }
}
```

#### 2. 前缀树（字典树）

前缀树通常是为了方便字符串的查询，比如字符串`abc`是否出现过，出现过几次？以`ab`的前缀的字符串是否出现过？出现过几次？

```c++
// 前缀树节点
struct TrieNode {
    int pass;   // 经过该点的路径数量
    int leaf;    // 以该点结束的路径数量
    vector<TrieNode*> next;     // 如果字符种类特别多，可以使用hashmap来表达

    TrieNode():pass(0), leaf(0) {
        next = vector<TrieNode*> (26, nullptr);
    }
};
```

为了方便使用，将前缀树以及各项功能全部封装在一个类中，包括构造、插入、删除、查询等功能。具体见以下代码：

```c++
class Trie {
private:
    TrieNode* root;
public:
    Trie() {
        root = new TrieNode();
    }
    // 插入字符串word
    void insertStr(string word) {
        TrieNode* node = root;
        node->pass++;
        for (char c : word) {   // 遍历字符
            if (node->next[c-'a'] == nullptr) {
                node->next[c-'a'] = new TrieNode();
            }
            node = node->next[c-'a'];
            node->pass++;   // 记录路径数量
        }
        node->leaf++;   // 末尾结点++
    }
    // 删除字符串word
    void deleteStr(string word) {
        if (searchStr(word) != 0) { // 一定要先做检查
            TrieNode* node = root;
            node->pass--;
            for (auto c : word) {
                node = node->next[c-'a'];
                node->pass--;
            }
            node->leaf--;
        }
    }
    // 查询字符串word出现过多少次
    int searchStr(string word) {
        TrieNode* node = root;
        for (auto c : word) {
            if (node->next[c-'a'] == nullptr)   return 0;
            node = node->next[c-'a'];
        }
        return node->leaf;
    }
    // 查询以word作为前缀的字符串出现过多少次
    int prefixNumber(string word) {
        TrieNode* node = root;
        for (auto c : word) {
            if (node->next[c-'a'] == nullptr)   return 0;
            node = node->next[c-'a'];
        }
        return node->pass;
    }
};
```

