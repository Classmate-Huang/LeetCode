# LeetCode46：全排列

## 题目

给定一个不含重复数字的数组 `nums` ，返回其 **所有可能的全排列** 。你可以 **按任意顺序** 返回答案。

 

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```
输入：nums = [1]
输出：[[1]]
```

 

**提示：**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数 **互不相同**



## 解法一：回溯

这个题目可以说是回溯法的入门题，首先问题本身即是搜索问题，给定一个数组，我们要求得其全排列，按照人的思维。我们不停地交换数组元素即可，比如将第1个位置与第2,3,...n个位置互换，第2个位置与第3,...,n个位置互换，递归进行即可求解。

但需要注意的是，为了不重复得到同一种序列，我们通常会按照某种策略，再交换位置后还原状态。例如[1,2,3]，我先记录[1,2,3]，然后交换1和2，记录[2,1,3]，然后还原回[1,2,3]，重新交换1和3，记录[3,2,1]等等。

代入到回溯的算法模板中去，代码如下：

```c++
class Solution {
public:
    void backtracking(vector<int>& nums, int level, vector<vector<int>>& ans){
        // 已交换到最后一位元素，满足要求
        if(level==nums.size()-1){
            ans.push_back(nums);
            return ;
        }
		// DFS
        for(int i=level; i<nums.size(); i++){
            swap(nums[i], nums[level]);
            backtracking(nums, level+1, ans);
            swap(nums[i], nums[level]);
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        backtracking(nums, 0, ans);	// 回溯
        return ans;
    }
};
```

时间复杂度：O(n!)

空间复杂度：O(1)，没有浪费额外空间



## 解法一：回溯2

上一种解法得不到顺序解，采用如下做法，使用一个额外的flag数组指示当前nums元素中是否可选。即可得到字典序最小的结果，代码如下：

```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<bool> flag;
    vector<int> res;

    void findAns(vector<int>& nums, int idx){
        // 找到最终条件
        if(idx == nums.size()){
            ans.push_back(res);
            return;
        }
        // 递归查找
        for(int i=0; i<nums.size(); i++){
            if(flag[i]){
                flag[i] = false;
                res.push_back(nums[i]);
                findAns(nums, idx+1);
                res.pop_back();
                flag[i] = true;
            }
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        
        int n = nums.size();
        flag = vector<bool> (n, true);
        findAns(nums, 0);

        return ans;
    }
};
```

时间复杂度：O(n!)

空间复杂度：O(1)