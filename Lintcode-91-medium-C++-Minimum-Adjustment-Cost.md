# 最小调整代价

## dp
枚举 1-100

"你可以假设数组中每个整数都是正整数，且小于等于100。"

```C++
class Solution {
public:
    /*
     * @param A: An integer array
     * @param target: An integer
     * @return: An integer
     */
    int MinAdjustmentCost(vector<int> &A, int target) {
        // 20:02 - 20:38 debugged AC
        if (A.size() <= 1) return 0;
        int minimum = A[0], maximum = A[0];
        for (auto a : A) {
            if (a < minimum) {
                minimum = a;
            } else if (a > maximum) {
                maximum = a;
            }
        }
        int len = maximum - minimum + 1;
        int num = A.size();
        vector<int> temp(maximum + 1, 0);
        vector<vector<int>> dp(num, temp);

        int res;
        for (int j = minimum; j <= maximum; ++j) {
            dp[0][j] = abs(j - A[0]);
        }
        for (int i = 1; i < num; ++i) {
            for (int j = minimum; j <= maximum; ++j) {
                int basecost = abs(j - A[i]);
                int mincost = INT_MAX;
                int min_k = max(minimum, j - target), max_k = min(maximum, j + target);
                for (int k = min_k; k <= max_k; ++k) {
                    mincost = min(mincost, basecost + dp[i-1][k]);
                }
                dp[i][j] = mincost;
            }
        }
        for (int j = minimum; j <= maximum; ++j) {
            res = min(res, dp[num - 1][j]);
        }
        return res;
    }
};
```
365 ms时间消耗
·
5.60 MB空间消耗
·
您的提交打败了51.60 %的提交
