# LeetCode400：第N位数字

## 题目

给你一个整数 n ，请你在无限的整数序列 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...] 中找出并返回第 n 位上的数字。

 

示例 1：

```
输入：n = 3
输出：3
```

示例 2：

```
输入：n = 11
输出：0
解释：第 11 位数字在序列 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... 里是 0 ，它是 10 的一部分。
```


提示：

```
1 <= n <= 231 - 1
```

## 解法一：模拟+找规律

首先按每位数组合出现的个数分组，比如1位数(1~9)出现了9次，2位数(10~99)出现了90次，...，n位数出现了$9*10^{digit-1}*digit$次。对于n，我们首先找到其位于那个组（属于几位数）。

找到n在几位数中之后，接下来要确定其具体位于哪个数中。比如n=10000，我们找到其属于`digit=4`位数。那么四位数的起始数字为1000，并且前面一共出现了`sum=9+90*2+900*3`bit数字，求到其在四位数中的偏移量，`n-sum-1`为了定位方便，这里减去1（表明索引从0开始），算出来为7110。说明在n位于以1000为起始点的第7110位，每位数字由`digit`位组成，计算`7110/4 = 1777...2`，说明第n位是在`1000+1777=2777`中的第2位数字（注意索引均从0开始），返回7.

代码如下：

```c++
class Solution {
public:

    int getShiftNum(int x, int t){  // 返回x从左往右的第t位数字
        string s = to_string(x);
        return int(s[t]-'0');
    }

    int findNthDigit(int n) {
        int digit = 1;  // 确定n处于几位数
        long long sum = 0, count = 9 * 1;
        while(sum+count < n){
            digit++;
            sum += count;
            count = 9 * pow(10, digit-1) * digit;
        }
        // -1是为了方便从第0位开始计数
        int shiftNum = (n - sum - 1) / digit;    // 距离10^digigt-1的偏移量
        int shiftBit = (n - sum - 1) % digit;   // 处于第几位
        int targetNum = pow(10, digit-1) + shiftNum;    // 定位到具体的数字
        
        return getShiftNum(targetNum, shiftBit);
    }
};
```

