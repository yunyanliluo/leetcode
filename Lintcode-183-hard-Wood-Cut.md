# 木材加工
字节后端三面真题

# 二分法

注意：开始时计算sum，叠加，可能会overflow，需要设置long int sum，同时一旦sum>=k就break，既提速又避免overflow

```C++
class Solution {
public:
    /**
     * @param L: Given n pieces of wood with length L[i]
     * @param k: An integer
     * @return: The maximum length of the small pieces
     */
    int woodCut(vector<int> &L, int k) {
        long int sum = 0;
        for (auto & len : L) {
            sum += len;
            if (sum >= k) break;
        }
        if (sum < k) return 0;
        
        // 排序
        sort(L.begin(), L.end());

        // 二分法，长度len=L[max(n-6, 0)]~L[n-1]
        int n = L.size();
        int left = n >= k ? L[n - k] : 1, right = L[n - 1];
        while (left < right) {
            int mid = left + (right - left + 1) / 2;
            int currsum = 0;
            for (auto & len : L) {
                currsum += len / mid;
            }
            if (currsum < k) {
                right = mid - 1;
            } else {
                left = mid;
            }
        }
        return left;
    }
};
```
