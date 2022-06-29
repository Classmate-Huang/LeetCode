# LeetCode24：两两交换链表中的节点

## 题目

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```
输入：head = []
输出：[]
```

**示例 3：**

```
输入：head = [1]
输出：[1]
```

 

**提示：**

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`

 

**进阶：**你能在不修改链表节点值的情况下解决这个问题吗?（也就是说，仅修改节点本身。）

## 解法一：模拟

直接模拟构造的过程即可，pre为之前的节点，now为当前待处理的节点，以两个节点为一次操作单位：

① 首先记录 t = now->next->next

② 进行两两交换：now->next->next = now,  pre->next = now->next; now->next = t;

③ 更新pre和now：pre = now; now = t;

代码如下：

```c++
class Solution {
public:
    void change(ListNode* & pre, ListNode* & now){
        ListNode* t = now->next->next;
        // 交换
        now->next->next = now;
        pre->next = now->next;
        now->next = t;
        pre = now;
        now = t;
    }

    ListNode* swapPairs(ListNode* head) {
        ListNode* temp = new ListNode();
        temp->next = head;
        ListNode* pre = temp, *now = head;
        while(now && now->next){
            change(pre, now);
        }
        return temp->next;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)

