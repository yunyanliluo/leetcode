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
