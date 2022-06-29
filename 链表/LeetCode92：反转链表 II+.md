# LeetCode92：反转链表 II

## 题目

给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。

![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)


示例 1：

```
输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
```


示例 2：

```
输入：head = [5], left = 1, right = 1
输出：[5]
```


提示：

```
链表中节点数目为 n
1 <= n <= 500
-500 <= Node.val <= 500
1 <= left <= right <= n
```


进阶： 你可以使用一趟扫描完成反转吗？

## 解法一：模拟

构造一个空节点NullHead指向head方便操作，首先找到链表反转的区间入口`left`。对`[left, right]`内的结点进行反转后进行拼接即可，如下所示：

```c++
class Solution {
public:
    // 反转链表[left, right]
    ListNode* reverse(ListNode* head, int left, int right){
        ListNode * nowP = head, * preP = nullptr, * nextP;
        for (int i = 0; i <= right - left; i++) {
            nextP = nowP->next;
            nowP->next = preP;
            preP = nowP;
            nowP = nextP;
        }
        head->next = nowP;
        head = preP;
        return head;
    }

    ListNode* reverseBetween(ListNode* head, int left, int right) {
        // 构造一个空头节点
        ListNode * Nullhead = new ListNode(-1);
        Nullhead->next = head;
        ListNode * preP = Nullhead, * nowP = head;
        // 找到反转区间的前一个节点
        for (int i = 0; i < left - 1; i++) {
            preP = nowP;
            nowP = nowP->next;
        }
        // 转换[left, right]区间
        preP->next = reverse(nowP, left, right);
        // 返回空节点的下一个节点
        return Nullhead->next;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)