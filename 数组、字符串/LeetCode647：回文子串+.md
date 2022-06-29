# LeetCode647：回文子串

## 题目

给你一个字符串 `s` ，请你统计并返回这个字符串中 **回文子串** 的数目。

**回文字符串** 是正着读和倒过来读一样的字符串。

**子字符串** 是字符串中的由连续字符组成的一个序列。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

 

**示例 1：**

```
输入：s = "abc"
输出：3
解释：三个回文子串: "a", "b", "c"
```

**示例 2：**

```
输入：s = "aaa"
输出：6
解释：6个回文子串: "a", "a", "a", "aa", "aa", "aaa"
```

 

**提示：**

- `1 <= s.length <= 1000`
- `s` 由小写英文字母组成



## 解法一：动态规划

`f[i][j]`用于表示子串`s[i:j]`是否为回文字符串，状态转移方程为：`s[i]==s[j] && (j-i<2 || f[i+1][j-1])`. 如果为单个字符或者两个相等的字符则`f[i][j]==1`。当子串大于2个时，需要判断`s[i+1:j-1]`是否为回文串，如果为回文串也为`f[i][j]==1`。代码如下：

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size(), ans = 0;
        vector<vector<int>> f(n+1, vector<int>(n+1, 0));
        
        for(int j=0; j<n; j++){            
            for(int i=0; i<=j; i++){
                if(s[i]==s[j] && (j-i<2 || f[i+1][j-1]==1)){
                    f[i][j] = 1;
                    ans++;
                }
            }
        }

        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O($n^2$)

## 解法二：中心扩展

从另一个角度出发，只需要从某个中心利用双指针向两边扩展即可，只要`s[left]==s[right]`，就`left--;right++`。

但需要注意的是，这个中心不只是单个字符【奇数长度的回文串】，还可能是双个字符【偶数长度的回文串】，比如`baab`就可以以`aa`为中心向两侧扩展。

在具体实现时，可以使用两次遍历，分布遍历奇数和偶数两种情况。代码如下：

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size(), ans = 0, left, right;
        for(int i=0; i<n; i++){
            // 奇数回文串
            left = right = i;
            while(left>=0 && right<n && s[left]==s[right]){
                ans++;
                left--; right++;
            }
            // 偶数回文串
            left = i; right = i+1;
            while(left>=0 && right<n && s[left]==s[right]){
                ans++;
                left--; right++;
            }
        }

        return ans;
    }
};
```

但也可以直接融入到一次遍历过程中，代码如下：

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size(), ans = 0, left, right;
        for(int i=0; i<2*n-1; i++){
            // 奇数回文串
            left = i/2;
            right = (i+1)/2;
            while(left>=0 && right<n && s[left]==s[right]){
                ans++;
                left--; right++;
            }
        }

        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(1)