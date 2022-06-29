# cLeetCode周赛286

## 题目一：[找出两数组的不同](https://leetcode.cn/problems/find-the-difference-of-two-arrays/) (模拟)

直接按照题意模拟即可。

```c++
class Solution {
public:
    vector<vector<int>> findDifference(vector<int>& nums1, vector<int>& nums2) {
        vector<vector<int>> ans(2);
        unordered_set<int> set1, set2;
        for(auto& i:nums1)  set1.insert(i);
        for(auto& i:nums2)  set2.insert(i);
        // 输出结果
        for(auto& i:set1){
            if(set2.find(i)==set2.end())    ans[0].push_back(i);
        }
        for(auto& i:set2){
            if(set1.find(i)==set1.end())    ans[1].push_back(i);
        }
        return ans;
    }
};
```

## 题目二：[美化数组的最少删除数](https://leetcode.cn/problems/minimum-deletions-to-make-array-beautiful/) (贪心)

采用双指针，一个指向模板元素(模拟题中的`i%2==0`的下标)，一个指向待匹配的元素（模拟题中的`i+1`）。

```c++
class Solution {
public:
    int minDeletion(vector<int>& nums) {
        int l = 0, r = l+1;
        int ans = 0, n = nums.size();
        
        while (r < n) {
            if (nums[l] == nums[r]) {    // 无法匹配
                r++;
                ans++;
            }
            else {
                l = r+1;
                r = l+1;
            }
        }
        
        if (l < n)  ans++;  // l不能没有被匹配
        return ans;
    }
};
```

## 题目三：[找到指定长度的回文数](https://leetcode.cn/problems/find-palindrome-with-fixed-length/) (模拟)

对于长度为t的第k小回文数，可以先构造一半的数字，比如`t=3, k=90`。对于回文数的一半`x = (t+1)/2`，我们知道base = pow(10, x-1)`，第k小就是`base + (k-1)`，对于剩下的一半直接利用回文性质构造即可。需要注意判断越界、以及奇数偶数长度的条件。

```c++
class Solution {
public:
    // 长度为t，第k小的回文数
    long long getResult(int k, int t) {
        int x = (t + 1) / 2;    // 一半占多少位
        long long base = pow(10, x-1);
        base += k - 1;
        
        if (base >= pow(10, x)) return -1;  // 越界直接返回-1
        
        long long ans = base;
        
        if (t % 2 == 1) base /= 10;
        while (base) {
            ans = ans*10 + base%10;
            base /= 10;
        }
        
        return ans;
    }
    
    vector<long long> kthPalindrome(vector<int>& queries, int intLength) {
        vector<long long> ans;
        for (auto k : queries) {
            ans.push_back(getResult(k, intLength));
        }
        return ans;
    }
};
```

## 题目四：[从栈中取出 K 个硬币的最大面值和](https://leetcode.cn/problems/maximum-value-of-k-coins-from-piles/) (DP)

建立状态`f[i][j]`表示前i个栈中取j个硬币的最大面值和。对于每个栈，可以选择对它取“t”次，状态转移方程为`f[i][j] = max(f[i-1][j-t]+val, f[i][j])`。需要注意的是边界条件以及转移的细节(每个栈最多取的次数不能超过其size大小)。

```c++
class Solution {
public:
    int maxValueOfCoins(vector<vector<int>>& piles, int k) {
        int n = piles.size();
        // f[i][j]在前i个栈中拿了j次硬币得到的最大面值和
        vector<vector<int>> f(n+1, vector(k+1, 0));
        
        for (int i = 1; i <= n; i++) {  // 前i个栈
            for (int j = 1; j <= k; j++) {  // 拿k次
                f[i][j] = f[i-1][j];
                // 在当前栈中拿t+1次，前i-1个栈中拿j-t-1次
                int val = 0;
                for (int t = 0; t < j && t < piles[i-1].size(); t++) {
                    val += piles[i-1][t];
                    f[i][j] = max(f[i-1][j-t-1]+val, f[i][j]);
                }
            }
        }
        
        return f[n][k];
    }
};
```

