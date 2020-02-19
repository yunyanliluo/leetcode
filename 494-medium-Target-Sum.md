# 目标和
示例 1:

输入: nums: [1, 1, 1, 1, 1], S: 3

输出: 5

解释: 

-1+1+1+1+1 = 3

+1-1+1+1+1 = 3

+1+1-1+1+1 = 3

+1+1+1-1+1 = 3

+1+1+1+1-1 = 3

一共有5种方法让最终目标和为3。

注意:

数组非空，且长度不会超过20。

初始的数组的和不会超过1000。

保证返回的最终结果能被32位整数存下。

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/target-sum

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 回溯（dfs）
最先想到的解法是回溯。

由于数组最长20，最大2^20种结果，所以不会超时。

注意有一个用例S接近int上界，所以target是long。

时间O2^n，空间On（dfs的递归空间，是从根到树叶On）

C++ code
```
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int S) {
        long int res=0;
        BackTrack(nums, 0, S, res);
        return res;
    }
    void BackTrack(vector<int>& nums, int start, long int target, long int& res) {
        if(start==nums.size()) {
            if(target==0) ++res;
            return;
        }
        BackTrack(nums, start+1, target-nums[start], res);
        BackTrack(nums, start+1, target+nums[start], res);
        return;
    }
};
```
执行用时 :
1348 ms
, 在所有 C++ 提交中击败了
41.98%
的用户

内存消耗 :
8.7 MB
, 在所有 C++ 提交中击败了
31.41%
的用户

显而易见，这里有大量重复的运算，

因此用dp数组存储一下计算结果dp[nums.size()][S+1]，用空间换时间。

下面int[][]dp的传参数没AC

C++ code
```
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int S) {
        int[][] dp=new int[nums.size()][S];
        for(int i=0;i<nums.size();++i) {
            for(int j=0;j<S;++j) {
                dp[i][j]=-1;
            }
        }
        return BackTrack(nums, 0, S, dp);
    }
    int BackTrack(vector<int>& nums, int start, long int target, int[][] dp) {
        if(start==nums.size()) {
            if(target==0) return 1;
            return 0;
        }
        if(dp[start][target]>-1) return dp[start][target];
        int sum=0;
        sum+=BackTrack(nums, start+1, target-nums[start], dp);
        sum+=BackTrack(nums, start+1, target+nums[start], dp);
        dp[start][target]=sum;
        return sum;
    }
};
```

## DP - 0-1背包的解法
参考：https://leetcode-cn.com/problems/target-sum/solution/c-dfshe-01bei-bao-by-bao-bao-ke-guai-liao/

假设nums用N表示，

前+的数和前-的数分别用P，Q来表示，

那么SumP-SumQ=S

SumP=SumQ+S

SumP+SumP=SumP+SumQ+S

2SumP=SumN+S

而SumN+S可以计算得出。

因此转化为0-1背包问题，目标和为SumP=（SumN+S）/2。

0-1背包的解法非常经典，应该倒背如流。

时间O(nums.size()*S),空间OS

C++ code
```
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int S) {
        int sum=0;
        for(auto& num:nums) {
            sum+=num;
        }
        if(S>sum) return 0; //注意这里的处理，若sum<S可以判断无解
        long int target=S+sum; //注意S接近int上界，因此target是long
        if(target%2) return 0; //注意这里的处理，若target偶数，说明无解
        target/=2;
        long int dp[target+1]={0};
        dp[0]=1;
        for(auto& num:nums) {
            for(long int j=target;j>=num;--j) {
                dp[j]+=(dp[j-num]);
            }
        }
        return dp[target];
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
90.39%
的用户

内存消耗 :
8.4 MB
, 在所有 C++ 提交中击败了
78.98%
的用户

