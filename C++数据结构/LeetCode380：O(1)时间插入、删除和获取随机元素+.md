# LeetCode380：O(1)时间插入、删除和获取随机元素

## 题目

实现RandomizedSet 类：

RandomizedSet() 初始化 RandomizedSet 对象
bool insert(int val) 当元素 val 不存在时，向集合中插入该项，并返回 true ；否则，返回 false 。
bool remove(int val) 当元素 val 存在时，从集合中移除该项，并返回 true ；否则，返回 false 。
int getRandom() 随机返回现有集合中的一项（测试用例保证调用此方法时集合中至少存在一个元素）。每个元素应该有 相同的概率 被返回。
你必须实现类的所有函数，并满足每个函数的 平均 时间复杂度为 O(1) 。

 

示例：

输入

```
["RandomizedSet", "insert", "remove", "insert", "getRandom", "remove", "insert", "getRandom"]
[[], [1], [2], [2], [], [1], [2], []]
```

输出

```
[null, true, false, true, 2, true, false, 2]
```

解释

```
RandomizedSet randomizedSet = new RandomizedSet();
randomizedSet.insert(1); // 向集合中插入 1 。返回 true 表示 1 被成功地插入。
randomizedSet.remove(2); // 返回 false ，表示集合中不存在 2 。
randomizedSet.insert(2); // 向集合中插入 2 。返回 true 。集合现在包含 [1,2] 。
randomizedSet.getRandom(); // getRandom 应随机返回 1 或 2 。
randomizedSet.remove(1); // 从集合中移除 1 ，返回 true 。集合现在包含 [2] 。
randomizedSet.insert(2); // 2 已在集合中，所以返回 false 。
randomizedSet.getRandom(); // 由于 2 是集合中唯一的数字，getRandom 总是返回 2 。
```


提示：

```
-231 <= val <= 231 - 1
最多调用 insert、remove 和 getRandom 函数 2 * 105 次
在调用 getRandom 方法时，数据结构中 至少存在一个 元素。
通过次数81,244提交次数153,621
```

## 解法一：双哈希表

维护两个哈希表，一个存储`[val, idx]`，另一个存储`[idx, val]`，另外用一个变量存储`nsize`。具体操作见代码注释：

```c++
class RandomizedSet {
private:
    int nsize;  // 存储长度
    unordered_map<int, int> mapA;   // val-idx
    unordered_map<int, int> mapB;   // idx-val
public:
    RandomizedSet(): nsize(0) {}
    
    bool insert(int val) {
        if (mapA.find(val) != mapA.end())   return false;	// 已存在
        mapA[val] = nsize;
        mapB[nsize++] = val;
        return true;
    }
    
    bool remove(int val) {
        if (mapA.find(val) == mapA.end())   return false;	// 不存在
        int idx = mapA[val];    // 删除val所在idx
        int lastval = mapB[nsize-1];    // 最后一个val
     	// 在map中更改lastval的idx；将mapB中记录代替到val的所在位置
        mapA[lastval] = idx;	
        mapB[idx] = lastval;
        // 在mapA中删除val；删除mapB中的最后一条记录
        mapA.erase(val);
        mapB.erase(nsize--);
        return true;
    }
    
    int getRandom() {
        int idx = random()%nsize;   // 随机获取一个[0, nsize)的索引
        return mapB[idx];
    }
};
```

