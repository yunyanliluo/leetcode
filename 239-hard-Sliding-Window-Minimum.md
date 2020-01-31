# 滑动窗口最大值
Example:

Input: nums = [1,3,-1,-3,5,3,6,7], and k = 3

Output: [3,3,5,5,6,7] 

Explanation: 

Window position       -         Max

[1  3  -1] -3  5  3  6  7   -    3

 1 [3  -1  -3] 5  3  6  7   -    3

 1  3 [-1  -3  5] 3  6  7   -    5

 1  3  -1 [-3  5  3] 6  7   -    5

 1  3  -1  -3 [5  3  6] 7   -    6

 1  3  -1  -3  5 [3  6  7]  -    7

Note: 

You may assume k is always valid, 1 ≤ k ≤ input array's size for non-empty array.

Follow up:

Could you solve it in linear time?

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/sliding-window-maximum

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## 双向队列 deque

维护一个递减的双向队列，我们发现当滑动窗口新加入一个元素，那么左侧比它小的元素一定用不着了。

双向队列可以从front和back弹出元素。

每一次新加入一个元素，先从front侧弹出超出长度k窗口边界的元素（如果存在），从back侧弹出比新加入的元素小的元素（维护递减）。

两个阶段：

1.前k个元素加入deque

2.后面所有元素，每加入一个，把左侧超出窗口边界元素弹出

把加入元素所需要弹出的所有处理写入一个函数clean_deq()，包括front和back，所以每一次加入只需调用这个函数

下面是代码

### 更改 - 将maxInd去掉

发现反而变慢了

C++ code
```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        int n=nums.size();
        if(n*k==0) return res;
        if(k==1) return nums;
        for(int i=0;i<k;++i) {
            clean_deq(nums, i, k);
            deq.push_back(i);
        }
        res.push_back(nums[deq[0]]);
        for(int i=k;i<n;++i) {
            clean_deq(nums, i, k);
            deq.push_back(i);
            res.push_back(nums[deq[0]]);
        }
        return res;
    }
    void clean_deq(vector<int>& nums, int i, int k) {
        if(!deq.empty()&&deq[0]==i-k) deq.pop_front();
        while(!deq.empty()&&nums[deq.back()]<=nums[i]) {
            deq.pop_back();
        }
    }
private:
    deque<int> deq;
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
98.20%
的用户

内存消耗 :
13.9 MB
, 在所有 C++ 提交中击败了
15.66%
的用户

### 原答案

C++ code
```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        int maxInd=0;
        int n=nums.size();
        if(n*k==0) return res;
        if(k==1) return nums;
        for(int i=0;i<k;++i) {
            clean_deq(nums, i, k);
            deq.push_back(i);
            if(nums[i]>nums[maxInd]) maxInd=i;
        }
        res.push_back(nums[maxInd]);
        for(int i=k;i<n;++i) {
            clean_deq(nums, i, k);
            deq.push_back(i);
            res.push_back(nums[deq[0]]);
        }
        return res;
    }
    void clean_deq(vector<int>& nums, int i, int k) {
        if(!deq.empty()&&deq[0]==i-k) deq.pop_front();
        while(!deq.empty()&&nums[deq.back()]<=nums[i]) {
            deq.pop_back();
        }
    }
private:
    deque<int> deq;
};
```
执行用时 :
16 ms
, 在所有 C++ 提交中击败了
99.61%
的用户

内存消耗 :
14.3 MB
, 在所有 C++ 提交中击败了
12.24%
的用户

时间复杂度：
O(N)，每个元素被处理两次- 其索引被添加到双向队列中和被双向队列删除。

空间复杂度：
O(N)，输出数组使用了 
O(N−k+1) 空间，双向队列使用了 
O(k)。

复杂度分析来自

作者：LeetCode

链接：https://leetcode-cn.com/problems/sliding-window-maximum/solution/hua-dong-chuang-kou-zui-da-zhi-by-leetcode-3/

### deque的使用

size()

empty()

begin()/rbegin()

end()/rend()

front()

back()

push_front()

pop_front()

push_back()

pop_back()

## 大顶堆
使用priority_queue建立大顶堆。维护一个k元大顶堆，堆顶即是当前最大值。

思路可以借鉴。但C++无法实现，因为不提供删除非堆顶元素的功能函数。

C++ code
```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        int n=nums.size();
        if(n*k==0) return res;
        if(k==1) return nums;
        priority_queue<int> maxheap;
        for(int i=0;i<k;++i) maxheap.push(nums[i]);
        res.push_back(maxheap.top());
        for(int i=k;i<n;++i) {
            //maxheap.pop(nums[i-k]); 这一步实现不了.pop()弹出堆顶。
            maxheap.push(nums[i]);
            res.push_back(maxheap.top());
        }
        return res;
    }
};
```

### priority_queue的使用
push(x)

pop()

top()

empty()

size()


## DP
参考 https://leetcode-cn.com/problems/sliding-window-maximum/solution/hua-dong-chuang-kou-zui-da-zhi-by-leetcode-3/

算法的思想是将输入数组分割成有 k 个元素的块。

若 n % k != 0，则最后一块的元素个数可能更少。

开头元素为 i ，结尾元素为 j 的当前滑动窗口可能在一个块内，也可能在两个块中。

建立数组 left， 其中 left[j] 是从块的开始到下标 j 最大的元素，方向 左->右。

数组 right，其中 right[j] 是从块的结尾到下标 j 最大的元素，方向 右->左。right 数组和 left 除了方向不同以外基本一致。

两数组一起可以提供两个块内元素的全部信息。考虑从下标 i 到下标 j的滑动窗口。 根据定义，right[i] 是左侧块内的最大元素， left[j] 是右侧块内的最大元素。

因此滑动窗口中的最大元素为 max(right[i], left[j])。

三个步骤：

- 从左到右遍历数组，建立数组 left。

- 从右到左遍历数组，建立数组 right。

- 建立输出数组 max(right[i], left[i + k - 1])，其中 i 取值范围为 (0, n - k + 1)。

时间复杂度：
O(N)，我们对长度为 N 的数组处理了 3次。

空间复杂度：
O(N)，用于存储长度为 N 的 left 和 right 数组，以及长度为 N - k + 1的输出数组。

C++ code
```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        int n=nums.size();
        if(n*k==0) return res;
        if(k==1) return nums;
        int* left = new int [n];
        int* right = new int [n];
        left[0]=nums[0];
        right[n-1]=nums[n-1];
        for(int i=1;i<n;++i) {
            if(i%k==0) {
                left[i]=nums[i];
            }
            else {
                left[i]=max(left[i-1], nums[i]);
            }
            int j=n-i-1;
            if((j+1)%k==0) {
                right[j]=nums[j];
            }
            else {
                right[j]=max(right[j+1], nums[j]);
            }
        }
        for(int i=0;i<n-k+1;++i) {
            res.push_back(max(right[i], left[i+k-1]));
        }
        return res;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
98.20%
的用户

内存消耗 :
14.4 MB
, 在所有 C++ 提交中击败了
11.66%
的用户
