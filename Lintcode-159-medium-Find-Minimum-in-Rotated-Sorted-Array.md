# 寻找旋转数组的最小值

suppose a sorted array in ascending order is rotated at some pivot unknown to you

递增数组旋转之后寻找最小值

## 一次遍历On

找到一个元素满足nums[i] > nums[i+1]，则nums[i+1]是要返回的值

## 二分法Ologn

wa了很多次

不同于二分法模板

让mid和start或end相比较，不妨假设让mid和start相比较

那么首先需要排除mid==start的情况，也即需要保证end-start>1。（mid取下确界，mid可能等于start。mid取上确界，mid可能等于end。mid总会等于start/end中的一个）

当end-start<=1时，直接得出答案min(nums[start], nums[end])

下面让mid和start比较

由于数组不含重复元素，则nums[start]!=nums[mid]

1. nums[start]<nums[mid]

start~mid这段单调增，最小值如果在start～mid就一定是start，如果在mid+1～end则继续查找

2. nums[start]>nums[mid]

start~mid这段，一定含有答案，继续查找
```C++
class Solution {
public:
    /**
     * @param nums: a rotated sorted array
     * @return: the minimum number in the array
     */
    int _findMin(vector<int> &nums, int start, int end) {
        while (end - start > 1) { // because we need to compare start/end and min, in case start/end == mid
            int mid = start + (end - start) / 2;
            if (nums[mid] > nums[start]) {
                if (nums[start] < nums[end]) return nums[start];
                start = mid + 1;
            } else {
                end = mid;
            }
        }
        return min(nums[start], nums[end]);
    }
    int findMin(vector<int> &nums) {
        int n = nums.size();
        return _findMin(nums, 0, n - 1);
    }
};
```
