# LeetCode234：回文链表

## 题目

请判断一个链表是否为回文链表。

示例 1:

> 输入: 1->2
> 输出: false

示例 2:

> 输入: 1->2->2->1
> 输出: true

进阶：

> 你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/palindrome-linked-list
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 解法一

不考虑空间复杂度的要求，直接用数组就可以解决，但是这种方法在面试的时候通不过(leetcode评论区有位老哥面试快手的时候就问了同样的题目，总之链表的题最好不要用数组来解)。代码如下：

```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        vector<int> a;
        ListNode * p=head;
        // 将链表元素依次存入数组
        while(p){
            a.push_back(p->val);
            p=p->next;
        }
        int n=a.size();
        // 在数组中进行比较
        for(int i=0; i<=n/2 && n; i++){
            if(a[n-1-i]!=a[i]) return false;
        }
        return true;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二

一样思想，使用栈实现，听说栈是解链表题最后的底线...，代码如下：

```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        stack<int> stk;
        ListNode *s=head, *f=head; // 使用快慢指针
        // s每次走一步，f走两步，当f走到头的时候，s正好指在中间节点
        while(f && f->next){
            stk.push(s->val);
            s=s->next;
            f=f->next->next;
        }
        // 进行调整，如果链表长度为奇数，s往后移一位
        if(f) s=s->next;
        // 利用栈先进后出的特点，进行比较
        while(!stk.empty() && s){
            if(s->val!=stk.top()) return false;
            stk.pop();
            s=s->next;
        }
        return true;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法三

为了解决空间复杂度的问题(不开辟额外空间)，那么就需要将链表拆成两半，进行反转然后比较。

具体做法就是先利用快慢指针找到链表中点，然后翻转后半段的链表，依次比较前半段与后半段的值（需要注意比较完之后还需要还原链表）。

代码如下：

```cpp
class Solution {
public:
    // 翻转链表
    ListNode * reverseList(ListNode* head){
        if (head == nullptr)    return head;
        ListNode * nowP = head, * preP = nullptr, * nextP;
        while (nowP) {
            nextP = nowP->next;
            nowP->next = preP;
            preP = nowP;
            nowP = nextP;
        }
        return preP;
    }

    bool isPalindrome(ListNode* head) {
        ListNode * slowP = head, * fastP = head;
        // 快慢指针，找到链表中点
        while (fastP->next != nullptr && fastP->next->next != nullptr) {
            slowP = slowP->next;
            fastP = fastP->next->next;
        }
        // 翻转后半段链表
        ListNode * p = slowP->next;
        slowP->next = nullptr;
        p = reverseList(p);
        // 判断是否回文
        ListNode * p1 = head, * p2 = p;
        bool ans = true;
        while (p1 && p2) {
            if (p1->val != p2->val) {
                ans = false;
                break;
            }
            p1 = p1->next;  p2 = p2->next;
        }
        // 还原链表
        slowP->next = reverseList(p);
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)