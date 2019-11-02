# *Search in Rotated Sorted Array*

## my answer

### 二分之后，左边和右边一定有一半是升序的，有一半是旋转的数组，递归就好

算法基于一个事实，数组从任意位置劈开后，至少有一半是有序的，

比如 [ 4 5 6 7 1 2 3] ，从 7 劈开，左边是 [ 4 5 6 7] 右边是 [ 7 1 2 3]，左边是有序的。

我们可以先分类讨论左/右哪一段是有序的 (只要判断端点即可)，然后顺着二分查找的思路判断 target 在不在有序的这一段里，把这一段或者另一段丢弃。

```C++ code
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if(nums.size()==0) return -1;
        return rotatedBinarySearch(nums, 0, nums.size()-1, target);
    }
    int rotatedBinarySearch(vector<int>& nums, int s, int e, int& target)
    {
        if(s<e)
        {
            int mid=s+(e-s)/2;
            if(nums[mid]>=nums[s])
            {
                if(target>=nums[s]&&target<=nums[mid]) return BinarySearch(nums, s, mid, target);
                else return rotatedBinarySearch(nums, mid+1, e, target);
            }
            else
            {
                if(target>=nums[mid]&&target<=nums[e]) return BinarySearch(nums, mid, e, target);
                else return rotatedBinarySearch(nums, s, mid-1, target);
            }
        }
        else if(s==e&&nums[s]==target) return s;
        else return -1;
    }
    int BinarySearch(vector<int>& nums, int s, int e, int& target)
    {
        while(s<e)
        {
            int mid=s+(e-s)/2;
            if(target>nums[mid])
            {
                s=mid+1;
            }
            else
            {
                e=mid;
            }
        }
        if(nums[s]==target)
            return s;
        return -1;
    }
};
```
Ologn, Ologn

执行用时 :8 ms, 在所有 cpp 提交中击败了57.44%的用户

内存消耗 :8.8 MB, 在所有 cpp 提交中击败了73.70%的用户

### improvements

不需要递归，迭代更省空间；同时，二分查找函数可以丢弃

```C++ code
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if(nums.size()==0) return -1;
        return rotatedBinarySearch(nums, 0, nums.size()-1, target);
    }
    int rotatedBinarySearch(vector<int>& nums, int s, int e, int& target)
    {
        while(s<e)
        {
            int mid=s+(e-s)/2;
            if(nums[mid]>=nums[s])
            {
                if(target>=nums[s]&&target<=nums[mid]) e=mid;
                else s=mid+1;
            }
            else
            {
                if(target>=nums[mid]&&target<=nums[e]) s=mid;
                else e=mid-1;
            }
        }
        if(s==e&&nums[s]==target) return s;
        else return -1;
    }
};
```
Ologn, O1

执行用时 :8 ms, 在所有 cpp 提交中击败了57.44%的用户

内存消耗 :8.6 MB, 在所有 cpp 提交中击败了92.88%的用户

## solutions

### 类似我的方法

有三个答案都是类似的方法

原文链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-35/





### 一种简洁的写法

原文链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/jian-ji-rong-yi-li-jie-java-er-fen-fa-by-breezean/

由于题目要求时间复杂度为 O(log n)，故实现为二分法查找，关于二分法，一般存在 low,high,mid位，来辅助判断。

如果 target 在[mid+1,high] 序列中，则low = mid+1,否则 high = mid,关键是如何判断 target在[mid+1,high]序列中,具体判断如下：

- 当[0, mid] 序列是升序: nums[0] <= nums[mid], 当target > nums[mid] || target < nums[0] ,则向后规约

- 当[0, mid] 序列存在旋转位: nums[0] > nums[mid],当target < nums[0] && target > nums[mid],则向后规约

- 当然其他其他情况就是向前规约了

循环判断，直到排除到只剩最后一个元素时，退出循环，如果该元素和 target 相同，直接返回下标，否则返回 -1。

C++ code from the link above

```C++ code 
class Solution {
    public int search(int[] nums, int target) {
        int lo = 0;
        int hi = nums.length - 1;

        while (lo < hi) {
            int mid = (lo + hi) / 2;
            // 当[0,mid]有序时,向后规约条件
            if (nums[0] <= nums[mid] && (target > nums[mid] || target < nums[0])) {
                lo = mid + 1;
                // 当[0,mid]发生旋转时，向后规约条件
            } else if (target > nums[mid] && target < nums[0]) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo == hi && nums[lo] == target ? lo : -1;
    }
}
```

### 改变target不落在的那半个区间的所有元素值为无穷

原文链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-35/

题目中的数组，其实是两段有序的数组。例如

[ 4 5 6 7 1 2 3 ] ，[ 4 5 6 7 ] 和 [ 1 2 3 ] 两段有序。

而对于 [ 1 2 3 4] 这种，可以看做 [ 1 2 3 4 ] 和 [ ] 特殊的两段有序。

而对于我们要找的 target ， target 不在的那一段，所有数字可以看做无穷大，这样整个数组就可以看做有序的了，可以用正常的二分法去找 target 了，例如

[ 4 5 6 7 1 2 3] ，如果 target = 5，那么数组可以看做 [ 4 5 6 7 inf inf inf ]。

[ 4 5 6 7 1 2 3] ，如果 target = 2，那么数组可以看做 [ -inf -inf - inf -inf 1 2 3]。

和解法一一样，我们每次只关心 mid 的值，所以 mid 要么就是 nums [ mid ]，要么就是 inf 或者 -inf。

什么时候是 nums [ mid ] 呢？

当 nums [ mid ] 和 target 在同一段里边。

- 怎么判断 nums [ mid ] 和 target 在同一段？

把 nums [ mid ] 和 target 同时与 nums [ 0 ] 比较，如果它俩都大于 nums [ 0 ] 或者都小于 nums [ 0 ]，那么就代表它俩在同一段。例如

[ 4 5 6 7 1 2 3]，如果 target = 5，此时数组看做 [ 4 5 6 7 inf inf inf ]。nums [ mid ] = 7，target > nums [ 0 ]，nums [ mid ] > nums [ 0 ]，所以它们在同一段 nums [ mid ] = 7，不用变化。

- 怎么判断 nums [ mid ] 和 target 不在同一段？

把 nums [ mid ] 和 target 同时与 nums [ 0 ] 比较，如果它俩一个大于 nums [ 0 ] 一个小于 nums [ 0 ]，那么就代表它俩不在同一段。例如

[ 4 5 6 7 1 2 3]，如果 target = 2，此时数组看做 [ - inf - inf - inf - inf 1 2 3]。nums [ mid ] = 7，target < nums [ 0 ]，nums [ mid ] > nums [ 0 ]，一个大于，一个小于，所以它们不在同一段 nums [ mid ] = - inf，变成了负无穷大。

Java code from the link above
```Java code
public int search(int[] nums, int target) {
    int lo = 0, hi = nums.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        int num = nums[mid]; 
        //nums [ mid ] 和 target 在同一段
        if ((nums[mid] < nums[0]) == (target < nums[0])) {
            num = nums[mid];
        //nums [ mid ] 和 target 不在同一段，同时还要考虑下变成 -inf 还是 inf。
        } else {
            num = target < nums[0] ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }

        if (num < target)
            lo = mid + 1;
        else if (num > target)
            hi = mid - 1;
        else
            return mid;
    }
    return -1;
}
```
时间复杂度：O（log（n））。

空间复杂度：O（1）。


### 一个极简的方法

原文链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/ji-jian-solution-by-lukelee/

以二分搜索为基本思路

简要来说：

- nums[0] <= nums[mid]（0 - mid不包含旋转）且nums[0] <= target <= nums[mid] 时 high 向前规约；

- nums[mid] < nums[0]（0 - mid包含旋转），target <= nums[mid] < nums[0] 时向前规约（target 在旋转位置到 mid 之间）

- nums[mid] < nums[0]，nums[mid] < nums[0] <= target 时向前规约（target 在 0 到旋转位置之间）

其他情况向后规约

也就是说nums[mid] < nums[0]，nums[0] > target，target > nums[mid] 三项均为真或者只有一项为真时向后规约。

C++ code from the link above
```C++ code
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int lo = 0, hi = nums.size() - 1;
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if ((nums[0] > target) ^ (nums[0] > nums[mid]) ^ (target > nums[mid]))
                lo = mid + 1;
            else
                hi = mid;
        }
        return lo == hi && nums[lo] == target ? lo : -1;
    }
};
```


### 先使用改写的二分查找找到旋转点，然后判断target在旋转点左右两段升序序列中哪一段，最后使用二分查找在升序序列找target

很麻烦的方法。

原文链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/sou-suo-xuan-zhuan-pai-xu-shu-zu-by-leetcode/
