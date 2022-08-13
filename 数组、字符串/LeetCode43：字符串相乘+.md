# LeetCode43：字符串相乘

## 题目

给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

注意：不能使用任何内置的 BigInteger 库或直接将输入转换为整数。

 

示例 1:
```
输入: num1 = "2", num2 = "3"
输出: "6"
```
示例 2:
```
输入: num1 = "123", num2 = "456"
输出: "56088"
```

提示：
```
1 <= num1.length, num2.length <= 200
num1 和 num2 只能由数字组成。
num1 和 num2 都不包含任何前导零，除了数字0本身。
```

## 解法一：利用vecotr进行加法操作

两个数的乘法可以拆分成多个数的加法来做，用`num1`去乘以`num2`上的每一位得到中间结果(注意尾随0的个数)，然后对所有的中间结果进行相加，但这类做法时间复杂度较高，为O(mn+$n^2$)。

一种比较简单的做法是将每一位都换成int存在`vector<int>`数组中，进行每一位的乘法+加法操作，最后再对数组进行一次进位操作即可。

> m位的数字a和n位的数字b，它们的乘积最多有`m+n`位，最少有`m+n-1`位。

代码如下：

```cpp
class Solution {
public:
    string multiply(string num1, string num2) {
        if (num1 == "0" || num2 == "0") return "0";
        int n1 = num1.size(), n2 = num2.size();
        vector<int> res(n1+n2, 0);
        // 对每一位进行乘+加法操作,res[i]中每一位的值域不在是[0,9]
        for (int i = n1-1; i >= 0; i--) {
            int x = num1[i] - '0';
            for (int j = n2-1; j >= 0; j--) {
                int y = num2[j] - '0';
                res[i+j+1] += x * y;    
            }
        }
        // 进行进位处理
        int flag = 0;
        for (int i = n1+n2-1; i >= 0; i--) {
            res[i] += flag;
            flag = res[i] / 10;
            res[i] = res[i] % 10;
        }
        // 转换成字符串
        string ans = "";
        for (int i = 0; i < n1+n2; i++) {
            if (i == 0 && res[i] == 0)  continue;
            ans += to_string(res[i]);
        }
        return ans;
    }
};
```
时间复杂度：O(mn)

空间复杂度：O(m+n)