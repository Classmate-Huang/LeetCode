# LeetCode1349：参加考试的最大学生数

## 题目

给你一个 `m * n` 的矩阵 `seats` 表示教室中的座位分布。如果座位是坏的（不可用），就用 `'#'` 表示；否则，用 `'.'` 表示。

学生可以看到左侧、右侧、左上、右上这四个方向上紧邻他的学生的答卷，但是看不到直接坐在他前面或者后面的学生的答卷。请你计算并返回该考场可以容纳的一起参加考试且无法作弊的最大学生人数。

学生必须坐在状况良好的座位上。

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/02/09/image.png)

```
输入：seats = [["#",".","#","#",".","#"],
              [".","#","#","#","#","."],
              ["#",".","#","#",".","#"]]
输出：4
解释：教师可以让 4 个学生坐在可用的座位上，这样他们就无法在考试中作弊。 
```

**示例 2：**

```
输入：seats = [[".","#"],
              ["#","#"],
              ["#","."],
              ["#","#"],
              [".","#"]]
输出：3
解释：让所有学生坐在可用的座位上。
```

**示例 3：**

```
输入：seats = [["#",".",".",".","#"],
              [".","#",".","#","."],
              [".",".","#",".","."],
              [".","#",".","#","."],
              ["#",".",".",".","#"]]
输出：10
解释：让学生坐在第 1、3 和 5 列的可用座位上。
```

 

**提示：**

- `seats` 只包含字符 `'.' 和``'#'`
- `m == seats.length`
- `n == seats[i].length`
- `1 <= m <= 8`
- `1 <= n <= 8`

## 解法一：状态压缩dp

这个题目不太好直接上手操作，因为不好定义状态。

我们抽象一个子问题来分析，对于第i排位置，其能坐多少人取决于三个条件：① 左右两边不能坐人；② 必须是好位置；③ 第i-1排状态的影响（不能在左上/右上）。

其次，对于这一层中每个元素的状态只有两种情况：坐人或者不坐人，很容易想到状态压缩的解法（用二进制表示某个位置上是否坐人）。因此用`f[i][mask]`来表示**第i排**在**状态mask**下最多能坐下多少个人（只考虑前i排位置），不难写出状态转移方程：

$$f[i][mask]=\max(f[i][mask], f[i-1][lastmask]+findOne(mask))$$，其中`f[i-1][lastmask]`表示上一排状态为lastmask时所坐的人数，`findOne`是一个求得当前mask状态1个数的函数(统计人数)。

并且利用位运算可以很快地判断三个条件：对于①，使用`mask&(mask>>1)`和`mask&(mask<<1)`来判断；对于②，使用`mask&badSeat[i]`来判断（badseat中存储每一排的座位状态，好座位为0，坏座位为1）；对于③，使用`mask&(lastmask>>1)`和`mask&(lastmask<<1)`来判断。

最后不难写出整体代码，如下所示：

```c++
class Solution {
public:
    int findOne(int n){
        int ans = 0;
        while(n){
            n = n&(n-1);
            ans++;
        }
        return ans;
    }

    int maxStudents(vector<vector<char>>& seats) {
        int m = seats.size(), n = seats[0].size();
        vector<vector<int>> f(m, vector<int>(1<<n, -1));
        
        // 记录座位好坏 (1为坏座位)
        vector<int> badSeats(m, 0);
        for(int i=0; i<m; i++){
            int bit = 0;
            for(int j=0; j<n; j++)  if(seats[i][j]=='#')    bit = bit | (1<<j);
            badSeats[i] = bit;
        }

        // 状态压缩dp
        for(int i=0; i<m; i++){
            for(int mask=0; mask<(1<<n); mask++){
                // 判断座位两边有没有人 and 座位都是可坐人的
                if( (mask&(mask<<1)) != 0 || (mask&(mask>>1)) != 0 || (mask&(badSeats[i])) != 0 ) continue;
                // 判断与上一排座位有没有交叉
                if(i!=0){
                    for(int lastmask=0; lastmask<(1<<n); lastmask++){
                        if(f[i-1][lastmask]==-1)    continue;   // 上一层此状态不可能出现
                        if((mask&(lastmask<<1))!=0 || (mask&(lastmask>>1))!=0)  continue;   // 出现交叉(左上 右上)
                        f[i][mask] = max(f[i][mask], f[i-1][lastmask] + findOne(mask));
                    }
                }
                else{ // 第一排座位不需要判断
                    f[i][mask] = max(f[i][mask], findOne(mask));
                }
            }
        }

        int ans = 0;
        for(auto& i:f[m-1]) ans = max(ans, i);
        return ans;
    }
};
```

时间复杂度：O($M*2^{2N}$)

空间复杂度：O($M*2^N$)

> 空间上由于每排座位只依赖上一排座位的状态，可以进行压缩