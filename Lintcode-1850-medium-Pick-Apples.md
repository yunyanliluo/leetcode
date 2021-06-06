# 捡苹果

## 隔板法+滑窗（等距同向双指针）

注意：return sum_max == 0 ? -1 : sum_max;

```C++
class Solution {
public:
    /**
     * @param A: a list of integer
     * @param K: a integer
     * @param L: a integer
     * @return: return the maximum number of apples that they can collect.
     */
    int PickApples(vector<int> &A, int K, int L) {
        int n = A.size();
        vector<int> prefix(n + 1, 0);
        for (int i = 0; i < n; ++i) {
            prefix[i + 1] = prefix[i] + A[i];
        }
        // 隔板法
        // 先K后L L取i+1～i+L, K取前面所有可能中的max
        int cost_left_K = 0, sum_max = 0;
        for (int i = K - 1; i + L < n; ++i) {
            cost_left_K = max(cost_left_K, prefix[i + 1] - prefix[i - K + 1]);
            sum_max = max(sum_max, cost_left_K + prefix[i + L + 1] - prefix[i + 1]);
        }
        // 先L后K 同理
        int cost_left_L = 0;
        for (int i = L - 1; i + K < n; ++i) {
            cost_left_L = max(cost_left_L, prefix[i + 1] - prefix[i - L + 1]);
            sum_max = max(sum_max, cost_left_L + prefix[i + K + 1] - prefix[i + 1]);
        }
        return sum_max == 0 ? -1 : sum_max;
    }
};
```
41 ms时间消耗
·
5.30 MB空间消耗
·
您的提交打败了93.85 %的提交

## 更直观的隔板法
```C++
class Solution {
public:
    /**
     * @param A: a list of integer
     * @param K: a integer
     * @param L: a integer
     * @return: return the maximum number of apples that they can collect.
     */
    int findMax(int left, int right, int k, vector<int> &A) {
        if (right - left + 1 < k) return -1;

        // ...
    }
    int PickApples(vector<int> &A, int K, int L) {
        for(int i = 0; i < A.size(); ++i) {
            int max_left_K = findMax(0, i, K, A);
            int max_left_L = findMax(0, i, L, A);
            int max_right_K = findMax(i + 1, A.size() - 1, K, A);
            int max_right_L = findMax(i + 1, A.size() - 1, L, A);

            int sum_max = 0;
            if (max_left_K != -1 && max_right_L != -1) {
                sum_max = max(sum_max + max_left_K + max_right_L);
            }
            if (max_left_L != -1 && max_right_K != -1) {
                sum_max = max(sum_max + max_left_L + max_right_K);
            }
        }
        return sum_max == 0 ? -1 : sum_max;
    }
};
```
