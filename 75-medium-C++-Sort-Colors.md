# *Sort Colors*
Given an array with n objects colored red, white or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

Note: You are not suppose to use the library's sort function for this problem.

Example:

Input: [2,0,2,1,1,0]

Output: [0,0,1,1,2,2]

Follow up:

A rather straight forward solution is a two-pass algorithm using counting sort.

First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
Could you come up with a one-pass algorithm using only constant space?

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/sort-colors

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

## my answer

### 计数0,1,2的个数，然后重写数组

On(2n), O1

C++ code
from https://leetcode-cn.com/submissions/detail/35027103/
``` C++ code
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int a0 = 0;
        int a1 = 0;
        int a2 = 0;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == 0) {
                a0++;
            } else if (nums[i] == 1) {
                a1++;
            } else {
                a2++;
            }
        }
        for (int i = 0; i < nums.size(); ++i) {
            if (a0 > 0) {
                a0--;
                nums[i] = 0;
            } else if (a1 > 0) {
                a1--;
                nums[i] = 1;
            } else if (a2 > 0) {
                a2--;
                nums[i] = 2;
            }
        }
        return;
    }
};
```

## solutions

### 荷兰三色旗问题

设置三指针，i从左向右顺序遍历，p0是0的右边界指针，p2是2的左边界指针，
（p0的左边全是0、p2的右边全是2、并且i遍历过的所有0都在p0左边、i遍历过的所有2都在p2右边）,

设置初值p0=0，p2=n-1，当i遍历到0或者2，和相应指针所指元素交换，然后指针更新：

（1）nums[i]==0: 将0换至开头也就是交换nums[i]和nums[p0]，0的个数加1了于是p0++，i++（假设i==p0，那么没有交换，所指皆0，两指针++；否则肯定交换了，而且nums[i]一定变成了1，1需要保留，i++（走（2）也需要i++））

（实际上，只有刚刚开始有连续的0的时候，i和p0指向相同的元素并且同时++；之后遇到1，则i++、p0不动，p0指向的永远是目前所有的0右边的第一个1）

（2）nums[i]==1: 1不动，i++

（3）nums[i]==2: 将2换至末尾，p2--，不改变i而是继续判断刚交换过来的元素nums[i]

C++ code

On(n), O1

```C++ code
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int p0=0, p2=nums.size()-1, i=0;
        //if(p2<1) return;
        while(i<=p2)
        {
            if(nums[i]==0)
            {
                swap(nums[i++], nums[p0++]);
            }
            else if(nums[i]==2)
            {
                swap(nums[i], nums[p2--]);
            }
            else
                ++i;
        }
        return;
    }
};
```

执行用时 :4 ms, 在所有 cpp 提交中击败了90.12%的用户

内存消耗 :8.6 MB, 在所有 cpp 提交中击败了31.81%的用户
