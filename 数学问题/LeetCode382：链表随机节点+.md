# LeetCode382：链表随机节点

## 题目

给定一个单链表，随机选择链表的一个节点，并返回相应的节点值。保证每个节点**被选的概率一样**。

**进阶:**
如果链表十分大且长度未知，如何解决这个问题？你能否使用常数级空间复杂度实现？

**示例:**

```
// 初始化一个单链表 [1,2,3].
ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);
Solution solution = new Solution(head);

// getRandom()方法应随机返回1,2,3中的一个，保证每个元素被返回的概率相等。
solution.getRandom();
```

## 解法一：朴素办法

最朴素的一个方法就是在对象初始化时，将链表中的元素存在数组`arrs`中。然后再随机在`0~arrs.size()`之间选择一个数，返回对应的元素即可。

```c++
class Solution {
public:
    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    vector<int> arrs;
    Solution(ListNode* head) {
        ListNode* p = head;
        while(p){
            arrs.push_back(p->val);
            p = p->next;
        }
    }
    
    /** Returns a random node's value. */
    int getRandom() {
        int sum = arrs.size();
        int idx = rand()%sum;
        return arrs[idx];
    }
};
```

时间复杂度：O(n)，初始化为O(n)，查询随机节点为O(1)

空间复杂度：O(n)

## 解法二：水库算法

假设在大数据流的场景下无法事先保留所有元素，这就会涉及到`蓄水池算法/水库算法`。首先保留第1个数字，对于之后遍历到的第m个数，有`1/m`保留当前这个数(覆盖掉了之前保留下的数字)，有`(m-1)/m`的概率丢弃当前的数(保留之前的值)。

简单的证明：假设算法遍历过一遍链表，最后筛选出来值为第`m`个数，那么它保留下来的概率为：

$\frac{1}{m} \times \frac{m}{m+1} \times \frac{m+1}{m+2} \times ...  \times \frac{n-2}{n-1} \times \frac{n-1}{n} = \frac{1}{n}$

之所以叫水库算法，是因为水库就是当前保留的值（满了），有新的值进来时，就得有旧的出去，因为水库的容量是一定的。

> 详细题解：https://leetcode-cn.com/problems/linked-list-random-node/solution/xu-shui-chi-chou-yang-suan-fa-by-jackwener/

```c++
class Solution {
public:
    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    ListNode* my_head;
    Solution(ListNode* head) {
        my_head = head;
    }
    
    /** Returns a random node's value. */
    int getRandom() {  // 水库算法
        int idx = 1, ans;
        ListNode* p = my_head;
        while(p){
            int t = rand()%idx;
            if(t==0){
                ans = p->val;
            }
            p = p->next;
            idx++;
        }
        return ans;
    }
};
```

时间复杂度：O(n)，每次查找都要O(n)

空间复杂度：O(1)