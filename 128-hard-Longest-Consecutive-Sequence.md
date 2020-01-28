# Longest Consecutive Sequence最长连续序列

找vector中的连续子序列中最长的一个，可以变换顺序。

例如：

输入: [100, 4, 200, 1, 3, 2]

输出: 4

解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4。

https://leetcode-cn.com/problems/longest-consecutive-sequence/

## 哈希集合
将vector倒入一个unordered_set，然后对于vector中的n个数遍历，找它的下一个数是否存在，如果存在则找下下个数是否存在……直到得到目前的连续子序列长度，更新答案。

由于哈希集合的插入和查询都是O（1），因此将vector倒入是O（n），遍历查询是O（n）。总的时间复杂度是O（n）。空间O（n）。

C++ code
```
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int n=nums.size();
        if(n==0) return 0;
        unordered_set<int> st(nums.begin(), nums.end());
        int maxLen=1;
        for(int i=0;i<n;++i) {
            if(st.find(nums[i]-1)==st.end()) {
                int curLen=1;
                int cur=nums[i];
                while(st.find(cur+1)!=st.end()) {
                    ++curLen;
                    ++cur;
                }
                maxLen=max(maxLen, curLen);
            }
        }
        return maxLen;
    }
};
```
执行用时 :
12 ms
, 在所有 C++ 提交中击败了
81.51%
的用户

内存消耗 :
10.3 MB
, 在所有 C++ 提交中击败了
12.65%
的用户

## 先排序，再遍历
原地遍历即可，排序时间O（nlogn），遍历时间O（n），总时间O（nlogn），原地空间O（1）。

C++ code
```
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int n=nums.size();
        if(n==0) return 0;
        int maxLen=1;
        sort(nums.begin(),nums.end());
        for(int i=0;i<n;++i) {
            if(i==0||nums[i]!=nums[i-1]) {
                int curLen=1;
                while(i+1<n&&nums[i+1]-nums[i]<=1) {
                    if(nums[i+1]==nums[i]) ++i;
                    else {
                        ++curLen;
                        ++i;
                    }                   
                }
                maxLen=max(maxLen, curLen);
            }
        }
        return maxLen;
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
97.34%
的用户

内存消耗 :
9.3 MB
, 在所有 C++ 提交中击败了
85.29%
的用户
