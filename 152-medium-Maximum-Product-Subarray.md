# 乘积最大子序列

## DP 自底向上
对于数组中每一个整数（有正数 负数 0三种可能性），

- 状态表示函数 max[n],min[n]分别表示以nums[n]为最后一个数的子序列最大正乘积和最小负乘积

- 状态转换方程 

  当它为正数时：max[i]=max[i-1]*nums[i], min[i]=min[i-1]*nums[i]

  当它为负数时：min[i]=max[i-1]*nums[i], max[i]=min[i-1]*nums[i]

  当它为零时：min=max=0

- 初值 n=0时，max=min=nums[0]

- 返回值 max(max[0...n-1]

改进

注意到 每一次只需要上一次的max和min，当前temp正负数的处理区别可以通过交换max，min去减少，而且if else只需要用max/min取其中最大/最小

C++ code
```
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n=nums.size();
        int maxVal=1;
        int minVal=1;
        int res=nums[0];
        for(int i=0;i<n;++i) {
            int temp=nums[i];
            if(temp<0) swap(maxVal, minVal);
            maxVal=max(maxVal*temp, temp);
            minVal=min(minVal*temp, temp);
            res=max(res, maxVal);
        }
        return res;
    }
};
```
执行用时 :
4 ms
, 在所有 C++ 提交中击败了
90.83%
的用户

内存消耗 :
9.2 MB
, 在所有 C++ 提交中击败了
18.60%
的用户

引用 执行用时为0ms的案例

C++ code
```
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int res = nums[0];
        int maxP = nums[0];
        int minP = nums[0];

        for (int i = 1; i < nums.size(); i++) {
            int temp = maxP;
            maxP = max(max(maxP * nums[i], nums[i]), minP * nums[i]);
            minP = min(min(temp * nums[i], nums[i]), minP * nums[i]);
            res = max(res, maxP);
        }

        return res;
    }
};
```

原答案
C++ code
```
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n=nums.size();
        int* maxVal=new int[n];
        int* minVal=new int[n];
        int res=nums[0];
        maxVal[0]=res;
        minVal[0]=res;
        for(int i=1;i<n;++i) {
            int temp=nums[i];
            if(temp==0) {
                maxVal[i]=0;
                minVal[i]=0;
            }
            else if(temp>0) {
                if(maxVal[i-1]>0) {
                    maxVal[i]=maxVal[i-1]*temp;
                }
                else maxVal[i]=temp;
                if(minVal[i-1]<0) {
                    minVal[i]=minVal[i-1]*temp;
                }
                else minVal[i]=temp;
            }
            else {
                if(maxVal[i-1]>0) {
                    minVal[i]=maxVal[i-1]*temp;
                }
                else minVal[i]=temp;
                if(minVal[i-1]<0) {
                    maxVal[i]=minVal[i-1]*temp;
                }
                else maxVal[i]=temp;
            }
            res=max(res, maxVal[i]);
        }
        return res;
    }
};
```
执行用时 :
8 ms
, 在所有 C++ 提交中击败了
55.79%
的用户

内存消耗 :
9.4 MB
, 在所有 C++ 提交中击败了
5.04%
的用户
