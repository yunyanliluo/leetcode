# 石子归并

## dp
从最小长度的ij开始做dp，因为长的ij肯定能从比他小的段计算出来

注意：如果用min，不要忘记初始值INT_MAX!!!

```C++
class Solution {
public:
    /**
     * @param A: An integer array
     * @return: An integer
     */
    int stoneGame(vector<int> &A) {
        if (A.size() <= 1) return 0;
        int n = A.size();
        vector<int> prefix(n + 1, 0);
        for (int i = 1; i <= n; ++i) {
            prefix[i] = prefix[i - 1] + A[i - 1];
        }
        // dp[i][j] 表示长度len，起始下标i，终止下标j.so len=j-i+1, len=1~n, i=0~n-1, j=0~n-1.
        vector<int> dptemp(n, INT_MAX);
        vector<vector<int>> dp(n, dptemp);
        for (int i = 0; i < n; ++i) {
            dp[i][i] = 0;
        }
        for (int len = 2; len <= n; ++len) {
            for (int i = 0; i < n - len + 1; ++i) {
                int j = len + i - 1;
                for (int k = i; k < j; ++k) {
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k+1][j] + prefix[j+1] - prefix[i]);
                }
            }
        }
        return dp[0][n - 1];
    }
};
```
