# 二分查找

> https://juejin.cn/post/7088341347584704520 只要涉及到$log(n)$的时间复杂度，十有八九都是二分查找
>

二分查找的思想估计大家都明白，就不多说了，但它其实也没有想象中的那么简单。特别是对于一些特殊情况和细节上的处理：

① 为了防止溢出：

```c++
mid = (right+left)/2； 改为 mid = (right-left)/2+left；
```

② 边界条件，并不是每个二分策略我们都是去找`target`的，比如去找到第一个小于等于`target`的值或者第一个大于等于`target`的值，这样就要注意边界条件的判断：

```c++
// 找到第一个满足条件的值  >= target
int binary(vector<int>& nums, int target){
    int l = 0, r = nums.size();
    while(l < r){
        int mid = (r - l) / 2 + l;
        if(nums[mid] < target){
            l = mid + 1;
        }
        else{
            r = mid;
        }
    }
    return l;
}

// 找到第一个满足条件的值 > target
int binary(vector<int>& nums, int target){
    int l = 0, r = nums.size();
    while(l < r){
        int mid = (r - l) / 2 + l;
        if(nums[mid] <= target){
            l = mid + 1;
        }
        else{
            r = mid;
        }
    }
    return l;
}
// 记忆: ① 优先考虑左边界的移动； ② 括号的条件置为相反.

// 找到最后一个满足条件的值 <=target [即找到第一个不满足条件的位置-1]
int binary(vector<int>& nums, int target){
    int l = 0, r = nums.size();
    while(l < r){
        int mid = (r - l) / 2 + l;
        if(nums[mid] <= target){
            l = mid + 1;
        }
        else{
            r = mid;
        }
    }
    return l - 1;
}
// 记忆：① 优先考虑左边界移动； ② 转换为"找到第一个不满足条件的位置"; ③ 括号内条件保持一致；④ 最后返回l-1.
```

#### lower_bound和upper_bound

c++的`STL`库给我们提供了现有的二分查找库，可以在笔试的时候很方便地进行调用，主要是`lower_bound()`和`upper_bound()`。

```c++
// 找到vector中第一个 ≥ val的地址，如果找不到返回v.end()
lower_bound(v.begin(), v.end(), val);
// 找到vector中第一个 ＞ val的地址，如果找不到返回v.end()
upper_bound(v.begin(), v.end(), val);
// 使用如下代码即可得到索引
int p = lower_bound(v.begin(), v.end(), val) - v.begin();
```

#### 二分问题的常用解题思路

* 判断这个问题的求解是否具有单调性(随着x的增加，f(x)的求解值也会增加)，比较常见的问题有求第n个数字、递增函数根的求解、完成某件事的最短or最长时间等等。**只有满足单调性的问题才能用二分法进行求解**；
* 下一步就是确定问题是上界和下界，这通常需要根据题目提供的数据范围来判断；
* 最后在编写二分逻辑，几个关键点：**左指针移动的条件**是什么？左指针怎么移动(`mid+1` or `mid`)？右指针怎么移动？可以利用上面的模板求解。
