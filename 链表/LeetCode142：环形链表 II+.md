# LeetCode142：环形链表 II

## 题目

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。**注意，`pos` 仅仅是用于标识环的情况，并不会作为参数传递到函数中。**

**说明：**不允许修改给定的链表。

**进阶：**

- 你是否可以使用 `O(1)` 空间解决此题？

 

**示例 1：**

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png" />

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png" />

```
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png" />

```
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
```

 

**提示：**

- 链表中节点的数目范围在范围 `[0, 104]` 内
- `-105 <= Node.val <= 105`
- `pos` 的值为 `-1` 或者链表中的一个有效索引

## 解法一：存储已遍历节点

使用`unordered_set`存储已遍历的节点，当出现第二次被遍历到的节点时，即为环的第一个节点。代码如下：

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> st;
        
        ListNode *p = head;
        while(p){
            if(st.find(p)!=st.end())  return p;
            st.insert(p);
            p = p->next;
        }

        return NULL;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二

如`LeetCode141`题，我们也可以使用快慢指针来解决。与141题不同的是，本题需要找到入环的第一个节点。

<img src="https://assets.leetcode-cn.com/solution-static/142/142_fig1.png" alt="fig1" style="zoom: 20%;" />

如果链表中出现环，根据141的分析，慢指针一定会在某个点相遇。如上图所示：假设`slow`和`fast`第一次相遇的点与环入口处的距离为`b`，我们知道：① 相遇时，`fast`比`slow`多走了n圈(1圈=b+c)；② `fast`比`slow`多走两倍距离。那么可以得出结论：a = (n-1)倍环长 + c
$$
a+b+n(b+C)=2(a+b) → a = (n-1)(b+c) +c
$$
现在我们再额外放置一个慢指针`temp`在起始处，同时与`slow`一起向后移动，当它们相遇时，temp指向的节点即为环的入口处。这是因为当`temp`走到入口处是，`slow`正好走了**(n-1)圈+c**，从图上可以看出，也到了入口处。

```c++
ListNode *detectCycle(ListNode *head) {
        ListNode *fast=head, *slow=head; // 快慢指针
        while(fast && fast->next){
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow)    break;  // 第一次相遇
        }
        // 不存在环
        if(fast==nullptr || fast->next==nullptr)    return nullptr;
        // 存在环，并且fast与slow已经相遇
        ListNode* temp = head;
        while(temp!=slow){
            temp = temp->next;
            slow = slow->next;
        }
        return temp;
    }
```

时间复杂度：O(n)

空间复杂度：O(1)

