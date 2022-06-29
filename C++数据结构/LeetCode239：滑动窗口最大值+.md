# LeetCode239：滑动窗口最大值

## 题目

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回滑动窗口中的最大值。

 

**示例 1：**

```
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**示例 2：**

```
输入：nums = [1], k = 1
输出：[1]
```

**示例 3：**

```
输入：nums = [1,-1], k = 1
输出：[1,-1]
```

**示例 4：**

```
输入：nums = [9,11], k = 2
输出：[11]
```

**示例 5：**

```
输入：nums = [4,-2], k = 2
输出：[4]
```

 

**提示：**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`
- `1 <= k <= nums.length`



## 解法一：优先队列

维护一个优先队列存储窗口内的值，方便提取最大值。在过程中采用**延迟删除**的操作，每次滑动窗口后，不急着删除无效元素【窗口外的值】，只有当无效元素占据堆顶元素时才进行删除。

为了判断堆顶元素是否为无效元素，优先队列存储`pair<int, int>`元素值和元素对应的索引值，当堆顶元素索引超过窗口范围内时即为无效元素，弹出队列。

```c++
struct cmp{ // 大顶堆
    bool operator() (pair<int, int>& a, pair<int, int>& b) {
        return a.first < b.first;
    }
};

class Solution {
public:
    priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> pri_que;
    
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> ans;
        
        int l = 0, r = 0;
        while (r < n) {
            pri_que.push(make_pair(nums[r], r));
            if (r-l+1 == k) {
                while (pri_que.top().second < l)    pri_que.pop();
                ans.push_back(pri_que.top().first);
                l++;
            }
            r++;
        }
        return ans;
    }
};
```

时间复杂度：O($nlogn$)

空间复杂度：O(n)

## 解法二：双端队列

当一个窗口内存在两个元素`nums[i],nums[j]`，且存在`i<j,num[i]<nums[j]`，可以明确：在后续窗口滑动过程中，最大值都不会是`nums[i]`【因为`i`出现在窗口内，`j`必然也出现在窗口内，并且`nums[j]>nums[i]`】。因此维护一个队列，队列中的元素只包含窗口内的值，且为非递增排列【单调队列】。

这样就可以轻易地找到窗口内的最大值，但同时也需要删除无效元素【不在窗口内的值】，所以采用双端队列。删除队首的无效元素。代码如下：

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<pair<int, int>> deQue;  // 维护一个递减队列代替优先队列
        vector<int> ans;
        
        int l = 0, r = 0;
        while (r < n) {
            while (!deQue.empty() && nums[r] > deQue.back().first)    deQue.pop_back();
            deQue.push_back(make_pair(nums[r], r));
            if (r-l+1 == k) {
                while (deQue.front().second < l)    deQue.pop_front();
                ans.push_back(deQue.front().first);
                l++;
            }
            r++;
        }
        
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(k)