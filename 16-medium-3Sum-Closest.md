# 最接近的三数之和

找出数组中最接近target的三数之和。

## DP
我的第一反应

先让sum=前三个数之和。

然后依次比较第4，5，……n个数，看是否换成目前最优的某一个数，sum更接近target，则替换。

WA

输入:

[1,2,4,8,16,32,64,128]

82

输出

88

stdout

14 1422 28 2852 56 56104 88 8888

预期结果

82

答案是不对的。因为前面的最优解不一定是后面的最优解。不满足局部最优原理。

例如，64出现之前的最优不一定含有2和16.

## 排序+双指针
题解的方法

排序nlogn，双指针n*n，总共n*n。空间O1.

遍历第1，2，3，……，n个数，start和end从最左和最右端开始，计算sum，若sum大了则end--，小了则start++。

0ms

C++
```
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());   
        
        int res = nums[0] + nums[1] + nums[2]; 
        for( int i = 0; i < nums.size(); ++i)
        {
            int b = i+1, e = nums.size()-1;
            while( b < e )
            {
                int sum = nums[b] + nums[e] + nums[i];
                if( sum < target || b == i )
                    ++b;
                else if ( sum > target || e == i )
                    --e;

                res = abs(target - sum) < abs(target - res)  ? sum : res; 
                if(res == target) return res;
            }
        }

        return res;
    }
};
```

我 一模一样的思路

C++
```
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        int n=nums.size();
        int closest=INT_MAX, cloSum=INT_MAX;
        sort(nums.begin(), nums.end());
        for(int i=0;i<n-2;++i) {
            int temp=nums[i];
            int start=i+1, end=n-1;
            while(start<end) {
                int sum=temp+nums[start]+nums[end];cout<<temp<<nums[start]<<nums[end]<<" "<<sum<<endl;
                if(sum==target) return sum;
                int cloTemp=abs(target-sum);
                if(cloTemp<closest) {
                    cloSum=sum; closest=cloTemp;
                }
                if(sum>target) --end;
                else ++start;
            }
        }
        return cloSum;
    }
};
```
执行用时 :
492 ms
, 在所有 C++ 提交中击败了
5.04%
的用户

内存消耗 :
12.3 MB
, 在所有 C++ 提交中击败了
5.43%
的用户
