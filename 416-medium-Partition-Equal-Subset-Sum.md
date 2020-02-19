# 分割等和子集
给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

注意:

每个数组中的元素不会超过 100

数组的大小不会超过 200

示例 1:

输入: [1, 5, 11, 5]

输出: true

解释: 数组可以分割成 [1, 5, 5] 和 [11].
 

示例 2:

输入: [1, 2, 3, 5]

输出: false

解释: 数组不能分割成两个元素和相等的子集.

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/partition-equal-subset-sum

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 0-1背包
计算总和，除以2，作为目标和

C++ code
```
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum=0;
        for(auto& num:nums) {
            sum+=num;
        }
        if(sum%2) return false;
        sum/=2;
        bool dp[sum+1]={0};
        dp[0]=1;
        for(auto& num:nums) {
            for(int j=sum;j>=num;--j) {
                if(!dp[j]) dp[j]=dp[j-num];
            }
            if(dp[sum]) return true;
        }
        return false;
    }
};
```
执行用时 :
36 ms
, 在所有 C++ 提交中击败了
85.44%
的用户

内存消耗 :
8.5 MB
, 在所有 C++ 提交中击败了
55.65%
的用户

## dfs+剪枝
参考：https://leetcode-cn.com/problems/partition-equal-subset-sum/solution/java-dfsjian-zhi-1ms-9931-by-lava-4/

由于输入规模不大，所以没有超时，可以参考。

值得参考点：

1.剪枝惯例先排序

2.排序后倒序查找（从大的开始），否则容易超时
