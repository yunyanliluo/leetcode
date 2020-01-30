# 数组中第k大元素
返回数组排序之后的第k大元素（重复元素也得计数）

Input: [3,2,3,1,2,4,5,5,6] and k = 4

Output: 4

https://leetcode-cn.com/problems/kth-largest-element-in-an-array/

## 排序

C++ code
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        return nums[nums.size()-k];
    }
};
```
执行用时 :
12 ms
, 在所有 C++ 提交中击败了
82.97%
的用户

内存消耗 :
9.4 MB
, 在所有 C++ 提交中击败了
31.52%
的用户
