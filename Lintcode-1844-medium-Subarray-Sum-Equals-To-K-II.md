# 子数组和为K II

## 暴力
On^2找全部数组，On计算数组内元素和，一共On^3

## 前缀和优化
On^2找全部数组，O1计算数组内元素和，一共On^2

## 哈希表优化
sum = prefix_back - prefix_front

对于一个区间，固定了区间起始位置，prefix_front就固定了；能够通过区间元素和目标target，知道期待的prefix_back是什么；同理，知道prefix_back就能知道期待的prefix_front是什么；

固定prefix_back，这个时候可以将遍历prefix_front优化为哈希表查找预期的prefix_front是否存在，On -> O1

On找全部数组，O1计算数组内元素和，一共On

```C++
class Solution {
public:
    /**
     * @param nums: a list of integer
     * @param k: an integer
     * @return: return an integer, denote the minimum length of continuous subarrays whose sum equals to k
     */
    int subarraySumEqualsKII(vector<int> &nums, int k) {
        int n = nums.size();
        if (n == 0) return -1;
        int res = INT_MAX;
        unordered_map<int, int> prefixSum2rightIndex;
        prefixSum2rightIndex[0] = 0;
        // compute prefix sum
        vector<int> prefixSum(n + 1, 0);
        for (int i = 1; i <= n; ++i) {
            prefixSum[i] = prefixSum[i - 1] + nums[i - 1];
            int target = prefixSum[i] - k;
            if (prefixSum2rightIndex.find(target) != prefixSum2rightIndex.end()) {
                res = min(res, i - prefixSum2rightIndex[target]);
            }
            prefixSum2rightIndex[prefixSum[i]] = i;
        }
        return res == INT_MAX ? -1 : res;
    }
};
```
142 ms时间消耗
·
6.87 MB空间消耗
·
您的提交打败了61.01 %的提交
