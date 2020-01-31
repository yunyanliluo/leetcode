# 和为k的子数组

## 
注意到，数组元素为整数，可正可负，

不能使用滑动窗口（滑动窗口需满足：有right和left移动停止条件），事实上，基本就是n^2遍历

时间On^2，空间O1

C++ code
```
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n=nums.size();
        int count=0;
        for(int l=0;l<n;++l) {
            int sum=0;
            for(int r=l;r<n;++r) {
                sum+=nums[r];
                if(sum==k) ++count;
            }
        }
        return count;
    }
};
```
执行用时 :
804 ms
, 在所有 C++ 提交中击败了
22.39%
的用户

内存消耗 :
10 MB
, 在所有 C++ 提交中击败了
77.32%
的用户

## 哈希表 unordered_map
由于sum[j]-sum[i]==k表示i～j的和为k，

使用map记录sum和sum出现的次数

C++ code
```
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n=nums.size();
        unordered_map<int,int> hashmap;
        hashmap[0]=1;
        int sum=0, count=0;
        for(int i=0;i<n;++i) {
            sum+=nums[i];
            if(hashmap.find(sum-k)!=hashmap.end()) {
                count+=hashmap[sum-k];
            }
            if(hashmap.find(sum)!=hashmap.end()) ++hashmap[sum];
            else hashmap[sum]=1;
        }
        return count;
    }
};
```
执行用时 :
40 ms
, 在所有 C++ 提交中击败了
85.64%
的用户

内存消耗 :
14.6 MB
, 在所有 C++ 提交中击败了
30.20%
的用户
