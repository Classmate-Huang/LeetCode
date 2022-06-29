# LeetCode696：计数二进制子串

## 题目

给定一个字符串 `s`，计算具有相同数量 0 和 1 的非空（连续）子字符串的数量，并且这些子字符串中的所有 0 和所有 1 都是连续的。

重复出现的子串要计算它们出现的次数。

 

**示例 1 :**

```
输入: "00110011"
输出: 6
解释: 有6个子串具有相同数量的连续1和0：“0011”，“01”，“1100”，“10”，“0011” 和 “01”。

请注意，一些重复出现的子串要计算它们出现的次数。

另外，“00110011”不是有效的子串，因为所有的0（和1）没有组合在一起。
```

**示例 2 :**

```
输入: "10101"
输出: 4
解释: 有4个子串：“10”，“01”，“10”，“01”，它们具有相同数量的连续1和0。
```

 

**提示：**

- `s.length` 在1到50,000之间。
- `s` 只包含“0”或“1”字符。



## 解法一：计数

对于一个字符串，假设为`00110011`，记录下相同字符连续出现的次数`arr = {2,2,2,2}`。要求得满足条件的子串数，其实就是对次数数组进行窗口为`2`的处理：`min{arr[i], arr[i+1]}`。

具体实现就是用一个last来记录当前字符，然后遍历字符串得到该字符连续出现的次数。如果出现与last不相符的字符，记录次数到arr中。代码如下：

```c++
class Solution {
public:
    int countBinarySubstrings(string s) {
        vector<int> arr;
        char last = s[0];
        int cnt = 1;
        for(int i=1; i<s.size(); i++){
            if(s[i]!=last){	// 如果不相等，保存结果并更换last字符
                arr.push_back(cnt);
                last = s[i];
                cnt=0;
            }
            cnt++;
        }
        arr.push_back(cnt);
        
        int ans = 0;
        for(int i=1; i<arr.size(); i++){
            ans += min(arr[i-1], arr[i]);
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(n)

## 解法二：空间优化

可以发现，子串个数只与`arr`中相邻的两个数值相关，因此可以进一步优化空间复杂度【用两个变量记录前后出现的次数】。代码如下：

```c++
class Solution {
public:
    int countBinarySubstrings(string s) {
        int i = 0, last = 0, now = 0, ans = 0;
        while(i<s.size()){
            char c = s[i];
            while(i<s.size() && s[i]==c){
                now++;
                i++;
            }
            ans += min(last, now);
            last = now;
            now = 0;
        }
        return ans;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)