# LeetCode149：直线上最多的点数

## 题目

给你一个数组 `points` ，其中 `points[i] = [xi, yi]` 表示 **X-Y** 平面上的一个点。求最多有多少个点在同一条直线上。

 

**示例 1：**

![img](LeetCode149：直线上最多的点数+.assets/plane1.jpg)

```
输入：points = [[1,1],[2,2],[3,3]]
输出：3
```

**示例 2：**

![img](LeetCode149：直线上最多的点数+.assets/plane2.jpg)

```
输入：points = [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
输出：4
```

 

**提示：**

- `1 <= points.length <= 300`
- `points[i].length == 2`
- `-104 <= xi, yi <= 104`
- `points` 中的所有点 **互不相同**



## 解法一：遍历 + 哈希表

以某个点`(x0, y0)`为参考，要判断其它的点是否在一条直线上，其实就是判断斜率`(yi-y0)/(xi-x0)`是否一致。如果不同的点与参考点计算出的斜率一致，那么可以确定它们处于同一直线上。因此遍历每个参考点，分别计算其它点与参考点的斜率，构造`hash表`，统计各斜率点的个数。每一轮次下来，记录最多的点数。

需要注意分母为0的情况，本解法采用一个额外的变量来存储分母为0的情况。

代码如下：

```c++
class Solution {
public:
    int maxPoints(vector<vector<int>>& points) {
        unordered_map<double, int> m;
        int n = points.size(), ans = 1;
        for(int i=0; i<n; i++){
            int x0 = points[i][0], y0 = points[i][1], count_same = 0;
            // 不考虑之前的参考点
            for(int j=i+1; j<n; j++){
                // 处理斜率，注意分母为0的情况
                if(points[j][0]-x0!=0){
                    double k = double(points[j][1] - y0) / double(points[j][0] - x0);
                    m[k]++;
                }
                else{
                    count_same++;
                }
            }
            // 更新最大值
            ans = max(ans, count_same+1);
            for(auto& [_, value]:m){
                ans = max(ans, value+1);
            }
            m.clear();
        }
        return ans;
    }
};
```

时间复杂度：O($n^2$)

空间复杂度：O(n)

## 解法二：解决浮点数精度问题

虽然解法一能够顺利通过测试样例，但采用的是`double`记录斜率，但这难免会遇到精度不足的情况。因此解法二**采用分式来表达斜率**，这里需要用到`gcd`来进行约分。

注意：利用取值范围<$-10^4,10^4$>，使得键值为`x*20001+y`。【也可以使用string来构建key，但存储空间较大，而一个int类型最大可存储$2\times 10^{10}$】。

```c++
class Solution {
public:
    int gcd(int a, int b){
        return a%b==0 ? b : gcd(b, a%b);
    }

    int maxPoints(vector<vector<int>>& points) {
        unordered_map<int, int> m;
        int n = points.size(), ans = 1;
        for(int i=0; i<n; i++){
            int x0 = points[i][0], y0 = points[i][1];
            for(int j=i+1; j<n; j++){
                int x = points[j][0] - x0, y = points[j][1] - y0;
                if(x==0){   // 斜率不存在
                    y = 1;
                }
                else if(y==0){  // 斜率为0
                    x = 1;
                }
                else{
                    // 保证负数都在分母x上【避免-1/2 和 1/-2的情况出现】
                    if(y<0){
                        x = -x; y = -y;
                    }
                    // 约分
                    int g = gcd(x, y);
                    x = x / g; y = y / g;
                }
                m[x*20001+y]++;
            }
            for(auto& [_, value]:m){
                ans = max(ans, value+1);
            }
            m.clear();
        }
        return ans;
    }
};
```

时间复杂度：O($n^2log(m)$)，额外的复杂度用于`gcd`计算

空间复杂度：O(n)