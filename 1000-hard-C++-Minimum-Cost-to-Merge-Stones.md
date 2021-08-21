# 合并石头的最低成本 合并石子堆 k堆

## 区间型DP 枚举隔板 

方法1 札法 二维dp（通过固定隔板右侧永远只有一堆，来取消堆数这一维放在dp维度里的必要性）

```Java
class Solution {
    public int mergeStones(int[] stones, int K) {
        if (K == 0)
            return 0;
        int n = stones.length;
        if ((n - 1) % (K - 1) != 0)
            return -1;
        // compute prefix sum for later use
        int[] prefixSum = new int[n+1];
        for (int i = 1; i <= n; ++i) {
            prefixSum[i] = stones[i-1] + prefixSum[i-1];
        }
        // do[i][j] represents the minimal cost after merging range i ~ j as many
        // steps as possible (until we cannot merge further). Therefore for span i~j,
        // after merging, it will remains (j-i) % (K-1) + 1 piles. For example,
        // when we merge [..., 3, 4, 5, 6, ...] from 2 to 5 with K=3, we will leave
        // 2 piles [12, 6] with cost 12.
        int[][] dp = new int[stones.length][stones.length];
        for (int r = 0; r < n; ++r) {
            for (int l = r - K + 1; l >= 0; --l) {
                int max = Integer.MAX_VALUE;
                for (int p = r; p > l; p -= K - 1) {
                    // We can consider merging a larger range as merging the left
                    // part as many as possible, and merging right part to 1 (and
                    // the length of right part should have limitation), and add
                    // the two parts cost, and check if we could merge the collection
                    // remainig iterms of two parts further to 1 item.
                    //
                    // Here we let the right part p ~ r to merge to 1 pile and
                    // let l ~ p-1 to merge as many as possible. In this way, we
                    // merge l ~ r with pivot p (remaining 1 pile + piles left remained)
                    // (in most case, we have done merge l ~ r, except if l ~ p-1
                    // could merge to K-1 piles, if so, we need to do an extra merge)
                    max = Math.min(max, dp[l][p-1] + dp[p][r]);
                    dp[l][r] = max;
                }
                // That's when we do extra merge if we could continue to merge
                // This merge will cost the sum of all stones in that range
                if ((r-l) % (K-1) == 0)
                    dp[l][r] += prefixSum[r+1] - prefixSum[l];
            }
        }
        return dp[0][n-1];
    }
}
```

```C++
class Solution {
public:
    /**
     * @param stones: 
     * @param K: 
     * @return: return a integer 
     */
    int getPilesNum(int originalPilesNum, int K) {
        int res = originalPilesNum;
        if (res > K - 1) {
            res %= (K - 1);
        }
        res = res == 0 ? (K - 1) : res;
        return res;
    }
    int mergeStones(vector<int> &stones, int K) {
        // 20:40 start coding
        // dp[i][j], i=0~N-1
        int N = stones.size();
        if ((N-1) % (K-1) != 0) return -1;
        vector<int> pref(N + 1, 0);
        for (int i = 1; i <= N; ++i) {
            pref[i] = pref[i-1] + stones[i-1];
        }
        vector<int> temp(N, 0); // 这里初始值一定为0！因为<K的不能合并的区间就应该是0
        vector<vector<int>> dp(N, temp);
        for (int i = 0; i < N; ++i) {
            dp[i][i] = 0;
        }
        for (int r = 0; r < N; ++r) {
            for (int l = r - K + 1; l >= 0; --l) { //l跳过的部分，区间长度<K，不能合并，应该就是初始值0，不用再计算了
                int max = INT_MAX;
                for (int p = l; p < r; p += (K-1)) {
                    max = min(max, dp[l][p] + dp[p+1][r]);
                }
                dp[l][r] = max;
                if ((l-r)%(K-1)==0) {
                    dp[l][r] += pref[r+1]-pref[l];
                }
            }
        }
        return dp[0][N-1];
    }
};
```

## 网课法 三维DP 增加堆数一维
```JAVA
public class Solution {
    /**
     * @param stones: 
     * @param K: 
     * @return: return a integer 
     */
    public int mergeStones(int[] stones, int K) {
        int n = stones.length;
        
        if ((n - 1) % (K - 1) != 0) {
            return -1;
        }
        
        // preprocessing
        int[] prefixSum = getPrefixSum(stones);
        
        // state: dp[i][j][k] i到j这一段合并为 k 堆石子的最小代价
        int[][][] dp = new int[n][n][K + 1];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                for (int k = 0; k <= K; k++) {
                    dp[i][j][k] = Integer.MAX_VALUE / 4;
                }
            }
        }
        
        // initialization: 一开始所有石子各自为一堆，代价为 0
        for (int i = 0; i < n; i++) {
            dp[i][i][1] = 0;
        }
        
        // function:
        // dp[i][j][k] = min{dp[i][x][k - 1] + dp[x + 1][j][1]} // for k > 1
        // dp[i][j][1] = dp[i][j][K] + sum(stones[i..j]) // for k = 1
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                for (int k = 2; k <= K; k++) {
                    for (int x = i; x < j; x++) {
                        dp[i][j][k] = Math.min(dp[i][j][k], dp[i][x][k - 1] + dp[x + 1][j][1]);
                    }
                }
                dp[i][j][1] = dp[i][j][K] + prefixSum[j + 1] - prefixSum[i];
            }
        }
        
        // answer: dp[0][n - 1][1]
        if (dp[0][n - 1][1] == Integer.MAX_VALUE) {
            return -1;
        }
        return dp[0][n - 1][1];
        
    }
    private int[] getPrefixSum(int[] A) {
        int[] prefixSum = new int[A.length + 1];
        for (int i = 1; i <= A.length; i++) {
            prefixSum[i] = prefixSum[i - 1] + A[i - 1];
        }
        return prefixSum;
    }
}
```

下面的C++我不知道为什么WA：
```C++
class Solution {
public:
    /**
     * @param stones: 
     * @param K: 
     * @return: return a integer 
     */
    int mergeStones(vector<int> &stones, int K) {
        // 20:28 start coding
        // dp[i][j][k],i,j=0~100
        // dp[i][i][1] = 0
        // dp[0][N-1][1]
        // dp[i][j][1] = dp[i][j][K] + sum[i][j]
        // dp[i][j][t] = min(dp[i][x][1]+dp[x+1][j][t-1]), x=i~j-t+1
        int N = stones.size();
        if ((N - 1) % (K - 1) != 0) {
            return -1;
        }
        vector<int> temp2(K+1, INT_MAX);
        vector<vector<int>> temp1(N+1, temp2);
        vector<vector<vector<int>>> dp(N+1, temp1);
        for (int i = 0; i < N; ++i) {
            dp[i][i][1] = 0;
        }
        vector<int> prefixSum(N+1, 0);
        for (int i = 1; i <= N; ++i) {
            prefixSum[i] = prefixSum[i-1] + stones[i - 1];
        }
        for (int i = N-2; i >= 0; --i) {
            for (int j = i + 1; j < N; ++j) {
                for (int k = 2; k <= K; ++k) {
                    for (int x = i; x <= j - k + 1; ++x) {
                        dp[i][j][k] = min(dp[i][j][k], dp[i][x][1] + dp[x+1][j][k-1]);
                    }
                }
                dp[i][j][1] = dp[i][j][K] + prefixSum[j+1] - prefixSum[i];
            }
        }
        int res = dp[0][N-1][1];
        return res == INT_MAX ? -1 : res;
    }
};
```
