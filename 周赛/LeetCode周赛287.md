# LeetCode周赛287

## 题目一：[转化时间需要的最少操作数](https://leetcode.cn/problems/minimum-number-of-operations-to-convert-time/) (贪心)

直接模拟 + 贪心，每次先考虑减少最多的时间进行操作。

```c++
class Solution {
public:
    int convertNum(string s){   // 将时间转换成分钟
        int h = (s[0]-'0') * 10 + s[1] - '0';
        int m = (s[3]-'0') * 10 + s[4] - '0';
        return h*60 + m;
    }
    
    vector<int> opt = {60, 15, 5, 1};
    
    int convertTime(string current, string correct) {
        int dist = convertNum(correct) - convertNum(current);
        int ans = 0;
        
        for (auto t : opt) {
            ans += dist / t;
            dist -= (dist / t) * t;
        }
        
        return ans;
    }
};
```

## 题目二：[找出输掉零场或一场比赛的玩家](https://leetcode.cn/problems/find-players-with-zero-or-one-losses/) （统计）

统计每个玩家输出的次数即可，需要注意不是每个玩家都有参与，需要标识出参与游戏的玩家。

```c++
class Solution {
public:
    vector<vector<int>> findWinners(vector<vector<int>>& matches) {
        vector<int> f(1e5+1, -1);   // f[i]表示第i个人输掉了多少场比赛，-1表示没有参加
        for (auto& match : matches) {
            int winer = match[0], loser = match[1];
            if (f[winer] == -1)    f[winer] = 0;
            if(f[loser] == -1)    f[loser] = 0;
            f[loser]++;
        }
        
        vector<vector<int>> ans(2);
        for(int i=1; i<=1e5; i++){
            if (f[i] == 0) ans[0].push_back(i);
            else if (f[i] == 1) ans[1].push_back(i);
        }
        return ans;
    }
};
```

## 题目三：[每个小孩最多能分到多少糖果](https://leetcode.cn/problems/maximum-candies-allocated-to-k-children/) (二分)

如果给定糖果数量t，可以很快地判断是否满足条件，因此我们使用二分的策略枚举t即可。根据题目的提示，得到下限是0，上限是1e7，代码如下：

```c++
class Solution {
public:
    static bool cmp(int a, int b) {
        return a > b;
    }
    
    // "k个小朋友，每个小朋友分t个糖果"的方案是否可行
    bool isOK(vector<int>& candies, long long k, int t) {
        for (auto candy : candies) {
            if (candy >= t)  k -= candy / t;
            if (k <= 0) return true;
        }
        return false;
    }
    
    int maximumCandies(vector<int>& candies, long long k) {
        
        int l = 0, r = 1e7;	// 上限为1e7
        while (l < r) {	// 二分
            int mid = (r - l + 1) / 2 + l;
            if (isOK(candies, k, mid))  l = mid;
            else r = mid - 1;
        }
        
        return l;
    }
};
```

## 题目四：[加密解密字符串](https://leetcode.cn/problems/encrypt-and-decrypt-strings/)

首先编码比较好实现，直接模拟即可。对于解码，如果每次都进行回溯会导致超时。但我们发现可以先对`dictionary`中的字符串事先进行预处理，统计相同编码结果有多少种，在解码时直接返回结果即可。在具体实现时，要注意判断编码失败的情况，代码如下：

```c++
class Encrypter {
public:
    vector<char> keys;
    vector<string> values, dictionary;
    
    unordered_map<char, string> key_map;
    unordered_map<string, int> dict_set;
    
    Encrypter(vector<char>& keys, vector<string>& values, vector<string>& dictionary): keys(keys), values(values), dictionary(dictionary) {
        // key -> value 将编码字符直接对应起来
        for (int i = 0; i < keys.size(); i++) {
            key_map[keys[i]] = values[i];
        }
        // 预处理解码过程, 将dictionary中的字符统一解码，记录次数
        for (auto& s : dictionary) {
            string code = encrypt(s);
            if (code != "") dict_set[code] ++;  // 排除掉无法编码的情况
        }
        
    }
    
    string encrypt(string word1) {
        string ans = "";
        for (auto& i : word1) {
            if (key_map.find(i) != key_map.end())   ans += key_map[i];
            else    return "";  // 无法编码的情况
        }
        return ans;
    }
    
    int decrypt(string word2) {
        if (dict_set.find(word2) == dict_set.end())    return 0;
        return dict_set[word2];
    }
};
```

