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
    if(left>=right) return;
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

### 堆排序

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
    if(L==R)    return ;
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

