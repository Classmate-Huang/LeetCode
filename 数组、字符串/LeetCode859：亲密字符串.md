# LeetCode859：[亲密字符串](https://leetcode-cn.com/problems/buddy-strings/description/)

## 题目

给你两个字符串 `s` 和 `goal` ，只要我们可以通过交换 `s` 中的两个字母得到与 `goal` 相等的结果，就返回 `true` ；否则返回 `false` 。

交换字母的定义是：取两个下标 `i` 和 `j` （下标从 `0` 开始）且满足 `i != j` ，接着交换 `s[i]` 和 `s[j]` 处的字符。

- 例如，在 `"abcd"` 中交换下标 `0` 和下标 `2` 的元素可以生成 `"cbad"` 。

 

**示例 1：**

```
输入：s = "ab", goal = "ba"
输出：true
解释：你可以交换 s[0] = 'a' 和 s[1] = 'b' 生成 "ba"，此时 s 和 goal 相等。
```

**示例 2：**

```
输入：s = "ab", goal = "ab"
输出：false
解释：你只能交换 s[0] = 'a' 和 s[1] = 'b' 生成 "ba"，此时 s 和 goal 不相等。
```

**示例 3：**

```
输入：s = "aa", goal = "aa"
输出：true
解释：你可以交换 s[0] = 'a' 和 s[1] = 'a' 生成 "aa"，此时 s 和 goal 相等。
```

**示例 4：**

```
输入：s = "aaaaaaabc", goal = "aaaaaaacb"
输出：true
```

 

**提示：**

- `1 <= s.length, goal.length <= 2 * 104`
- `s` 和 `goal` 由小写英文字母组成

## 解法一：模拟

主要就是利用到模拟的思想，分为几种情况：

① 两个数字完全匹配，需要判断是否满足交换两个字母也匹配【其实记录字母出现的频率，如果有两个以上相同的字母即可】；

② 不匹配次数不为2，直接返回false；

② 不匹配次数为2，判断交换后是否能够完成匹配。

代码如下：

```c++
class Solution {
public:
    bool buddyStrings(string s, string goal) {
        // 长度不相等直接返回false
        if(s.size() != goal.size()) return false;
        // 遍历
        int count = 0;  // 记录不匹配的次数
        vector<int> count_c(26, 0); // 记录各字母出现频率
        bool flag = false;
        char temp_s, temp_g;
        for(int i=0; i<s.size(); i++){
            // 更新频率， 并且如果存在两个字母以上则flag置为true
            count_c[s[i]-'a']++;
            if(count_c[s[i]-'a'] >= 2)    flag = true;

            if(s[i]!=goal[i]){
                // 第一次出现不匹配
                if(count==0){
                    temp_s = s[i];
                    temp_g = goal[i];
                    count++;
                }
                // 第二次出现，判断是否能交换成功
                else if(count==1){
                    if((temp_s != goal[i]) || (temp_g != s[i])) return false;
                    count++;
                }
                // 第三次出现直接报错
                else return false;
            }
        }
        // 没有出现过匹配错误的情况并且有存在出现频率大于2的字母 or 正好完全完成交换
        return ((count==0) & flag) || (count==2);
    }
};
```

时间复杂度：O(n)

空间复杂度：O(1)