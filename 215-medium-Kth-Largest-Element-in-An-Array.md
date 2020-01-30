# 数组中第k大元素
返回数组排序之后的第k大元素（重复元素也得计数）

Input: [3,2,3,1,2,4,5,5,6] and k = 4

Output: 4

https://leetcode-cn.com/problems/kth-largest-element-in-an-array/

## 排序
时间O（nlogn），空间O（1）

原地排序

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

## 快速选择（分治思路 借助于快排的partition）
取第一个元素，找到这个元素在排好序的数组里应该位于的位置，这个位置左边所有元素比它小，右边所有元素比它大，

如果这个元素的位置就是想要找的第k大，那么返回，

否则按照同样的方法在左边数组或右边数组中寻找。

改进

加了一行：随机选取pivot元素，时间提升到接近打败百分之百
```
swap(nums[l], nums[rand()%(r-l+1)+l]); //随机选取pivot元素
```
C++ code
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int target=nums.size()-k; //目标元素的由小到大排序下标
        int l=0, r=nums.size()-1;
        while(1) {
            int mid=partition(nums, l, r);
            if(mid==target) return nums[mid];
            else if(mid<target) l=mid+1;
            else r=mid-1;
        }
        return 0;
    }
    int partition(vector<int>& nums, int l, int r) {
        swap(nums[l], nums[rand()%(r-l+1)+l]); //随机选取pivot元素
        int pivot=nums[l];
        while(l<r) {
            while(l<r&&nums[r]>=pivot) --r;
            nums[l]=nums[r];
            while(l<r&&nums[l]<pivot) ++l;
            nums[r]=nums[l];
        }
        nums[l]=pivot;
        return l;
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
98.10%
的用户

内存消耗 :
9.4 MB
, 在所有 C++ 提交中击败了
40.78%
的用户

原答案 

我自己写的双指针partition

C++ code
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int target=nums.size()-k; //目标元素的由小到大排序下标
        int l=0, r=nums.size()-1;
        while(1) {
            int mid=partition(nums, l, r);
            if(mid==target) return nums[mid];
            else if(mid<target) l=mid+1;
            else r=mid-1;
        }
        return 0;
    }
    int partition(vector<int>& nums, int l, int r) {
        int pivot=nums[l];
        int a=l;
        while(l<r) {
            while(l<r&&nums[l]<=pivot) ++l;
            while(l<r&&nums[r]>pivot) --r;
            swap(nums[l], nums[r]);
        }
        if(nums[l]>pivot) --l;
        swap(nums[l], nums[a]);
        return l;
    }
};
```
执行用时 :
32 ms
, 在所有 C++ 提交中击败了
29.05%
的用户

内存消耗 :
9.3 MB
, 在所有 C++ 提交中击败了
41.50%
的用户

partition()的写法二（也是双指针）

C++ code
```
    int partition(vector<int>& nums, int l, int r) {
        int pivot=nums[l];
        while(l<r) {
            while(l<r&&nums[r]>=pivot) --r;
            nums[l]=nums[r];
            while(l<r&&nums[l]<pivot) ++l;
            nums[r]=nums[l];
        }
        nums[l]=pivot;
        return l;
    }
```

单指针partition写法

Java code
```
/**
     * 在数组 nums 的子区间 [left, right] 执行 partition 操作，返回 nums[left] 排序以后应该在的位置
     * 在遍历过程中保持循环不变量的语义
     * 1、[left + 1, j] < nums[left]
     * 2、(j, i] >= nums[left]
     *
     * @param nums
     * @param left
     * @param right
     * @return
     */
    public int partition(int[] nums, int left, int right) {
        // 在区间随机选择一个元素作为标定点
        if (right > left) {
            int randomIndex = left + 1 + random.nextInt(right - left);
            swap(nums, left, randomIndex);
        }

        int pivot = nums[left];
        int j = left;
        for (int i = left + 1; i <= right; i++) {
            if (nums[i] < pivot) {
                // 小于 pivot 的元素都被交换到前面
                j++;
                swap(nums, j, i);
            }
        }
        // 在之前遍历的过程中，满足 [left + 1, j] < pivot，并且 (j, i] >= pivot
        swap(nums, j, left);
        // 交换以后 [left, j - 1] < pivot, nums[j] = pivot, [j + 1, right] >= pivot
        return j;
    }
``` 

时间复杂度：
O
(
N
)
O(N)，这里 
N
N 是数组的长度，理由可以参考本题解下用户 @ZLW 的评论，需要使用主定理进行分析。
空间复杂度：
O
(
1
)
O(1)，原地排序，没有借助额外的辅助空间。

此种partition和复杂度分析取自：

作者：liweiwei1419

链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/partitionfen-er-zhi-zhi-you-xian-dui-lie-java-dai-/

## 堆 使用优先队列
寻找的既是第k大，又是第len-k+1小。

思路1.建立一个最小堆，容量len，弹出堆顶元素len-k次，则返回堆顶（空间复杂度Olen）

思路2.建立一个最大堆，容量len，弹出堆顶元素k-1次，则返回堆顶

思路3.建立一个最小堆(最大堆)，容量k+1（len-k+1），进来k（len-k）个元素以后，每次进来一个出去一个，保持堆元素个数k（len-k+1）个，返回堆顶（空间复杂度降低至Ok）

思路4.结合最大最小堆，在k小时建立最大堆，k大时建立最小堆（优化时间复杂度）

C++11 数据结构 priority_queue的使用

```
#include <functional>
#include <queue>
#include <vector>
#include <iostream>
 
template<typename T> void print_queue(T& q) {
    while(!q.empty()) {
        std::cout << q.top() << " ";
        q.pop();
    }
    std::cout << '\n';
}
 
int main() {
    std::priority_queue<int> q;
 
    for(int n : {1,8,5,6,3,4,0,9,7,2})
        q.push(n);
 
    print_queue(q);
 
    std::priority_queue<int, std::vector<int>, std::greater<int> > q2;
 
    for(int n : {1,8,5,6,3,4,0,9,7,2})
        q2.push(n);
 
    print_queue(q2);
 
    // Using lambda to compare elements.
    auto cmp = [](int left, int right) { return (left ^ 1) < (right ^ 1); };
    std::priority_queue<int, std::vector<int>, decltype(cmp)> q3(cmp);
 
    for(int n : {1,8,5,6,3,4,0,9,7,2})
        q3.push(n);
 
    print_queue(q3);
 
}
```
Output:

9 8 7 6 5 4 3 2 1 0 

0 1 2 3 4 5 6 7 8 9 

8 9 6 7 4 5 2 3 0 1

答案

思路1.最小堆 栈顶弹出len-k次

C++ code
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int len=nums.size();
        priority_queue<int, vector<int>, greater<int>> minheap;
        for(int i=0;i<len;++i) {
            minheap.push(nums[i]);
        }
        for(int i=0;i<len-k;++i) {
            minheap.pop();
        }
        return minheap.top();
    }
};
```
执行用时 :
16 ms
, 在所有 C++ 提交中击败了
54.63%
的用户

内存消耗 :
10 MB
, 在所有 C++ 提交中击败了
9.23%
的用户

思路2.最大堆 栈顶弹出k-1次

C++ code

```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int len=nums.size();
        priority_queue<int> maxheap;
        for(int i=0;i<len;++i) {
            maxheap.push(nums[i]);
        }
        for(int i=0;i<k-1;++i) {
            maxheap.pop();
        }
        return maxheap.top();
    }
};
```
执行用时 :
12 ms
, 在所有 C++ 提交中击败了
82.97%
的用户

内存消耗 :
9.9 MB
, 在所有 C++ 提交中击败了
9.23%
的用户

思路3.最小堆 容量k+1

C++ code
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int len=nums.size();
        priority_queue<int, vector<int>, greater<int>> minheap;
        for(int i=0;i<k;++i) {
            minheap.push(nums[i]);
        }
        for(int i=k;i<len;++i) {
            minheap.push(nums[i]);
            minheap.pop();
        }
        return minheap.top();
    }
};
```
执行用时 :
16 ms
, 在所有 C++ 提交中击败了
54.63%
的用户

内存消耗 :
9.8 MB
, 在所有 C++ 提交中击败了
12.87%
的用户

思路4.结合k与len的大小，建立最大或最小堆

C++ code
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int len=nums.size();
        if(k>len-k) {
            priority_queue<int, vector<int>, greater<int>> minheap;
            for(int i=0;i<k;++i) minheap.push(nums[i]);
            for(int i=k;i<len;++i) {
                minheap.push(nums[i]);
                minheap.pop();
            }
            return minheap.top();
        }
        else {
            priority_queue<int> maxheap;
            for(int i=0;i<len-k+1;++i) maxheap.push(nums[i]);
            for(int i=len-k+1;i<len;++i) {
                maxheap.push(nums[i]);
                maxheap.pop();
            }
            return maxheap.top();
        }
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
98.10%
的用户

内存消耗 :
10 MB
, 在所有 C++ 提交中击败了
8.76%
的用户
