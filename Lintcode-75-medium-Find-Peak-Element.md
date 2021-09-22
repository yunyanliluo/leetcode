# 寻找峰值

## On 遍历一次

## Ologn 二分法
原理：mid, mid+1，若mid大，则start～mid一定有峰；若mid+1大，则mid+1～end一定有峰

```C++
class Solution {
public:
    /**
     * @param A: An integers array.
     * @return: return any of peek positions.
     */
    int findPeak(vector<int> &A) {
        // 21:36
        int n = A.size();
        int start = 0, end = n - 1;
        while (start < end) {
            int mid = start + (end - start) / 2;
            if (A[mid] < A[mid + 1]) {
                start = mid + 1;
            } else {
                end = mid;
            }
        }
        return start;
    }
};
```

