# Best Time to Buy and Sell Stock III

买卖股票的最佳时机 III

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。


示例 1:

输入: [3,3,5,0,0,3,1,4]

输出: 6

解释: 在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。


示例 2:

输入: [1,2,3,4,5]

输出: 4

解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。


示例 3:

输入: [7,6,4,3,1] 

输出: 0 

解释: 在这个情况下, 没有交易完成, 所以最大利润为 0。

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

# my answer
## dp
这道题是一道hard，但是和股票系列的其它问题相比，只是多了需要考虑k的因素：k是最多交易的次数，k=2.

因此在进行动态规划的时候，需要考虑k，状态方程里k可以为1或2.

状态转移方程为

dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])

dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])

其中k=1或2

因此可以将k的不同取值穷举出来，代码如下：

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        // hold[i][k] represents has done k transactions, done means sold out
        int n = prices.size();
        if (n <= 1) return 0;
        vector<int> temp(3, INT_MIN);
        vector<vector<int>> hold(n, temp);
        vector<vector<int>> sold(n, temp);
        hold[0][0] = 0 - prices[0];
        sold[0][0] = 0;
        for (int i = 1; i < n; ++i) {
            for (int k = 0; k < 3; ++k) {
                // hold[i][k] = max(hold[i - 1][k], sold[i - 1][k] - prices[i]);
                if (hold[i - 1][k] != INT_MIN)
                    hold[i][k] = hold[i - 1][k];
                if (sold[i - 1][k] != INT_MIN)
                    hold[i][k] = max(hold[i][k], sold[i - 1][k] - prices[i]);
                
                // sold[i][k] = max(sold[i - 1][k], hold[i - 1][k - 1] + prices[i]);
                if (sold[i - 1][k] != INT_MIN)
                    sold[i][k] = sold[i - 1][k];
                if (k > 0 && hold[i - 1][k - 1] != INT_MIN)
                    sold[i][k] = max(sold[i][k], hold[i - 1][k - 1] + prices[i]);
            }
        }
        int maxprofit = max(max(sold[n - 1][0], sold[n - 1][1]), sold[n - 1][2]);
        return maxprofit;
    }
};
```
执行用时：
752 ms
, 在所有 C++ 提交中击败了
12.04%
的用户

内存消耗：
167.1 MB
, 在所有 C++ 提交中击败了
12.25%
的用户

可简化如下：
```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n=prices.size();
        if(n<2) return 0;
        int dp_i10=0,dp_i20=0,dp_i11=INT_MIN,dp_i21=INT_MIN;
        for(int i=0;i<n;++i)
        {
            dp_i20=max(dp_i20,dp_i21+prices[i]);
            dp_i21=max(dp_i21,dp_i10-prices[i]);
            dp_i10=max(dp_i10,dp_i11+prices[i]);
            dp_i11=max(dp_i11,-prices[i]);
        }
        return dp_i20;
    }
};
```
执行用时 :8 ms, 在所有 C++ 提交中击败了91.72%的用户

内存消耗 :9.5 MB, 在所有 C++ 提交中击败了67.68%的用户

思路详解见股票问题带手续费的那道714题的附录的题解，
