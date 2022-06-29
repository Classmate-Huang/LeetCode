

# LeetCode6010：完成旅途的最少时间

## 题目

给你一个数组 time ，其中 time[i] 表示第 i 辆公交车完成 一趟旅途 所需要花费的时间。

每辆公交车可以 连续 完成多趟旅途，也就是说，一辆公交车当前旅途完成后，可以 立马开始 下一趟旅途。每辆公交车 独立 运行，也就是说可以同时有多辆公交车在运行且互不影响。

给你一个整数 totalTrips ，表示所有公交车 总共 需要完成的旅途数目。请你返回完成 至少 totalTrips 趟旅途需要花费的 最少 时间。

 

示例 1：

```
输入：time = [1,2,3], totalTrips = 5
输出：3
解释：

- 时刻 t = 1 ，每辆公交车完成的旅途数分别为 [1,0,0] 。
  已完成的总旅途数为 1 + 0 + 0 = 1 。
- 时刻 t = 2 ，每辆公交车完成的旅途数分别为 [2,1,0] 。
  已完成的总旅途数为 2 + 1 + 0 = 3 。
- 时刻 t = 3 ，每辆公交车完成的旅途数分别为 [3,1,1] 。
  已完成的总旅途数为 3 + 1 + 1 = 5 。
  所以总共完成至少 5 趟旅途的最少时间为 3 。
```

示例 2：

```
输入：time = [2], totalTrips = 1
输出：2
解释：
只有一辆公交车，它将在时刻 t = 2 完成第一趟旅途。
所以完成 1 趟旅途的最少时间为 2 。
```


提示：

```
1 <= time.length <= 105
1 <= time[i], totalTrips <= 107
```

## 解法一：二分法

找到问题的上下界，进行二分查找。首先找到速度最快的公交车（假设一趟时间为`x`），那么这个问题的上界就是`totalTrips*x`，下界设为1。

需要注意的是在使用c++要避免溢出，因此需要使用`break早停`，代码如下：

```c++
    long long minimumTime(vector<int>& time, int totalTrips) {
        int tMin = *min_element(time.begin(), time.end());

        long long right = long(totalTrips) * long(tMin);
        long long left = 1;
        // 二分
        while(left < right){
            long long mid = left + (right-left) / 2;
            
            int curTrips = 0;
            for(auto& i:time){
                curTrips += mid/i;
                if(curTrips >= totalTrips){	// 满足条件就立刻停止
                    right = mid;
                    break;
                }
            }
            if(curTrips < totalTrips)   left = mid+1;
        }
        return left;
    }
};
```

时间复杂度：O(nlogn)

空间复杂度：O(1)