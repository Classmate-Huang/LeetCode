# LeetCode55：跳跃游戏

## 题目

给定一个非负整数数组 nums ，你最初位于数组的 第一个下标 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

 

示例 1：

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

示例 2：

```
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
```


提示：

```
1 <= nums.length <= 3 * 104
0 <= nums[i] <= 105
```

## 解法一：暴力

利用数组`f[i]`记录位置i是否可以到达重点，然后从后到前进行遍历即可，只要当前节点向后能到达的点中`f[]`为true，那这个点也可达到终点。代码如下：

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        // 暴力尝试
        vector<bool> f(n, false); // 表示第i个位置上是否能到达终点
        f[n-1] = true;
        int idx = n-2;
        while(idx>=0){
            for(int i=1; i<=nums[idx]; i++){
                if(f[idx+i]){
                    f[idx] = true;
                    break;
                }
            }
            idx--;
        }
        return f[0];
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n)

> 这个复杂度本来是过不了的，但是样例比较弱，还是过了

## 解法二：贪心

基于解法一，我们发现其实不用一个一个遍历，只要当前节点往后跳跃的最大距离大于可到达区间的左值即可。我们用一个变量维护最小的可到达索引first，只要`idx+nums[idx]>=first`就说明从idx出发，一定能达到first，而first一定能到达终点，最后判断first是否为起始点即可。代码如下：

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        // 贪心, first从该点出发能达到终点
        int n = nums.size(), first, idx;
        first = n - 1; idx = n - 2;    // 
        while(idx >= 0){
            if(idx + nums[idx] >= first){    // 从idx出发能跳跃到可到达区间内
                first = idx;
            }
            idx--;
        }
        return first == 0;
    }
};
```

还可以从头开始考虑，比如用变量记录从起始点出发，能到达的最远距离last，只要`last>=n-1`即可。代码如下：

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        // 贪心, last表示从起点出发能到达的最远距离
        int n = nums.size(), last = 0;
        for(int idx=0; idx<n; idx++){
            if(idx > last)  return false;   // 已经无法到达idx了
            last = max(idx+nums[idx], last);
            if(last >= n - 1)   break;  // 提前结束
        }
        return true;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)