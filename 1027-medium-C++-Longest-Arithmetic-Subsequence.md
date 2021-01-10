# solutions

```
class Solution {
public:
    int longestArithSeqLength(vector<int>& A) {
        int n = A.size();
        if (n <= 1) return n;
        unordered_map<int, unordered_map<int, int>> dp;
        int longest = 2;
        for (int i = 0; i < n; ++ i) {
            unordered_map<int, int> temp;
            if (i != 0) {
                for (int j = i - 1; j >= 0; -- j) {
                    int diff = A[i] - A[j];
                    if (dp[j].find(diff) == dp[j].end()) {
                        if (temp.find(diff) == temp.end())  temp[diff] = 2;  // 这里注意！假如31 35 31 39这种情况，39前面两个31的哈希表是不同的，遇到第二个31时如果diff找不到可能会将temp[diff]进行覆盖，但是39的哈希表中diff4的值应该取较大的一个
                    }
                    else {
                        temp[diff] = max(temp[diff], dp[j][diff] + 1);  // 这里注意！类似的，一定要max一下
                        longest = max(longest, temp[diff]);
                    }
                }
            }
            dp[i] = temp;
        }
        return longest;
    }
};
```
TLE

炸炸的java
```
class Solution {
    public int longestArithSeqLength(int[] A) {
        int[][] dp = new int[A.length][2001];
        int ans = 1;
        for (int i = 1; i < dp.length; ++i) {
            for (int j = 0; j < i; ++j) {
                int diff = A[j] - A[i] + 1000;
                dp[i][diff] = Math.max(dp[j][diff] + 1, dp[i][diff]);
                ans = Math.max(ans, dp[i][diff]);
            }
        }
        return ans + 1;
    }
}
```
击败百分之八十、七十
