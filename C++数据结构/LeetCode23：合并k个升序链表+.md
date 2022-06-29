# LeetCode23：合并k个升序链表

## 题目

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

 

**示例 1：**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**

```
输入：lists = []
输出：[]
```

**示例 3：**

```
输入：lists = [[]]
输出：[]
```

 

**提示：**

- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` 按 **升序** 排列
- `lists[i].length` 的总和不超过 `10^4`

## 解法一：暴力

一种很简单的方法就是不管有几条链表，先全部放到一个数组里，统一排好序之后再返回结果。代码如下：

```c++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n = lists.size();
        vector<int> temp;
        // 所有数据压入vector中
        for(int i=0; i<n; i++){
            ListNode* p = lists[i];
            while(p!=nullptr){
                temp.push_back(p->val);
                p = p->next;
            }
        }
        // 排序
        sort(temp.begin(), temp.end());
        // 返回结果
        ListNode *p, *head = new ListNode();
        p = head;
        for(int i=0; i<temp.size(); i++){
            p->next = new ListNode(temp[i]);
            p = p->next;
        }
        return head->next;
    }
};
```

时间复杂度：O($nk·log(nk)$)，n为链表最长长度

空间复杂度：O(n)

## 解法二：依次合并

解法一虽然能过，但是太暴力了，时间空间复杂度都难以让人接受。还有一种容易想到的方法就是依次进行两个链表间的归并（**合并两个有序链表**），代码如下：

```c++
class Solution {
public:
    ListNode* mergeSort(ListNode* a, ListNode* b){
        ListNode *head = new ListNode(), *p;
        p = head;
        while(a && b){
            if(a->val > b->val){
                p->next = b;
                b = b->next;
            }
            else{
                p->next = a;
                a = a->next;
            }
            p = p->next;
        }
        p->next = (a?a:b);
        return head->next;
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n = lists.size();
        ListNode* ans = nullptr;
        
        for(int i=0; i<n; i++){
            ans = mergeSort(ans, lists[i]);
        }
        return ans;
    }
};
```

时间复杂度：O($k^2n$)

空间复杂度：O(1)

## 解法三：归并

第三种解法就是采用归并排序的思想，将每个链表视为单元，进行归并排序，如下图所示：

![img](LeetCode23：合并k个升序链表+.assets/6f70a6649d2192cf32af68500915d84b476aa34ec899f98766c038fc9cc54662-image.png)

采用递归实现，代码如下：

```c++
class Solution {
public:
    ListNode* mergeTwoList(ListNode* a, ListNode* b){
        ListNode *head = new ListNode(), *p;
        p = head;
        while(a && b){
            if(a->val > b->val){
                p->next = b;
                b = b->next;
            }
            else{
                p->next = a;
                a = a->next;
            }
            p = p->next;
        }
        p->next = (a?a:b);
        return head->next;
    }

    ListNode* mergeSort(vector<ListNode*>& lists, int left, int right){
        if(left==right){
            return lists[left];
        }
        else if(left>right){
            return nullptr;
        }
        int mid = (left+right) / 2;
        ListNode* ans = mergeTwoList(mergeSort(lists, left, mid), mergeSort(lists, mid+1, right));
        return ans;
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n = lists.size();
        return mergeSort(lists, 0, n-1);
    }
};
```

时间复杂度：O($nk·log(k)$)

空间复杂度：O(logk)，递归所占用的空间

## 解法四：优先队列+

跟双指针的想法类似，每次获取k个链表位置`i`上的最小值，然后遍历完所有链表元素为止。为了方便，采用优先队列找到k个元素中的最小值。

```c++
struct cmp{ // 小顶堆
    bool operator() (ListNode* a, ListNode* b) {
        return a->val > b->val;
    }
};

class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n = lists.size();
        ListNode * head = new ListNode();
        priority_queue<ListNode*, vector<ListNode*>, cmp> pri_que;
        // 初始化
        for (auto i : lists) {
            if(i != nullptr)   pri_que.push(i);
        }
        
        ListNode * p = head;
        while (!pri_que.empty()) {
            ListNode* temp = pri_que.top();
            pri_que.pop();
            p->next = temp;
            p = p->next;
            if (temp->next != nullptr)  pri_que.push(temp->next);
        }
        
        return head->next;
    }
};
```

时间复杂度：O($nk·log(k)$)

空间复杂度：O(k)
