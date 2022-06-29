# LeetCode342：4的幂

## 题目

给定一个整数，写一个函数来判断它是否是 4 的幂次方。如果是，返回 `true` ；否则，返回 `false` 。

整数 `n` 是 4 的幂次方需满足：存在整数 `x` 使得 `n == 4x`

 

**示例 1：**

```
输入：n = 16
输出：true
```

**示例 2：**

```
输入：n = 5
输出：false
```

**示例 3：**

```
输入：n = 1
输出：true
```

 

**提示：**

- `-231 <= n <= 231 - 1`

 

**进阶：**

- 你能不使用循环或者递归来完成本题吗？



## 解法一：常规的幂问题

与`3的幂`的解法类似，我们可以采用循环、或者底数变换的方法，或者算出32位限制下4的最大指数值进行除法。

```c++
// 循环的方法 O(n)
class Solution {
public:
    bool isPowerOfFour(int n) {
        if(n<1) return false;
        while(n!=1){
            if(n%4) return false;
            n /= 4;
        }
        return true;
    }
};
// 底数变化
class Solution {
public:
    bool isPowerOfFour(int n) {
        if(n<1) return false;
        double t = log(n)/log(4);
        return t==floor(t);
    }
};
```

## 解法二：位运算

若采用位运算的效率非常高：先判断字符是否为2的幂次方（2进制只含一个1），再判断是否为4的幂（检查`1`出现的位置）。

```c++
class Solution {
public:
    bool isPowerOfFour(int n) {
        return (n>0) && (n&(n-1))==0 && (n&(0xAAAAAAAA))==0;
    }
};
```

