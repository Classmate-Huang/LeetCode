# LeetCode205：同构字符串

## 题目

给定两个字符串 ***s*** 和 ***t\***，判断它们是否是同构的。

如果 ***s*** 中的字符可以按某种映射关系替换得到 ***t\*** ，那么这两个字符串是同构的。

每个出现的字符都应当映射到另一个字符，同时不改变字符的顺序。不同字符不能映射到同一个字符上，相同字符只能映射到同一个字符上，字符可以映射到自己本身。

 

**示例 1:**

```
输入：s = "egg", t = "add"
输出：true
```

**示例 2：**

```
输入：s = "foo", t = "bar"
输出：false
```

**示例 3：**

```
输入：s = "paper", t = "title"
输出：true
```

 

**提示：**

- 可以假设 ***s*** 和 **t**长度相同。



## 解法一：统一编码

将s串和t串统一编码成数字串：将每个字符出现的顺序视为它的编码序号，比如`abb`就编码为`011`，`egg`编码为`011`。最后比较两者编码后的结果是否一致即可。代码如下：

```c++
class Solution {
public:
    string convertString(string s){
        string c_s;
        unordered_map<char, int> m;
        int idx = 0;
        for(char& c:s){
            if(m.find(c)==m.end())  m[c] = idx++;
            c_s = c_s + to_string(idx);
        }
        return c_s;
    }

    bool isIsomorphic(string s, string t) {
        if(convertString(s)==convertString(t))
            return true;
        return false;
    }
};
```

时间复杂度：O(n)

空间复杂度：O(k)，字符串中不同字符的种类

## 解法二：相互编码

除了统一编码，也可以相互编码：当字符s[i] / t[j]第一次出现时，给他们确定好对应关系，如果是同构字符串，那么后续在遍历到相应字符时也应该满足这个对应关系。代码如下：

```c++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        unordered_map<char, char> s2t, t2s;
        for(int i=0; i<s.size(); i++){
            char x = s[i], y = t[i];
            // 均为第一次出现，确定对应关系
            if(s2t.find(x)==s2t.end() && t2s.find(y)==t2s.end()){
                s2t[x] = y; t2s[y] = x;
            }
            // 对应关系不满足，则说明不是同构字符串
            if(s2t[x]!=y || t2s[y]!=x) 
                return false;
        }
        return true;
    }
};
```



