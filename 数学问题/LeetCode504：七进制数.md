# LeetCode504：七进制数

## 题目

给定一个整数 `num`，将其转化为 **7 进制**，并以字符串形式输出。

 

**示例 1:**

```
输入: num = 100
输出: "202"
```

**示例 2:**

```
输入: num = -7
输出: "-10"
```

 

**提示：**

- `-107 <= num <= 107`

  

## 解法一：进制转换

比较简单，主要就是得注意负数的情况。

```c++
class Solution {
public:
    string convertToBase7(int num) {
        string ans = "";

        bool flag = false;  // 是否为负数
        if(num<0){  // 为负数先转换成正数（避免求余操作出现符号）
            flag = true;
            num = 0-num;
        }
        
        do{
            string c = to_string(num%7);
            num /= 7;
            ans = c + ans;
        }while(num!=0);

        if(flag)    ans = "-" + ans; // 添加符号

        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)