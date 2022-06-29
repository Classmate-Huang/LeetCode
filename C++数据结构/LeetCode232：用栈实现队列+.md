# LeetCode232：用栈实现队列

## 题目

请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）：

实现 `MyQueue` 类：

- `void push(int x)` 将元素 x 推到队列的末尾
- `int pop()` 从队列的开头移除并返回元素
- `int peek()` 返回队列开头的元素
- `boolean empty()` 如果队列为空，返回 `true` ；否则，返回 `false`

 

**说明：**

- 你只能使用标准的栈操作 —— 也就是只有 `push to top`, `peek/pop from top`, `size`, 和 `is empty` 操作是合法的。
- 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。

 

**进阶：**

- 你能否实现每个操作均摊时间复杂度为 `O(1)` 的队列？换句话说，执行 `n` 个操作的总时间复杂度为 `O(n)` ，即使其中一个操作可能花费较长时间。

 

**示例：**

```
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```

**提示：**

- `1 <= x <= 9`
- 最多调用 `100` 次 `push`、`pop`、`peek` 和 `empty`
- 假设所有操作都是有效的 （例如，一个空的队列不会调用 `pop` 或者 `peek` 操作）



## 解法一：入队+出队

一个栈用于处理**入队**操作，凡是有新入队的元素，先压入栈`normal`。

另一个栈用于处理查询和出队操作，由于栈`normal`存储的元素是LIFO的，不能直接基于它来处理，因此使用`reverse`来处理。里面保存的是反向的元素，也就是把`normal`中的元素反过来再压入栈中，这样先进入`normal`中的元素就会在接近栈顶，实现FIFO的效果。

代码如下：

```c++
class MyQueue {
public:

    stack<int> normal, reverse;
    
    MyQueue() {}
    
    void normal2reverse(){
        while(!normal.empty()){
            reverse.push(normal.top());
            normal.pop();
        }
    }

    void push(int x) {
        normal.push(x);
    }
    
    int pop() {
        if(reverse.empty()) normal2reverse();

        int t = reverse.top();
        reverse.pop();
        return t;
    }
    
    int peek() {
        if(reverse.empty()) normal2reverse();

        return reverse.top();
    }
    
    bool empty() {
        if(reverse.empty() && normal.empty())
            return true;
        return false;
    }
};
```

时间复杂度：O(1)

空间复杂度：O(1) 均摊