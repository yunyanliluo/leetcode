# 书籍复印II

## 二分法

```C++
class Solution {
public:
    /**
     * @param n: An integer
     * @param times: an array of integers
     * @return: an integer
     */
    int copyBooksII(int n, vector<int> &times) {
        // 二分法，判断条件比较的是len时间下可以复制的书籍总数
        int left = 1, right = times[0] * n;
        while (left < right) {
            int mid = left + (right - left) / 2;
            int booksum = 0;
            for (auto & time : times) {
                booksum += mid / time;
            }
            if (booksum < n) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
};
```
41 ms时间消耗
·
5.57 MB空间消耗
·
您的提交打败了99.60 %的提交
