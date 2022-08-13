## 前言

虽然c++已经提供了非常成熟的实现，比如快速排序`sort()`和归并排序`stable_sort()`，提供了自定义的排序规则，如下所示：

```c++
//以普通函数的方式实现自定义排序规则
bool cmp1(int i, int j) {
    return (i < j);
}
//以函数对象的方式实现自定义排序规则[这个也用于priority_queue]
class cmp2 {
public:
    bool operator() (int i, int j) {
        return (i < j);
    }
};
```

### 快速排序

确定数组中某个target，左边全是小于其的值，右边是大于其的值，然后往下递归。

主要利用到荷兰国旗问题的方法：将小于target的元素全部放在数组左边，等于target的放在中间，大于target的放在右侧。规则：三个指针(lBound, i, rBound)，等于则i++，小于swap(nums[i++],nums[lBound++])，大于则swap(nums[i], nums[rBound--])。

```c++
// 快速排序
void quickSort(vector<int>& nums, int left, int right){
    if(left >= right) return;
    // 随机交换
    swap(nums[left], nums[rand()%(right-left+1)+left]);
    int target = nums[left], lBound = left, rBound = right, i = left;
    // 荷兰国旗
    while(i<=rBound){
        if(nums[i]==target) i++;
        else if(nums[i]<target) swap(nums[i++], nums[lBound++]);
        else    swap(nums[i], nums[rBound--]);
    }
    quickSort(nums, left, lBound-1);
    quickSort(nums, rBound+1, right);
}
```

**快速选择**：快排的一个经典应用就是找到数组中第k大个数，使用"快排+二分"的思想进行编码，能够将时间复杂度控制在O(n)。题215.

### 堆排序*

堆主要利用数组模拟一棵二叉树，其中获取最大值叫大根堆，获取最小值叫小根堆。几个需要注意的点：

① 在数组中，当前节点的父节点是`(i-1)/2`，左孩子节点`2*i+1`，右孩子节点`2*i+2`，数组长度`heapsize`；

② 上浮操作(`heapInsert`)：当新加入了一个数，将其放在数组尾部，`heapsize++`，进行上浮操作确保堆结构；

③ 下沉操作(`heapfiy`)：取出堆顶元素后，将最后尾部元素放置在首部，`heapsize--`，进行下沉操作确保堆结构。

> 堆这个结构比堆排序本身要更重要，优先队列也是基于堆实现的

```c++
// 上浮操作
void heapInsert(vector<int>& nums, int index){
    int root = (index-1)/2; // 父节点
    while(nums[index] > nums[root]){
        swap(nums[index], nums[root]);
        index = root;
        root = (index-1)/2;
    }
}

// 下沉操作
void heapify(vector<int>& nums, int index, int heapsize){
    int leftChild = 2*index+1;
    while(leftChild < heapsize){
        // 选择最大孩子
        int largestChild = leftChild;
        if(leftChild+1 < heapsize && nums[leftChild+1] > nums[leftChild])  largestChild++;
        // 比较当前节点和最大孩子节点
        if(nums[index] > nums[largestChild])    break;
        swap(nums[index], nums[largestChild]);
        index = largestChild;
        leftChild = 2*index+1;
    }
}

void heapSort(vector<int>& nums){
    if(nums.size() < 2) return;
    // 插入所有
    for(int i=0; i<nums.size(); i++){
        heapInsert(nums, i);
    }
    // 依次取当前堆顶元素放到后面
    int heapsize = nums.size();
    swap(nums[0], nums[--heapsize]);
    while(heapsize > 0){
        heapify(nums, 0, heapsize);
        swap(nums[0], nums[--heapsize]);
    }
}
```

### 归并排序

归并排序的思想就是：将数组分为两个子数组，先对每个子数组排序，然后对两个有序子数组进行排序即可【利用递归排序】。

```c++
// arr[L:M] 有序  arr[M+1:R] 有序  合并两个有序数组
void merge(vector<int>& arr, int L, int R, int M){
    vector<int> help(R-L+1);
    int i = 0, p1 = L, p2 = M+1;
    while(p1<=M && p2<=R){
        help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
    }
    while(p1<=M){
        help[i++] = arr[p1++];
    }
    while(p2<=R){
        help[i++] = arr[p2++];
    }
    for(i=0; i<help.size(); i++){
        arr[L+i] = help[i];
    }
}
// 分治
void process(vector<int>& arr, int L, int R){
    if(L == R)    return ;
    int mid = (R - L) / 2 + L;
    process(arr, L, mid);
    process(arr, mid+1, R);
    merge(arr, L, R, mid);
}
// 归并排序
void mergeSort(vector<int>& arr){
    process(arr, 0, arr.size()-1);
}
```

#### 归并排序扩展： 小和问题 / 逆序对问题 *

**小和问题**：计算数组中的**每个元素左侧比它小的元素**，累加得到结果。例子：[1,3,4,2,5]，其结果为`0+1+(1+3)+1+(1+3+4+2)=16`。（求右边有多少个数比当前大）

**逆序对问题**：在数组中的两个数字，如果**前面一个数字大于后面的数字**，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。例子：[7,5,6,4]，结果为`5`（求右边有多少个数比当前小）

这两类问题都可以通过归并排序的思想进行解决，在归并排序的过程中，对于左右两个有序子数组，我们在合并的同时可以知道：**左子数组中有多少个数大于右子数组** or **右子数组中有多少个数大于左子数组**。

如果需要单独判断**右边有多少小于当前值的数字**，采用**递减**排序；如果需要判断**左边有多少大于当前值的数字**，采用**递增**排序。并且实现的时候需要注意细节：**当两个指针指向的数字相等时，应该移动左指针还是右指针**。

逆序对的代码见`剑指Offer51`以及315题，这里列出小和问题的代码：

```c++
// arr[L:M] 有序  arr[M+1:R] 有序  合并两个有序数组，顺便求小和
int merge(vector<int>& arr, int L, int R, int M){
    vector<int> help(R-L+1);
    int i = 0, p1 = L, p2 = M+1, ans = 0;
    while(p1<=M && p2<=R){
        // 累加小和
        if(arr[p1] < arr[p2])   ans += arr[p1] * (R-p2+1);
        // 求小和时, 相等先移动右指针
        help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
    }
    while(p1<=M){
        help[i++] = arr[p1++];
    }
    while(p2<=R){
        help[i++] = arr[p2++];
    }
    for(i=0; i<help.size(); i++){
        arr[L+i] = help[i];
    }
    return ans;
}
// 递归的过程中顺便求小和
int process(vector<int>& arr, int L, int R){
    if(L==R)    return 0;
    int mid = (R - L) / 2 + L;
    return process(arr, L, mid) + process(arr, mid+1, R) + merge(arr, L, R, mid);
}
```

上面介绍的排序算法都是基于“比较”的排序方法，这类排序算法时间复杂度最优就是O(nlogn)，下面介绍一些"非比较"的排序方法，这类方法在某些情况下性能可以达到更优。

### 计数排序 *

> 参考：https://www.cxyxiaowu.com/5437.html

当我们确定了数据的范围在一个比较小的区间内的话，比如`0~10`的整数区间内，我们就可以采用计数排序：初始化一个固定大小的数组cnt，分别记录每个整数出现的次数，然后再依次输出即可。

**改进1**：我们并不需要将cnt与记录值一一对应起来，而是记录数组中最小值与最大值之间的增值即可，比如范围在`90~100`的数据，我们也只需要开辟一个长度为11的数组空间。

**改进2**：在某些情况下，我们需要构造稳定排序（比如成绩排名），不希望经过计数排序后改变了原先的位置，所以就要用到一个技巧：变形数组。在统计了所有数字出现的次数后，从前往后对cnt进行一次前缀和处理(每个元素都加上前面的元素之和)。在输出结果数组时，从后往前遍历原始数组，查到cnt中对应的数字即为他在最终结果中的位次(每次查询后需要将对应的cnt--，下次遇到相同值的元素时放在更前排)。

代码如下：

```cpp
void countSort(vector<int>& nums) {
    int n = nums.size();
    // 1. 计算得到最大值与最小值
    int maxNum = nums[0], minNum = nums[0];
    for (auto i : nums) {
        maxNum = max(maxNum, i);
        minNum = min(minNum, i);
    }
    int d = maxNum - minNum + 1;
    // 2. 计数
    vector<int> cnt(d, 0);
    for (auto i : nums) {
        cnt[i-minNum]++;
    }
    // 3. 计数数组变形 - 前缀和
    for (int i = 1; i < d; i++) {
        cnt[i] += cnt[i-1];
    }
    // 4. 根据计数数组进行排序
    vector<int> sortNum(n);
    for (int i = n-1; i >= 0; i--) {
        int p = --cnt[nums[i]-minNum];
        sortNum[p] = nums[i];
    }
    nums.assign(sortNum.begin(), sortNum.end());
}
```
时间复杂度：O(N+M), 空间复杂度：O(N+M), M表示数据阈值范围(代码中的d).

**局限性**：① 对应数据范围相差特别大的数据难以应用（需要开辟很大的计数数组）→ 这个可以使用桶排序来解决；② 当数据是小数时难以应用。

### 基数排序 *

对于整数，还有一种常见的排序算法就是基数排序。其核心思想在于“按照每个数位，对每个数据元素进行比较”，可以根据从高到低排序 or 从低到高排序分为MSD和LSD两种具体算法。这里介绍从低到高的LSD算法。
> 从高到低的算法需要采用桶排序的思想

常见的就是10基数，LSD就是分别抽取每个元素在个位、十位、百位...上的数字，进行计数排序。代码如下：

```cpp
// 传入待排序数组与最大元素位数
void radixSort(vector<int>& nums, int bit) {
    int n = nums.size(), base = 1;
    for (int k = 0; k < bit; k++, base *= 10) {     // 遍历每一位
        // 计数排序
        vector<int> cnt(10, 0);
        for (int i : nums) {
            cnt[(i/base)%10]++;   // 获取第k位上的数
        }
        for (int i = 1; i < 10; i++) {  // 计数数组变形
            cnt[i] += cnt[i-1];
        }
        vector<int> tempNums(n);
        for (int i = nums.size()-1; i >= 0; i--) {
            int p = --cnt[(nums[i]/base)%10];
            tempNums[p] = nums[i];
        }
        nums.assign(tempNums.begin(), tempNums.end());
    }
}
```

时间复杂度：O(N*M), 空间复杂度：O(N), M为最长元素的位数

基数排序的具体题目可以参照题LeetCode6121.