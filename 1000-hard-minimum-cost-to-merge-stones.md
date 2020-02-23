# 合并石头的最低成本

## DP
依据炸炸思路: https://leetcode.com/problems/minimum-cost-to-merge-stones/discuss/433588/java-dp-beats-97-with-detailed-explanation/452465


将美国论坛的 @bobchou 的Java版本翻译成了C++版本，Java版本原始题解：https://leetcode.com/problems/minimum-cost-to-merge-stones/discuss/433588/java-dp-beats-97-with-detailed-explanation/452465


总体思路与戳气球题目（hot100，https://leetcode-cn.com/problems/burst-balloons/）类似。


dp [i] [j]表示将范围为第i〜j堆的石头经过尽可能多的步骤合并后的最小成本（直到我们无法进一步合并）。因此，对于第i〜j堆的石头，合并后将保留（j-i）％（K-1）+ 1 个堆。例如，当我们将[...，3、4、5、6，...]从第2堆合并到第5堆，且K = 3时，我们将留下2个堆[12，6]，其成本为12。


我们考虑合并更大范围的石头堆时，策略是将总的范围分成左、右两部分，尽可能多地合并左侧部分（直到我们无法进一步合并），并且将右侧部分合并为1个堆（右侧部分的长度应有限制，即右侧部分的长度是K-1的整数倍+1），然后将两个部分的成本相加，最后检查是否可以合并两个部分目前剩余的石头堆，如果可以合并再把这一合并成本加进来。两个部分剩余石头堆能够合并的条件是有限的，即（r-l）是（K-1）的整数倍。
                    

具体而言，我们让右侧的部分p〜r合并为1堆，并让左侧的l〜p-1尽可能多地合并。这样，我们以p为pivot将l〜r合并（合并后右侧剩余1个堆，左侧剩余的堆数不超过K-1）。在大多数情况下，我们已经完成了l〜r的合并。除非当l〜p-1可以合并为K-1个堆，此时，我们需要进行额外的合并，也就是将左侧K-1堆与右侧1堆合并。
                    

上面提到额外的合并，将花费l～r范围内所有石头的总和。


给出代码及代码注释。

C++ code

```
class Solution {
public:
    int mergeStones(vector<int>& stones, int K) {
        int n=stones.size();
        if(n<=1) return 0;
        if((n-1)%(K-1)) return -1;
        //计算前缀总和以备后用
        //prefixSum[i]代表第0～i-1堆的石头数目之和。
        int prefixSum[n+1]={0}; 
        for(int i=1;i<=n;++i) prefixSum[i]=prefixSum[i-1]+stones[i-1]; 
        //dp[i][j]代表从第i堆到第j堆合并的成本
        int dp[n][n]; 
        for(int i=0;i<n;++i)
        for(int j=0;j<n;++j)
            dp[i][j]=0;
        for(int r=0;r<n;++r) {
            for(int l=r-K+1;l>=0;--l) { //l>r-K+1的这部分由于不足K堆无法合并，可以跳过不考虑
                int minNum=INT_MAX;
                //以p为pivot，将l～r分成两部分：l～p-1，p～r。
                //左侧部分l～p-1不一定能够合并成一堆，这里p需要保证的是右侧部分p～r一定可以合并成一堆。
                //将两部分l～p-1，p～r预合并，暂不加上两部分合并的成本，下面单独判断两部分的合并条件。
                for(int p=r;p>l;p-=(K-1)) { 
                    minNum=min(minNum, dp[l][p-1]+dp[p][r]); 
                }
                dp[l][r]=minNum; 

                //l～r不一定满足可以合并的关系：（r-l）是（K-1）的整数倍。
                //所以需要判断每当满足这一条件则合并两部分，加上合并成本；否则暂不合并，避免合并成本的重复计算。
                if((r-l)%(K-1)==0) dp[l][r]+=(prefixSum[r+1]-prefixSum[l]); 
            }
        }
        return dp[0][n-1];
    }
};
```

执行用时 :
4 ms
, 在所有 C++ 提交中击败了
94.38%
的用户

内存消耗 :
10.5 MB
, 在所有 C++ 提交中击败了
32.26%
的用户

时间复杂度：O(n^3), 以p为pivot合并l～r堆时的r-l-p三重循环。

空间复杂度：O(n^2), dp[stones.size()][stones.size()]所需空间。
