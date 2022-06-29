# LeetCode264：丑数II

## 题目

给你一个整数 n ，请你找出并返回第 n 个 丑数 。

丑数 就是只包含质因数 2、3 和/或 5 的正整数。

 

示例 1：

```
输入：n = 10
输出：12
解释：[1, 2, 3, 4, 5, 6, 8, 9, 10, 12] 是由前 10 个丑数组成的序列。
```

示例 2：

```
输入：n = 1
输出：1
解释：1 通常被视为丑数。
```


提示：

```
1 <= n <= 1690
```

## 解法一：优先队列（最小堆）+ 集合

第一个丑数是1，后面的每个丑数都是在之前丑数基础上乘以一个丑数因子(2, 3, 5)。

我们要找的就是第n个最小的丑数，在做"找第k个最小数"类似的题目时，我们自然会想到用优先队列来解题。对于此题，我们维护一个最小堆，堆中存放的都是丑数。每次选择堆顶元素乘以丑数因子找到新的丑数，新丑数入堆，弹出旧丑数，第n个弹出的丑数就是我们要找的答案。

为了避免重复计算丑数(比如10可以是`2x5`，也可以是`5x2`，但它只该被记录一次)，因此还要采用一个集合来存储被遍历过的值。

```c++
struct cmp{     // 小顶堆
    bool operator()(long long & a, long long & b) {
        return a > b;
    }
};

class Solution {
public:
    vector<int> factors = {2, 3, 5};
    
    int nthUglyNumber(int n) {
    	// 使用long long避免溢出
        priority_queue<long long, vector<long long>, cmp> pri_que;
        unordered_set<long long> mySet;
        
        pri_que.push(1LL);
        mySet.insert(1LL);
        int ugly = 0;
        
        // 找到第n个弹出的元素
        for (int i = 0; i < n; i++) {
            long long t = pri_que.top();
            pri_que.pop();
            ugly = int(t);
            for (auto f : factors) {
                long long x = t * f;
                if (mySet.find(x) == mySet.end()) {
                    pri_que.push(x);
                    mySet.insert(x);
                }
            }
        }
        
        return ugly;
    }
};
```

时间复杂度：O(nlogn)

空间复杂度：O(n)

## 解法二：动态规划*

虽然说是动态规划，但跟常规的DP又不太一样，对于这个题我们貌似很难去构造状态转移方程。

设定`f[i]`表示第`i`小的丑数，边界条件`f[1] = 1`，我们知道后续的丑数都是由前面的丑数乘以对应因子(2, 3, 5)得到的。为了保证第`i`次得到的数就是第`i`小的丑数，维护3个指针`p2, p3, p5`，初始值都指向`1`，使用`f[i] = min(f[p2]*2, f[p3]*3, f[p5]*5)`进行状态转移，为了避免重复统计同一个丑数，转移之后要将对应的指针++。

> 有点类似于"K个有序链表合并"的s思路

```c++
class Solution {
public:
    int nthUglyNumber(int n) {
        vector<int> f(n+1);
        f[1] = 1;
        
        int p2 = 1, p3 = 1, p5 = 1;
        for (int i = 2; i <= n; i++) {
            f[i] = min(f[p2]*2, min(f[p3]*3, f[p5]*5));	// 状态转移
            // 将对应指针++，采用多个分支的写法，是为了避免10=5*2和10=2*5
            if (f[i] == f[p2]*2)    p2++;
            if (f[i] == f[p3]*3)    p3++;
            if (f[i] == f[p5]*5)    p5++;
        }
        
        return f[n];
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)