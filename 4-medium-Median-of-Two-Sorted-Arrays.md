# 寻找两个有序数组的中位数

## 归并排序的merge部分

先排序，根据合并后数组的元素个数len的奇偶性，返回第len/2个数或第len/2与len/2+1个数的平均值

时间O(m+n)，空间Om+n

## 用指针模拟上面的merge，无需排序

由于根据合并后数组的元素个数len的奇偶性，返回第len/2个数或第len/2与len/2+1个数的平均值（由0起始遍历，坐标最多达到len/2）

可以用right遍历，用left记录right遍历的上一个数字

最后根据len的奇偶，返回right或left与right的平均值

节约时间和空间，时间Om+n，空间O1

C++ code
```
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int n1=nums1.size(), n2=nums2.size();
        if(n1==0) return (n2%2)!=0?nums2[n2/2]:((nums2[n2/2-1]+nums2[n2/2])/2.0);
        if(n2==0) return (n1%2)!=0?nums1[n1/2]:((nums1[n1/2-1]+nums1[n1/2])/2.0);
        int left=-1, right=-1, len=0;
        for(int i=0, j=0;len<=(n1+n2)/2;++len) {
            left=right;
            if(i<n1&&(j>=n2||nums1[i]<nums2[j])) right=nums1[i++];
            else right=nums2[j++];
        }
        if(((n1+n2)&1)==0) return (left+right)/2.0;
        else return right;
    }

};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
74.16%
的用户

内存消耗 :
9.6 MB
, 在所有 C++ 提交中击败了
56.03%
的用户

## 二分查找 第k小
由于题目Olog（m+n）的要求，知道log一定得用二分查找。

参考：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/

思路就是为了找第k小，每一次找两个数组前k/2个数，比较，其中小的那个以及当前数组比它小的前面的数，全都可以过滤掉。这样一次就能过滤k/2个数。

时间Ologm+n，空间O1 。

注意，由于递归全部放在return，这叫尾递归，不占用递归空间。

C++ code
```
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int n1=nums1.size(), n2=nums2.size();
        int left=(n1+n2+1)/2, right=(n1+n2+2)/2; //找第left，right小
        if((n1+n2)%2) return getKth(nums1, 0, n1-1, nums2, 0, n2-1, left); //奇数返回中间一个数
        else return (getKth(nums1, 0, n1-1, nums2, 0, n2-1, left)+getKth(nums1, 0, n1-1, nums2, 0, n2-1, right))/2.0; //偶数返回中间两个数的平均值
    }
    int getKth(vector<int>& nums1, int start1, int end1, vector<int>& nums2, int start2, int end2, int k) {
        int len1=end1-start1+1, len2=end2-start2+1;
        if(len1<=0) return nums2[start2+k-1];
        if(len2<len1) return getKth(nums2, start2, end2, nums1, start1, end1, k); //保证nums1比nums2短
        if(k==1) return min(nums1[start1], nums2[start2]);
        else {
            int i=min(end1, start1+k/2-1), j=min(end2, start2+k/2-1);
            if(nums1[i]<nums2[j]) return getKth(nums1, i+1, end1, nums2, start2, end2, k-i+start1-1);
            else return getKth(nums1, start1, end1, nums2, j+1, end2, k-j+start2-1);
        }
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
74.16%
的用户

内存消耗 :
9.6 MB
, 在所有 C++ 提交中击败了
46.07%
的用户

## 二分查找的另一种方法

参考：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/

比较难想。

时间Olog（min（m,n）)，空间O1

C++ code
```
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m=nums1.size(), n=nums2.size();
        if(m==0) return (n%2)?nums2[n/2]:(nums2[n/2]+nums2[n/2-1])*0.5;
        if(n==0) return (m%2)?nums1[m/2]:(nums1[m/2]+nums1[m/2-1])*0.5;
        if(m>n) return findMedianSortedArrays(nums2, nums1);
        int iMIN=0, iMAX=m;
        while(iMIN<=iMAX) {
            int i=(iMIN+iMAX)/2, j=(m+n+1)/2-i;
            if(j!=0&&i!=m&&nums2[j-1]>nums1[i]) iMIN=i+1; //减半用二分法
            else if(i!=0&&j!=n&&nums1[i-1]>nums2[j]) iMAX=i-1;
            else {
                int leftMAX;
                if(i==0) leftMAX=nums2[j-1];
                else if(j==0) leftMAX=nums1[i-1];
                else leftMAX=max(nums1[i-1], nums2[j-1]);
                if((m+n)%2) return leftMAX;
                int rightMIN;
                if(i==m) rightMIN=nums2[j];
                else if(j==n) rightMIN=nums1[i];
                else rightMIN=min(nums1[i], nums2[j]);
                return (leftMAX+rightMIN)/2.0;
            }
        }
        return 0;
    }
};
```
执行用时 :
20 ms
, 在所有 C++ 提交中击败了
74.16%
的用户

内存消耗 :
9.6 MB
, 在所有 C++ 提交中击败了
43.89%
的用户
