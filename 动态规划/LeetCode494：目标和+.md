# LeetCode494：目标和

## 题目

给你一个整数数组 `nums` 和一个整数 `target` 。

向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 **表达式** ：

- 例如，`nums = [2, 1]` ，可以在 `2` 之前添加 `'+'` ，在 `1` 之前添加 `'-'` ，然后串联起来得到表达式 `"+2-1"` 。

返回可以通过上述方法构造的、运算结果等于 `target` 的不同 **表达式** 的数目。

 

**示例 1：**

```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

**示例 2：**

```
输入：nums = [1], target = 1
输出：1
```

 

**提示：**

- `1 <= nums.length <= 20`
- `0 <= nums[i] <= 1000`
- `0 <= sum(nums[i]) <= 1000`
- `-1000 <= target <= 1000`

## 解法一：回溯

其实就是利用`DFS`遍历完所有的情况，每一种情况判断一次是否满足，若满足就+1，不推荐使用。代码如下：

```c++
class Solution {
public:
    void backtracking(vector<int>& nums, int i, int s, int target, int & result){
        if(i==nums.size()){
            if(s==target){
                result++;
            }
            return;
        }

        backtracking(nums, i+1, s+nums[i], target, result);
        backtracking(nums, i+1, s-nums[i], target, result);
    }

    int findTargetSumWays(vector<int>& nums, int target) {
        int result = 0;
        backtracking(nums, 0, 0, target, result);
        return result;
    }
};
```

时间复杂度：O($2^n$)

空间复杂度：O(n)

## 解法二：0-1背包问题

这也是本题最推荐的做法，但难点就在于如何将本题转换到0-1背包问题。将`nums`中的元素分为两部分，一类是前面符号为`+`，称为`pos`，另一类则是为`-`，称为`neg`。`nums`中的元素均为正数，设和为`sum`，即存在`pos+neg=sum`，而`target=pos-neg`，那么可以推得`pos=(pos+neg)/2`。

问题就转换成了，在`nums`中选一些数，然后其和为`pos`。要注意不能直接套0-1背包的模板，因为这里要求得是方法数，因此`f`里存的也是方法数，这里直接采用空间压缩后的思路。`f[w]`为和为w时可选的方法数，状态转移方程为：`f[j]=f[j]+f[j-w])`，表示不选当前物体能达到`j`的方法数+选择当前物体能达到`j-tw`的方法数。

注意初始条件，`f[0]=1`：一方面全不选也为1类方法数，另一方面也用于后续的状态转移。代码如下，注意为逆向遍历：

```c++
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if((sum+target)%2==1){
            return 0;
        }
        int N = nums.size(), W = (sum+target)/2, result = 0;
        vector<int> f(W+1, 0);
        f[0] = 1;

        for(int i=0; i<N; i++){
            int tw = nums[i];
            for(int j=W; j>=tw; j--){
                f[j] = max(f[j], f[j-tw]+f[j]);
            }
        }

        return f[W];
    }
};
```

时间复杂度：O(NW)，N为数组长度，W为(sum+target)/2。

空间复杂度：O(W)

> 这一题也可以从`neg`的角度出发，`W=(sum-target)/2`。可以看出来，当target为正时，从负数角度出发空间复杂度要更小。