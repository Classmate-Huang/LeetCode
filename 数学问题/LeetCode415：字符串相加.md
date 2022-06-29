# LeetCode415：字符串相加

## 题目

给定两个字符串形式的非负整数 `num1` 和`num2` ，计算它们的和并同样以字符串形式返回。

你不能使用任何內建的用于处理大整数的库（比如 `BigInteger`）， 也不能直接将输入的字符串转换为整数形式。

 

**示例 1：**

```
输入：num1 = "11", num2 = "123"
输出："134"
```

**示例 2：**

```
输入：num1 = "456", num2 = "77"
输出："533"
```

**示例 3：**

```
输入：num1 = "0", num2 = "0"
输出："0"
```

 

 

**提示：**

- `1 <= num1.length, num2.length <= 104`
- `num1` 和`num2` 都只包含数字 `0-9`
- `num1` 和`num2` 都不包含任何前导零

## 解法一：模拟

模拟逐位加法操作，需要注意进位的细节。代码如下：

```c++
class Solution {
public:
    string addStrings(string num1, string num2) {
        string ans = "";
        int p1 = num1.size()-1, p2 = num2.size()-1, flag=0, t;

        while(p1>=0 || p2>=0){
            if(p2<0){
                t = num1[p1] - '0' + flag;
                p1--;
            }
            else if(p1<0){
                t = num2[p2] - '0' + flag;
                p2--;
            }
            else{
                t = num1[p1] + num2[p2] - 2*'0' + flag;
                p1--; p2--;
            }
            ans = to_string(t%10) + ans;
            flag = t/10;
        }

        if(flag)    ans = "1" + ans;
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)